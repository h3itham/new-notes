1. Create simple python code which print message 
	```python 
	from airflow import DAG
	from airflow.operators.python import PythonOperator
	from datetime import datetime
	
	def test_function():
	    print("✅ Airflow DAG is running successfully on Composer!")
	# Define default args
	default_args = {
	    'owner': 'airflow',
	    'start_date': datetime(2025, 1, 1),
	    'retries': 0
	}
	
	# Define DAG
	with DAG(
	    dag_id='test_dag',
	    default_args=default_args,
	    schedule_interval='@daily',
	    catchup=False,
	    tags=['test']
	) as dag:
	
	    test_task = PythonOperator(
	        task_id='print_message',
	        python_callable=test_function
	    )
	
	    test_task
	```

- Upload it to Bucket 
	- From GCP console select composer 
	- Open DAGS folder then upload file in the bucket, Wait for 2 minutes  
		![[opencomposer.png]]
	- Open Airflow GUI. 
