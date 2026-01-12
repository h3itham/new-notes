	- Source [Course Link](	https://www.youtube.com/playlist?list=PLtYQVzxMLm3k0TYSJtxiCdf2cReEdxLha)
### General 
- What is the difference between `Data warehouse` & `Data lake`? (<span style="color:rgb(255, 0, 0)">Data type, UseCase, Example</span>)
	- Data warehouse 
		1. Structured Data
		2. Business Intelligence & Reporting. 
		3. Google BigQuery, Amazon RedShift 
	- Data lake 
		1. Structured, SimiStructured & UnStructured Datat
		2. Machine Learngin & analytics 
		3. Amazon Athena, Azure Data lake 
- <span style="color:rgb(255, 0, 0)">What is the difference between normalized data and denormalized data? </span>
	- Normalized: data is split into multiple related tables to reduce duplication and storage capacity. 
	- denormalized: data is combined into fewer tables, even if it causes duplication, to speed up queries.
	- Example: (E-Commerce app)
		Normalized: Customers, Orders, Products are tables 
		DeNormalized: All tables in one table Orders table 
#### Storage 
[[GCP#Storage]]
Amazing image 
![[gcpstorage.png]]

#### Streaming Data 
- Include 
	1. Ingest the data with `pub/sub`. 
	2. Process the data with `Dataflow`. 
	3. visualize the result with `Data studio` and `Looker`. 
		![[streamming.png]]
- What is the different between Data streaming and batch processing? 
	![[streamvsbatch.png]]
- What is the big data challenges? (<span style="color:rgb(0, 255, 0)">Four Vs</span>) 
	- *Volume*: Huge amount of data to store and manage. 
	- *Velocity*: Data coming in very fast and needs quick processing.
	- *Variety*: Data comes in many formats (text, images, videos, logs, etc.).
	- *Veracity*: Data may be inaccurate, incomplete or unreliable.

#### Message Oriented Architecture. 
- What is the meaning of Data ingestion? 
	- Collecting Data from different resources into one place
		![[ingestion.png | 250]]
- Complete Data Pipeline 
		![[datapipeline.png]]
	- Data ingestion 
		- use pub/sub to collect real-time data from different resources 
	- Data Processing (ETL)
		- Extract data from pub/sub 
		- Transform data (Clean, Format, enrich)
		- Load data to storage 
	- Storage 
		- Store the processed data in a **Data Warehouse** (e.g., BigQuery) for analysis and reporting

#### BigQuery (Storage & analytics)
- What is bigQuery? 
	1. Fully manage serverless data warehouse. 
	2. Scalable and distributed analitical Engine. 
	3. Designed for fast SQL analytics on large Datasets
- bigQuery Features 
	1. Serverless 
	2. Petabyte Scale 
	3. Realtime analytics 
	4. Hight availability 
	5. Data encryption & Security and more.... 
- BigQuery Architecture 
	![[bigqueryarchitecure.png]]
	This diagram show that there are three component of `BigQuery` architecture
	1. Storage 
	2. Shuffle tier (Network / Memeory)
	3. Compute 
	
- BigQuery Storage 
	- Managed columnar storage optimized for analytics.
		![[columner.png]]
	- Columnar is faster because the only scan needed columns 
#### Cloud Composer (Airflow) ![[apacheairflow.png | 50]]
- Fully managed workflow orchestration service. 
- Based on apache airflow(Open Source)
- Create, schedule, monitor, and manage workflow pipelines (Orchestrator)
- Integrate with different GCP Services. 
- Airflow component 
	1. DAG 
		- Waht is <span style="color:rgb(255, 0, 0)">Dag</span>? And why it called that?. 
			- Collection of tasks that you want to schedule and run.  
			- Dag stands for <span style="color:rgb(255, 0, 0)">D</span>irected <span style="color:rgb(255, 0, 0)">A</span>cyclic <span style="color:rgb(255, 0, 0)">G</span>raph 
				1. **Directed** → Each task has a **clear direction** (e.g., Task A → Task B).
				2. **Acyclic** → No cycles allowed (you can't go back to a previous task).
				3. **Graph** → The workflow is structured as a graph of **nodes (tasks)** and **edges (dependencies)**.
	2. Tasks
		- Basic Unit of execution in airflow. 
	3. RelationShips 
		- Define relationships between tasks 
			![[dagcomponent.png]]
- Airflow Architecture 
	![[airflow-arch.png]]
- Infrastructure that composer create 
	- GKE cluster 
		- Webserver Pod 
		- Scheduler Pod 
		- Workers Fleet of Pods for every task. 
	- Cloud SQL Instance 
		- For MetadataDB 
	- Cloud Logging + buckets 
		- Logs 
- 
#### Apache beam ![[beam.png | 50]]
- Programming model to define and execute data pipelines. 
#### Dataflow  (Data Processing Solution)
- Fully managed serverless service for <span style="color:rgb(255, 0, 0)">processing</span> and analyzing datastreams and batches. 
- Store processing data into BigQuery, or Cloud Storage. 
- Use apache beam SDK for pipeline creation. 
#### Datafusion ![[datafusion.png | 30]]
- Fully managed, cloud native data <span style="color:rgb(255, 255, 0)">integration service.</span> for Pipelines. 
- Built on open-source <span style="color:rgb(0, 255, 0)">Cask Data Application Platform</span> (CDAP). 
- Data integration service for managing ETL/ELT data pipelines. 
- Migrate Data from different sources (Bigquery, CloudStorage, Mysql, on-primses  database)
- Consolidating (تجميع) data from different sources into data warehouse.
- Data fusion features 
	1. Visual pipeline Design 
	2. Support different sources
	3. support parallel query execution. 
	4. Realtime data integration.  
	5. Scalability. 
	6. Monitoring and logging 
- Create ephemeral execution environment (Dataproc). 
- Compute profile to determine <span style="color:rgb(255, 255, 0)">how and where</span> the pipeline will run. 
- Namespace: is used to achieve data isolation. 
- Real Example (Robot)
	Imagine that there is (Robot) which can gather information form different sources such as 
		- Camera (see images)
		- Microphone (hears sound)
		- GPS (know location)
	Each sensor gives partial information, Data fusion combines them to help robot to understand the environment better and take smarter decisions. 
##### Datafusion Arhcitecture 
- Architecture 
	![[datafusionarch.png]]
- Component 
	![[datafusioncomponent.png]]
#### Dataproc 
- Fully managed service for easily create and manage Hadoop and Spark clusters.

#### Namespaces 
- Logical grouping of applications or Data
- You can think of namespace as instance partitioning. 
- 