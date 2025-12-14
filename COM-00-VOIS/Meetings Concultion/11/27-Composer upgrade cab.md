- Hello everyone,
- We are planning to upgrade Composer from  version **composer-2.10.0-airflow-2.10.2** to **composer-2.15.3-airflow-2.10.5** because the older image is reaching its end of support.
- We have already made this change in the alpha environment and non live, and our ETL team is testing everything, so we can proceed with the non-live environment. 
- Pull Requests:
    - **Deletion of the existing environment:**  
        [Delete](https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs/pull/22842)
    - **Creation of the new Composer environment with the updated image:**  
        [create](https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs/pull/22843)
- We have also created a dedicated bucket that is no longer tied to the Composer environment. This will simplify future upgrades, as we will not need to back up everything each time.