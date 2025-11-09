#### Architecture 
![[Pasted image 20250701103255.png]]
#### Data flow 
1. Qlik move encrypted Data from on-premsis Orcle DB to Landing bucket.     
2. Pub/Sub trigger cloud run which pull image from Artifact Registry 
3. Cloud Run start processing data and decrypt data with PGP 
4. Send Processed Data to Bigquery dataset and archiving bucket. 
5. SAP datasphere Read Data from big query. 