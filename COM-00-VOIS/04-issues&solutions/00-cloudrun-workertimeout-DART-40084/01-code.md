```python 
import os
import re
import json
import base64
import logging
import tempfile
import time
from flask import Flask, request
from datetime import datetime, UTC, timezone
from threading import Lock
from collections import OrderedDict
import gnupg
from google.cloud import secretmanager, storage, bigquery
from google.api_core.exceptions import NotFound
import uuid

# --- Flask App Initialization ---
app = Flask(__name__)
logging.basicConfig(level=logging.INFO)

# --- Configuration from Environment Variables ---
PROJECT_ID = os.environ.get("PROJECT_ID", "vf-grp-mirep-dev-beta")
SECRET_NAME = os.environ.get("SECRET_NAME", "gpg-private-key")
SOURCE_BUCKET_NAME = os.environ.get("SOURCE_BUCKET_NAME", "vfgrp-dh-mireporting-src")
DEST_BUCKET_NAME = os.environ.get("DEST_BUCKET_NAME", "vfgrp-dh-mireporting-arch")
BQ_DATASET = os.environ.get("BQ_DATASET", "vfgrp_dh_lake_edw_mipyramid_nonlive_s")
SECRET_VERSION = os.environ.get("SECRET_VERSION", "latest")
SEC_PROJECT_ID = os.environ.get("SEC_PROJECT_ID", "vf-grp-mirep-dev-beta")
BQ_PROJECT_ID = os.environ.get("BQ_PROJECT_ID", "vf-grp-mirep-dev-beta")   # ✅ BigQuery project explicitly defined
STATUS_TABLE = "control_table_for_status"                                  # Status table to maintain status of files processed/failed in BigQuery.  
BQ_DATASET_REF = bigquery.DatasetReference(BQ_PROJECT_ID, BQ_DATASET)

# --- File Matching Patterns for GPG Files ---
GPG_FILENAME_PATTERNS = [
    "Fact_Base_Transform_LIB_",
    "Fact_Base_Transform_RF_",
    "Fact_Base_Transform_LIB_REP_Control_",  
    "Fact_Base_Transform_RF_Control_"        
]

# --- BigQuery Schemas ---
common_schema = [
    bigquery.SchemaField("Key_Reverse_Map", "STRING"),
    bigquery.SchemaField("CUSTOM1_CD", "STRING"),
    bigquery.SchemaField("TARIFF_SEGMENT_CD", "STRING"),
    bigquery.SchemaField("File_Name", "STRING"),
    bigquery.SchemaField("CALENDAR_MONTH_ID", "STRING"),
    bigquery.SchemaField("MEASURE_VALUE", "STRING"),
    bigquery.SchemaField("NUMERATOR_VALUE", "STRING"),
    bigquery.SchemaField("DENOMINATOR_VALUE", "STRING"),
    bigquery.SchemaField("ACCOUNT_TYPE_INDICATOR", "STRING"),
    bigquery.SchemaField("CHART_OF_ACCNT_CD", "STRING"),
    bigquery.SchemaField("CONSOLIDATION_CD", "STRING"),
    bigquery.SchemaField("CONSOLIDATION_CD_O", "STRING"),
    bigquery.SchemaField("LEGAL_ENTITY_CD", "STRING"),
    bigquery.SchemaField("SCENARIO_CD", "STRING"),
    bigquery.SchemaField("SCENARIO_CD_O", "STRING"),
    bigquery.SchemaField("ICP_ENTITY_CD", "STRING"),
    bigquery.SchemaField("MEASURE_CD", "STRING"),
    bigquery.SchemaField("VIEW_CD", "STRING"),
    bigquery.SchemaField("FACT_SYSTEM_FLAG", "STRING"),
    bigquery.SchemaField("CUSTOM3_CD", "STRING"),
    bigquery.SchemaField("ACCOUNT_TYPE", "STRING"),
    bigquery.SchemaField("VERSION", "STRING"),
    bigquery.SchemaField("KPI_KEY", "STRING"),
    bigquery.SchemaField("RUN_ID", "STRING"),
    bigquery.SchemaField("ORGANIC_FLAG", "STRING"),
    bigquery.SchemaField("YEAR", "STRING"),
    bigquery.SchemaField("KEY_DASHBOARD", "STRING"),
]

forecast_schema = [
    bigquery.SchemaField("File_Name", "STRING"),
    bigquery.SchemaField("CALENDAR_MONTH_ID", "STRING"),
    bigquery.SchemaField("MEASURE_VALUE", "STRING"),
    bigquery.SchemaField("NUMERATOR_VALUE", "STRING"),
    bigquery.SchemaField("DENOMINATOR_VALUE", "STRING"),
    bigquery.SchemaField("CHART_OF_ACCNT_CD", "STRING"),
    bigquery.SchemaField("TARIFF_SEGMENT_CD", "STRING"),
    bigquery.SchemaField("CUSTOM1_CD", "STRING"),
    bigquery.SchemaField("CONSOLIDATION_CD", "STRING"),
    bigquery.SchemaField("LEGAL_ENTITY_CD", "STRING"),
    bigquery.SchemaField("SCENARIO_CD", "STRING"),
    bigquery.SchemaField("SCENARIO_CD_O", "STRING"),
    bigquery.SchemaField("ICP_ENTITY_CD", "STRING"),
    bigquery.SchemaField("MEASURE_CD", "STRING"),
    bigquery.SchemaField("VIEW_CD", "STRING"),
    bigquery.SchemaField("FACT_SYSTEM_FLAG", "STRING"),
    bigquery.SchemaField("CUSTOM3_CD", "STRING"),
    bigquery.SchemaField("ACCOUNT_TYPE", "STRING"),
    bigquery.SchemaField("VERSION", "STRING"),
    bigquery.SchemaField("KPI_KEY", "STRING"),
    bigquery.SchemaField("RUN_ID", "STRING"),
    bigquery.SchemaField("KEY_DASHBOARD", "STRING"),
]

# Schema for the new M_MI_Control table
control_schema = [
    bigquery.SchemaField("RUN_ID", "STRING"),
    bigquery.SchemaField("AREA", "STRING"),     
    bigquery.SchemaField("AREA_FLAG", "STRING"), 
    bigquery.SchemaField("SELF_SERVE_FLAG", "STRING"),
]

# Schema for Status Table to maintain status/ processing state of different files
status_table_schema = [
    bigquery.SchemaField("table_name", "STRING"),
    bigquery.SchemaField("schema", "STRING"),     
    bigquery.SchemaField("inserted_at", "TIMESTAMP"), 
    bigquery.SchemaField("status", "STRING"),    
]

extra_fields = [
    bigquery.SchemaField("INSERTED_AT", "TIMESTAMP"),
    bigquery.SchemaField("SOURCE_FILE", "STRING"),
]

# --- GCP Clients with Explicit Project for BigQuery ---
def get_secret_client(): 
    return secretmanager.SecretManagerServiceClient()
    
def get_storage_client(): 
    return storage.Client()

def get_bq_client(): 
    return bigquery.Client(project=BQ_PROJECT_ID)  # ✅ BigQuery client initialized using BQ_PROJECT_ID

# --- Internal State ---
lock = Lock()
processed_files = OrderedDict()
MAX_PROCESSED = 100

# --- Secret Key Access ---
def get_private_key(secret_name):
    path = f"projects/{SEC_PROJECT_ID}/secrets/{secret_name}/versions/{SECRET_VERSION}"
    response = get_secret_client().access_secret_version(request={"name": path})
    logging.info(f"✅ Successfully retrieved GPG private key path from Secret Manager (path: {path})")
    return response.payload.data.decode("UTF-8")
    
# --- GPG Decryption ---
def decrypt_gpg_file(blob, private_key):
    gpg = gnupg.GPG()
    logging.info("🔐 Importing GPG private key...")
    gpg.import_keys(private_key)
    temp_encrypted = f"/tmp/{os.path.basename(blob.name)}"
    blob.download_to_filename(temp_encrypted)
    temp_decrypted = temp_encrypted.replace(".gpg", "")
    with open(temp_encrypted, "rb") as f:
        result = gpg.decrypt_file(f, output=temp_decrypted)
    os.remove(temp_encrypted)
    if not result.ok:
        raise ValueError(f"❌ Decryption failed for {blob.name}: {result.status}")
    logging.info(f"✅ Decryption successful. Decrypted file path: {temp_decrypted}")
    return temp_decrypted

# --- Upload Decrypted File ---
def upload_csv(file_path, dest_name):
    client = storage.Client()
    bucket = client.bucket(DEST_BUCKET_NAME)
    blob = bucket.blob(dest_name)

    # Set smaller chunk size (e.g., 5 MB) for resumable upload
    blob.chunk_size = 20 * 1024 * 1024  # 5 MB
    # Open file and upload with increased timeout
    with open(file_path, "rb") as file_obj:
        blob.upload_from_file(file_obj, timeout=900)  # 600 seconds timeout

    logging.info(f"✅ Uploaded to destination bucket: {DEST_BUCKET_NAME}/{dest_name}")    

# --- Delete Temporary CSV ---
def delete_csv(dest_name):
    bucket = get_storage_client().bucket(DEST_BUCKET_NAME)
    blob = bucket.blob(dest_name)
    blob.delete()
    logging.info(f"✅ Deleted CSV from destination bucket: {DEST_BUCKET_NAME}/{dest_name}")

# --- Archive Original GPG File ---
def archive_blob(blob):
    archive_name = f"archive/{blob.name.split('/')[-1]}"
    bucket = get_storage_client().bucket(DEST_BUCKET_NAME)
    bucket.copy_blob(blob, bucket, archive_name)
    blob.delete()
    logging.info(f"✅ Archived GPG file to {archive_name}")

# --- Create Control Status Table If Not exist ---
def create_status_table_if_not_exists():
    client = get_bq_client()
    table_id = BQ_DATASET_REF.table(STATUS_TABLE)
    try:
        client.get_table(table_id)
        logging.info(f"✅ Control table {STATUS_TABLE} already exists")
        
    except NotFound:
        logging.info(f"⚠ Control table {STATUS_TABLE} not found. Creating...")
        table = bigquery.Table(table_id, schema=status_table_schema)
        client.create_table(table)
        logging.info(f"✅ Control table {STATUS_TABLE} created successfully")   

# --- Function to Insert First Status In Control Status Table for any files ("in_progress"/"waiting") ---        
def insert_status(table_name, schema_name, status):
    #create_status_table_if_not_exists()
    client = get_bq_client()
    query = f"""
    insert into `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}` SELECT @table_name, @schema_name, current_timestamp(), @status 
    """
    job_config = bigquery.QueryJobConfig(query_parameters=[
        bigquery.ScalarQueryParameter("table_name", "STRING", table_name),
        bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name),
        bigquery.ScalarQueryParameter("status", "STRING", status),
    ])
    client.query(query, job_config=job_config).result()
    logging.info(f"✅ Status upserted: {table_name} -> {status}")          

# --- Function to Update Status in Control Status Table once file is processed("completed"/"Failed"/"Timeout") ---
def upsert_status(table_name, schema_name, status):
    #create_status_table_if_not_exists()
    client = get_bq_client()
    query = f"""UPDATE `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}` T SET T.status = @status  WHERE T.table_name = @table_name AND T.schema = @schema_name"""
    job_config = bigquery.QueryJobConfig(query_parameters=[
        bigquery.ScalarQueryParameter("table_name", "STRING", table_name),
        bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name),
        bigquery.ScalarQueryParameter("status", "STRING", status),
    ])
    client.query(query, job_config=job_config).result()
    logging.info(f"✅ Status upserted: {table_name} -> {status}")   
    
# --- Get Latest Status Before Updating Rows In Control Status Table ---    
def get_latest_status(table_name, schema_name):
    client = get_bq_client()
    query = f"""
    SELECT status FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}`
    WHERE table_name=@table_name AND schema=@schema_name
    ORDER BY inserted_at DESC LIMIT 1
    """
    job_config = bigquery.QueryJobConfig(query_parameters=[
        bigquery.ScalarQueryParameter("table_name", "STRING", table_name),
        bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name)
    ])
    result = list(client.query(query, job_config=job_config).result())
    return result[0].status if result else None    
    
# --- Delete 'in_progress' rows if any, so that new row can get inserted for other files ---
def delete_in_progress_for_schema(schema_name, table_name):
    client = get_bq_client()
    query = f"""
    DELETE FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}`
    WHERE schema = @schema_name AND table_name = @table_name AND status = 'in_progress'
    """
    job_config = bigquery.QueryJobConfig(query_parameters=[
        bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name.strip().lower()),
        bigquery.ScalarQueryParameter("table_name", "STRING", table_name.strip().lower())
    ])
    client.query(query, job_config=job_config).result()
    logging.info(f"✅ Deleted 'in_progress' rows for table {table_name} and schema {schema_name}")

# --- Delete data of control status table after every 2-3 Months to optimize cost ---
def delete_control_table_rows(retention_days):
    create_status_table_if_not_exists() 
    client = get_bq_client()
    query = f"""
    DELETE FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}`
    WHERE inserted_at < TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL @retention_days DAY)
    """
    job_config = bigquery.QueryJobConfig(query_parameters=[
        bigquery.ScalarQueryParameter("retention_days", "INT64", retention_days)
    ])
    client.query(query, job_config=job_config).result()
    logging.info(f"✅ Deleted previous days rows from control table")                 

#--- Check if any file with the same schema is "in_progress" (used to acquire lock). ---
def is_schema_locked(schema_name, table_name):
    client = get_bq_client()
    table_fq = f"{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}"
    query = f"""
    SELECT COUNT(1) as cnt
    FROM {table_fq}
    WHERE schema = @schema_name AND table_name = @table_name AND status IN ('in_progress','completed')
    """
    job_config = bigquery.QueryJobConfig(
        query_parameters=[
            bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name.strip().lower()),
            bigquery.ScalarQueryParameter("table_name", "STRING", table_name.strip().lower())
        ]
    )
    result = list(client.query(query, job_config=job_config).result())
    count = result[0].cnt if result else 0
    logging.info(f"{count} in_progress records for table {table_name} and schema {schema_name}")
    return count

# --- Check if any control file is in "waiting", used in wait and acquired to stop control files from processing before data files. ---    
def is_waiting(schema_name, table_name):
    client = get_bq_client()
    table_fq = f"{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}"
    query = f"""
    SELECT COUNT(1) as cnt
    FROM {table_fq}
    WHERE table_name = @table_name and status = 'waiting' 
    """
    job_config = bigquery.QueryJobConfig(
        query_parameters=[
            bigquery.ScalarQueryParameter("schema_name", "STRING", schema_name.strip().lower()),
            bigquery.ScalarQueryParameter("table_name", "STRING", table_name.strip().lower())
        ]
    )
    result = list(client.query(query, job_config=job_config).result())
    count = result[0].cnt if result else 0
    logging.info(f"{count} waiting records for table {table_name} and schema {schema_name}")
    return count    
    
# --- This function will make sure that at a time no more than 1 files should be 'in_progress' in control status table so that it won't exhaust quota limit ---
def max_no_of_simultaneous_files():
    client = get_bq_client()
    table_fq = f"{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}"
    
    query = f"""SELECT COUNT(1) as cnt FROM {table_fq} WHERE status = 'in_progress'"""
    try:
        result = list(client.query(query).result())
        count = result[0].cnt if result else 0
        logging.info(f"Total in_progress files: {count}")
        return count
    except Exception as e:
        logging.error(f"Error querying in_progress count: {e}")
        return float('inf')  # Prevent lock acquisition if query fails    

# ---Acquire lock with retries, return True if acquired, False if timed out. --
# --- Max attempts given for retry is 70 and wait_sec is 180. Also Max "in_progress" should be 1 in Control status table. ---
def wait_and_acquire_lock(schema_name, table_name, max_attempts=70, wait_seconds=180, max_in_progress=1):   
    create_status_table_if_not_exists() 
    schema_name = schema_name.strip().lower()
    table_name = table_name.strip().lower()

    for attempt in range(max_attempts):
        latest_status = get_latest_status(table_name, schema_name)
        if latest_status in ["completed", "failed"]:
           logging.info(f"Skipping {table_name}: already {latest_status}")
           return latest_status  
           
        
        # --- control files check ---
        waiting_count = is_waiting(schema_name, table_name)
        if table_name.startswith("fact_base_transform_rf_control") or table_name.startswith("fact_base_transform_lib_rep_control"):
            if waiting_count == 0:
               insert_status(table_name, schema_name, "waiting") # --Control File status should be Set to "waiting" so that other data file process first.
               return "waiting"
            else:
               return "waiting"        
        
        # --- Other files check ---
        count = is_schema_locked(schema_name, table_name)
        in_progress_count = max_no_of_simultaneous_files()
        if count == 0 and in_progress_count < max_in_progress:
            insert_status(table_name, schema_name, "in_progress") # --If Data Files Status should be updated as "in_progress"
            logging.info(f"Acquired lock for {table_name} on schema {schema_name}")
            return "in_progress"   
                   
        logging.info(f"Schema {schema_name} busy or quota full for {table_name}")
        logging.info(f"In-progress count: {in_progress_count}. Attempt {attempt+1}/{max_attempts}.")
        logging.info(f"Waiting {wait_seconds}s...")
        time.sleep(wait_seconds)

    upsert_status(table_name, schema_name, "timeout") # -- In case of timeout after retries/max_attempt status should be updated as "timeout"
    return "timeout"

# --- Load Data to BigQuery ---
def load_csv_to_bigquery(file_path, table_name, schema, original_name):
    # --- Session ID and Initial Logging ---
    session_id = f"{table_name}_{int(time.time())}"

    # --- BigQuery Load ---
    final_schema = schema + extra_fields
    inserted_at = datetime.now(UTC).replace(microsecond=0).isoformat()

    temp_gcs_path = f"temp/{os.path.basename(file_path)}.final.csv"
    temp_local_path = f"/tmp/{os.path.basename(file_path)}.final.csv"

    with open(file_path, "r") as src, open(temp_local_path, "w") as dst:
        header = src.readline().strip()
        dst.write(f"{header},inserted_at,source_file\n")
        for line in src:
            dst.write(f"{line.strip()},{inserted_at},{original_name}\n")

    upload_csv(temp_local_path, temp_gcs_path)
    bucket = get_storage_client().bucket(DEST_BUCKET_NAME)
    blob = bucket.blob(temp_gcs_path)
    logging.info(f"[Session: {session_id}] ✅ Uploaded temp CSV to GCS: {temp_gcs_path}")

    table_id = f"{BQ_PROJECT_ID}.{BQ_DATASET}.{table_name}"
    job_config = bigquery.LoadJobConfig(
        schema=final_schema,
        skip_leading_rows=1,
        write_disposition="WRITE_TRUNCATE",
        source_format=bigquery.SourceFormat.CSV
    )
    client = get_bq_client()
    job = client.load_table_from_uri(f"gs://{DEST_BUCKET_NAME}/{temp_gcs_path}", table_id, job_config=job_config)
    job.result()
    logging.info(f"[Session: {session_id}] ✅ Loaded data into BigQuery table: {table_id}")
    blob.delete() 
    os.remove(temp_local_path)
    logging.info(f"Deleted temp CSV file: {temp_local_path}")

    return table_id

# -- Function to check schema of a file based on file pattern, used to acquire lock --
def check_schema(table_id):
    table_name_only = table_id.split(',')[-1]
    if table_name_only.startswith("fact_base_transform_rf_control") or table_name_only.startswith("fact_base_transform_lib_rep_control"):
        schema_name = "control_schema"
    elif table_name_only.startswith("fact_base_transform_rf"):
        schema_name = "forecast_schema"
    else:
        schema_name = "common_schema"
    logging.info(f"Table {table_id} belongs to schema: {schema_name}")
    return schema_name   

# --- Drop Staging table created during load_csv_to_bigquery function after SP execution is completed and status in control status table is "completed" for that files. ---
def drop_table_if_exists(table_name):
    client = get_bq_client()
    table_id = f"{BQ_PROJECT_ID}.{BQ_DATASET}.{table_name}"
    try:
        client.delete_table(table_id, not_found_ok=True)
        logging.info(f"Dropped Bigquery table: {table_id}")
    except Exception as e:
        logging.exception(f"Failed to drop table {table_id}: {e}")     
               
# --- Get SP based on file pattern ---        
def get_stored_procedure_for_file(file_name):
    """
    Returns the stored procedure name based on file name pattern.
    """
    file_name_lower = file_name.lower()

    if "fact_base_transform_lib_rep_control" in file_name_lower:
        return "sp_m_mi_control_lib_rep_test"
    elif "fact_base_transform_rf_control" in file_name_lower:
        return "sp_m_mi_control_rf"
    elif "fact_base_transform_lib_" in file_name_lower:
        return "sp_fact_lib_rep_2"
    elif "fact_base_transform_rf_" in file_name_lower:
        return "sp_fact_rf"
    else:
        raise ValueError(f"No matching stored procedure for file: {file_name}")

# --- Execute SP for each staging table passed in argument ---       
def execute_stored_procedure(file_name):
    sp_name = get_stored_procedure_for_file(file_name)
    client = get_bq_client()
    query = f"CALL `{BQ_PROJECT_ID}.{BQ_DATASET}.{sp_name}`(@table_name)"
    job_config = bigquery.QueryJobConfig(
        query_parameters=[
            bigquery.ScalarQueryParameter("table_name", "STRING", file_name)
        ]
    )
    logging.info(f"▶ Executing SP: {sp_name} for file: {file_name}")
    client.query(query, job_config=job_config).result()
    logging.info(f"✅ SP executed successfully: {sp_name}")
    
# --- Function created to synchronize processing of control file. Control Files should execute only when status of control file is "waiting" and any other files should not be in "in_progress" ---
def check_status_of_lib_rep_files():
    client = get_bq_client()
    query = f"""
    SELECT CASE WHEN
      SUM(CASE WHEN schema = 'control_schema' AND status = 'waiting' THEN 1 ELSE 0 END) = 1
      AND SUM(CASE WHEN schema = 'common_schema' AND status = 'in_progress' THEN 1 ELSE 0 END) = 0
    THEN TRUE ELSE FALSE
  END AS is_valid
    FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}`
    """
    result = list(client.query(query).result())
    return bool(result[0].is_valid) if result else False  

# --- Function to get status and control file details from control table ---
def get_ready_control_files():
    """
    Returns a list of control file names (table_name) where:
      - schema is 'control_schema'
      - status is 'waiting'
      - all related common_schema files are 'completed'
    """
    client = get_bq_client()
    query = f"""
    WITH control_candidates AS (
      SELECT table_name
      FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}`
      WHERE schema = 'control_schema' AND status = 'waiting'
    )
    SELECT c.table_name AS control_table
    FROM control_candidates c
    WHERE NOT EXISTS (
      SELECT 1
      FROM `{BQ_PROJECT_ID}.{BQ_DATASET}.{STATUS_TABLE}` s
      WHERE s.schema = 'common_schema'
        AND s.status != 'completed'
        AND REGEXP_EXTRACT(s.table_name, r'(.*)_control') = REGEXP_EXTRACT(c.table_name, r'(.*)_control')
    )
    """
    result = list(client.query(query).result())
    return [row.control_table for row in result]    
        

# --- Main Webhook Endpoint ---
@app.route("/", methods=["POST"])
def process_pubsub():
    try:
        envelope = request.get_json()
        if not envelope or "message" not in envelope:
            logging.error(f"❌ Invalid Pub/Sub message structure: {envelope}")
            return "❌ Invalid Pub/Sub message", 400
        
        message = envelope["message"]
        if "data" not in message:
            logging.error(f"❌ Pub/Sub message missing 'data' field: {message}")
            return "❌ No data found in message", 400

        payload = json.loads(base64.b64decode(message["data"]).decode("utf-8"))
        gcs_event = payload.get("name", "")
        if not gcs_event.endswith(".gpg"):
            logging.warning(f"❌ Skipped file: {gcs_event} — not a .gpg file.")
            return "❌ Not a .GPG file. Please upload a valid .gpg file ", 200

        with lock:
            normalized_event = gcs_event.lower()
            if normalized_event in processed_files:
                logging.info(f"🔁 Duplicate file detected: {gcs_event}. Skipping reprocessing.")
                return "⏳ Already processed. Please re-upload the file after 15 minutes.", 200
            processed_files[normalized_event] = datetime.now(UTC).isoformat()
            if len(processed_files) > MAX_PROCESSED:
                processed_files.popitem(last=False)

        if not any(p.lower() in gcs_event.lower() for p in GPG_FILENAME_PATTERNS):
            logging.warning(f"🚫 File pattern not matched: {gcs_event}. Expected one of {GPG_FILENAME_PATTERNS}")
            return "🚫 File pattern not matched. Please upload a correctly named .gpg file", 200

        # Choose correct schema/table name
        #base_name = re.sub(r'_(\d{14})(?=(_\d+)?\.csv(?:\.gpg)?)', '', gcs_event, flags=re.IGNORECASE).replace(".csv.gpg", "").lower() # added file part as suffix
        
        base_name = re.sub(r'\.csv\.gpg$', '', gcs_event, flags=re.IGNORECASE).lower()
        if base_name.startswith("Fact_Base_Transform_RF_Control") or base_name.startswith("Fact_Base_Transform_LIB_REP_Control"):
            schema = control_schema
        elif base_name.startswith("Fact_Base_Transform_RF"):
            schema = forecast_schema
        else:
            schema = common_schema
            
        schema_name = check_schema(base_name)
        
        # -- Introduced random delay1 to handle concurrency and race condition using threading and delaying instance until processing is completed for previous instance --- 
        delay_time = int(uuid.uuid4()) % 100
        logging.info(f"Delay time for file {gcs_event} before sleep: {delay_time} at {datetime.now(UTC)}")

        # While loop to ensure delay >= 50
        while delay_time < 50:
        # Generate a new random delay
            delay_time = int(uuid.uuid4()) % 100
        
        time.sleep(delay_time)
        # Deleting Control Table after retention_mins
        delete_control_table_rows(retention_days=30)
        
        blob = get_storage_client().bucket(SOURCE_BUCKET_NAME).blob(gcs_event)
        
        if blob.exists():
            
            #Attempt to acquire schema lock(with retries)              
            locking_status = wait_and_acquire_lock(schema_name, base_name)
            logging.info(f"Locking status is: {locking_status}")
                
            if locking_status == "in_progress":                 #If Status for any file is "in_progress" then only further steps will be executed.
               logging.info(f"🔄 Processing file: {gcs_event}")
               private_key = get_private_key(SECRET_NAME)

               decrypted_file = decrypt_gpg_file(blob, private_key)
               try:
                 #Load data into staging table
                 load_csv_to_bigquery(decrypted_file, base_name, schema, gcs_event)    
                 execute_stored_procedure(base_name)
                 upsert_status(base_name, schema_name, "completed")  

                 #--- Here processing of data files is completed, now logic for processing control files begins --- 
                 if check_status_of_lib_rep_files(): 
                     time.sleep(180) #wait for 3 mins before prcessing control file to be sure that all data files are processed.
                     check_status = check_status_of_lib_rep_files() # Check if status of any control files is "waiting"
                     logging.info(f"Check status: {check_status}")
                     if check_status:  # True means validation passed
                        ready_control_files = get_ready_control_files() #Getting latest details from control status table for control files
                        logging.info(f"Ready control files to process: {ready_control_files}")
                        bucket2 = get_storage_client().bucket(SOURCE_BUCKET_NAME)
                        all_blobs = list(bucket2.list_blobs())
                        for control_file in ready_control_files:
                            expected_name = control_file + ".csv.gpg"    #Make sure file name in bucket and control status table are in same format. 
                            matched_blob = None
                            for cntl_blob in all_blobs:
                                if cntl_blob.name.lower() == expected_name.lower(): 
                                    matched_blob = cntl_blob
                                    break
                            if matched_blob:                             #If pattern matched, continue to process control file
                                control_decrypted_file = decrypt_gpg_file(matched_blob, private_key)
                                load_csv_to_bigquery(control_decrypted_file, control_file, control_schema, control_file)
                                execute_stored_procedure(control_file)
                                upsert_status(control_file, "control_schema", "completed")
                                archive_blob(matched_blob)
                                os.remove(control_decrypted_file)
                                drop_table_if_exists(control_file)
                            else:
                                logging.error(f"Control file {expected_name} does not exist in bucket!")
                     else:
                        logging.info("Validation failed. Control file or other files are not in expected status.")

               except Exception as e:
                logging.exception(f"Error: {str(e)}", 500)
                
                #If Status is not completed and any issue/error occur during processing, status should be updated as "failed" in control status table. 
                latest_status = get_latest_status(base_name, schema_name) 
                if latest_status != "completed":
                   upsert_status(base_name, schema_name, "failed")
                else:
                 logging.info(f"Skipping failed status update because {base_name} is already completed")
                 
                try:
                   os.remove(decrypted_file)
                except Exception:
                   pass
                return f"Error: {str(e)}", 500

               archive_blob(blob)
               os.remove(decrypted_file)
               drop_table_if_exists(base_name)
               delete_in_progress_for_schema(schema_name, base_name)   #check for already processed file.
               logging.info(f"Finished processing for {gcs_event}")
               return "OK", 200
            else:
             logging.info(f"Error Processing File: {gcs_event}")       


#if waiting
#same process             
       
    except Exception as e:
        logging.exception("Error processing message")
        return f"Error: {str(e)}", 500

# --- Entry Point ---
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(os.environ.get("PORT", 8080)))
```

- Dockfile 
```Dockerfile 
FROM python:3.12-alpine3.20
 
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1
 
RUN apk update && apk add --no-cache \
    gcc \
    g++ \
    musl-dev \
    libffi-dev \
    openssl-dev \
    gnupg \
    make \
    bash \
    shadow  # Needed to add user with non-root privileges
 
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
 
WORKDIR /app
 
COPY requirements.txt .
 
RUN pip install --upgrade pip setuptools && \
    pip install --no-cache-dir -r requirements.txt
 
COPY . .
 
RUN chown -R appuser:appgroup /app
 
USER appuser
 
EXPOSE 8080
 
CMD ["gunicorn", "-b", "0.0.0.0:8080", "--timeout", "3600", "main:app"]
```