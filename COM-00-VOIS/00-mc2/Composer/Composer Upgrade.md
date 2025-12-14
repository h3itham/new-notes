#### Introduction 
This is a detailed notes to composer upgrade for future use.

#### Steps 
1. Announce Our ETL Team that we will start upgrading the instance and they should have to take bacup (Compser/CDF upgrade) Group. 
2. Create two PRS  
	1. One for `Delete` the running composer. 
	2. One for Changes or new image. 
  start from the stop or delete Pr and create the start PR from the stop one [IMP]	
3. Send to ETL engineer to take backup. 
4. Send mail to ops team to stop all alrets 
	```txt 
	Disabling alerts for Composer Upgrade in Live
	Hi Team,
	
	Composer Upgrade for Live Environment is planned for 08/12/2025 from 08:30AM UTC (02:00 PM IST) to 04:30PM UTC(10:00 PM IST). Requesting you to disable the MC2 Dag’s and pipeline’s alerts during this time window.
	```

#### Code Explanation 
- My Implementation 
```yml 
  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//composer_v2?ref=composer_v2_v1.1.9
    depends_on: 
      - module.dev_environment
      - module.buckets
    name: composer_v2
    project_name: ${var.project_id}
    region: "europe-west1"
    zone: "europe-west1-b"
    common_resource_id: "dev"
    sa_prefix: "grp-mc2"
    composer_bucket: "vf-mc2dev-ca-nonlive-composer-v2"
    composer_service_account: "${module.dev_environment.composer_sa}"
    composer_image_version: "composer-2.10.0-airflow-2.10.2"
    composer_authorised_network: "${module.tenant_core.management_zone_cidr}"
    network_name: "${module.tenant_core.vpc_name}"
    composer_subnet: "${module.tenant_core.trusted_zone_name}"
    composer_env_subnetwork: "${module.dev_environment.restricted_zone}"
    master_ipv4_cidr_block: "172.16.3.0/28"
    default_ip_allocation_policy: true
    use_relative_networks: true
	# WORKER BLOCK Support 
    composer_worker_min_count: 1 # The minimum number of airflow workers 
    composer_worker_max_count: 6 # The maximum number of airflow workers 
    composer_worker_cpu: 1       # The number of CPUs for a single worker
    composer_worker_memory_gb: 4 # The amount of ram for each woker 
	# WEB SERVER CONFIGURATION 
    composer_web_server_cpu: 2   # The number of CPUs for webser 
    composer_web_server_memory_gb: 2 # The amount of RAM for webserver 
    composer_env_variables: 
      gce_zone: "europe-west1-b"
      opco: ${var.tenant}
      service_account: "${module.dev_environment.dataproc_sa}"
      firewall_rules_tags: "allow-ssh-from-management-zone-dev,allow-internal-dataproc-dev"
    pypi_packages:
      google-api-core: ==2.14.0
      google-cloud-core: ==2.3.3
      google-cloud-datastore: ==2.18.0
      google-cloud-logging: ==3.8.0
      mysql-connector-python: ==8.3.0
      apache-airflow-providers-google: ==10.12.0
      pymsteams: ==0.2.1
      airflow-gpg-plugin: ==0.2.0
      # kfp:                    ==1.8.18
      # kfp-pipeline-spec:      ==0.2.0
      # kfp-server-api:         ==1.8.5
    airflow_config_overrides:
      core-dag_run_conf_overrides_params: "True"
      scheduler-catchup_by_default: "false"
      webserver-workers: "1"
      core-min_serialized_dag_update_interval: "30"
      api-auth_backend: "airflow.composer.api.backend.composer_auth"
      core-max_active_runs_per_dag: "100"
      core-max_active_tasks_per_dag: "100"
      core-dag_concurrency: "100"
      core-parallelism: "100"
```

#### Platform modifications 
1. Add maintaince window in configuation 
2. There was problem with gke_cluster in `firewall.tf` line `7` which caused the following problem while planning terraform 
	![[gke-firewall.png]]
	the code before fix 
	![[code-before.png]]
	the code after fix 
	![[code-after.png]]	