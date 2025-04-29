# cloud-run-function-triggers-airflow-dag-basic-project
Step 1: Enabling the required APIs:
-----------------------------------
gcloud services enable cloudfunctions.googleapis.com composer.googleapis.com

Note: Composer environment is to be running state

Step 2: Create a Cloud Storage bucket:
--------------------------------------

make sure the bucket is there in the location where your composer environment is running 

Step 3: Get the composer web url:
-------------------------------------
gcloud composer environments describe composer-demo `
    --location us-central1 `
    --format='value(config.airflowUri)'
    
    
    https://fd9fcccaed6647f696acafef4494ab71-dot-us-central1.composer.googleusercontent.com   
    
    
Ste 4: DAG:
------------
import datetime
import airflow
from airflow.operators.bash import BashOperator


with airflow.DAG(
    "composer_sample_trigger_response_dag",
    start_date=datetime.datetime(2021, 1, 1),
    # Not scheduled, trigger only
    schedule_interval=None, # is not given - '*/10 * * * *'
) as dag:
    # Print the dag_run's configuration, which includes information about the
    # Cloud Storage object change.
    print_gcs_info = BashOperator(
        task_id="print_gcs_info", bash_command="echo {{ dag_run.conf }}"
    )
    
    - gcs to bq load - 
    - python operator 
    - bash operator 
    - gcp contributed operater - gcstobigquery operator
    - and etc ---
    
    
Step 4: Deploy a Cloud Function that triggers the DAG:
--------------------------------------------------------


folder structure:
-----------------
main.py
rest_api.py
requirements.txt 

------------------------
gcloud functions deploy gcs_crf_composer_dag_trigger_func `
  --gen2 `
  --runtime python312 `
  --region us-central1 `
  --source "." `
  --entry-point trigger_dag_gcf `
  --trigger-event google.storage.object.finalize `
  --trigger-resource composer-dag-trigger-from-crfs `
  --allow-unauthenticated `
  --memory=512Mi
