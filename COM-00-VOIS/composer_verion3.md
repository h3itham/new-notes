```yaml 
- source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//composer_v3?ref=composer_v3_1.0

depends_on:

- module.core_network_tf14

- module.tenant_cryptokey_tf12

name: composer_v3

project_name: ${var.project_id}

region: "europe-west3"

zone: "europe-west3-b"

common_resource_id: "mirep"

network_name: "${module.core_network_tf14.vpc_network.name}"

composer_subnet: "${module.core_network_tf14.subnets.composer.name}"

composer_internal_ipv4_cidr_block: "10.10.0.0/20"

composer_image_version: "composer-3-airflow-2.10.5-build.10"

environment_size: "ENVIRONMENT_SIZE_SMALL"

pypi_packages:

google-api-core: "==2.25.1"

google-cloud-core: "==2.4.3"

google-cloud-datastore: "==2.21.0"

mysql-connector-python: "==9.3.0"

pandas: "==2.1.4"

requests: "==2.32.4"

openpyxl: "==3.0.10"

composer_bucket_suffix: "comp-v3"

bucket_kms_key_name: ${module.tenant_cryptokey_tf12.tenant_cmek-gcs}

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

- Created from command line 
1. firewall rule
	```
	gcloud compute firewall-rules create allow-composer-to-google-apis \
	    --project=vf-grp-mirep-dev \
	    --network=vf-grp-mirep-dev-vpc \
	    --direction=EGRESS \
	    --action=ALLOW \
	    --priority=900 \
	    --destination-ranges=199.36.153.4/30,199.36.153.8/30 \
	    --target-service-accounts=service-602462157184@cloudcomposer-accounts.iam.gserviceaccount.com \
	    --rules=tcp:443
	```
	