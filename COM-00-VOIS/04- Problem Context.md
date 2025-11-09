I have the following problem `mi-reporting` and `mi-reporting2-orchestration2` Can't talk to each other I mean I need to invoke the second cloudrun but I can't 
- Code 
	```yaml 
	---
	general:
	  neuron_gcp_platform_version: 1.11.290
	  tf_version: 0.14.4
	  google_terraform_version: 6.37.0
	  google_beta_terraform_version: 6.37.0
	
	modules:
	  - name: terraform_provider_base_tf12
	    tenant: grp
	
	  - source: "git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//core_network?ref=core_network_v3.2.10"
	    name: core_network_tf14
	    project_name: ${var.project_id}
	    region: europe-west3
	    deny_egress: true
	    restricted_google_apis: true
	    allow_github_access: true
	    ingress_ssh_via_IAP: false
	    ingress_health_check: false
	    allow_internal_communication: true
	    nat_external_ips:
	      - name: "data-ext-nat-address-1"
	        description: "Permanent NAT External IP for whitelisting, PLEASE DO NOT REMOVE IT"
	        region: europe-west3
	    subnets:
	      - name      : trusted
	        cidr      : 172.22.24.0/24
	        region    : europe-west3
	        allow_nat : false
	      - name      : composer
	        cidr      : 172.22.30.0/24
	        region    : europe-west3
	        allow_nat : false
	  
	  - source: "git::https://github.vodafone.com/VFGroup-CloudAnalytics/terraform-modules.git//dns_zone?ref=dns_zone_v1.0.0"
	    name: dns_zone
	    project_name: ${var.project_id}
	    network_name: ${module.core_network_tf14.vpc_network.name}
	    dns_zone_name: "googleapis-com"
	    dns_name: "googleapis.com."
	    a_records: 
	      - dns_name: "restricted.googleapis.com."
	        ips: ["199.36.153.4", "199.36.153.5", "199.36.153.6","199.36.153.7"]
	    cname_records:
	      - dns_name: "*.googleapis.com."
	        canonical_name: ["restricted.googleapis.com."]
	
	  # Network With DNS Logging Profile
	  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//google_dns_policy/?ref=feat/google_dns_policy_module
	    name: google_dns_policy
	    network_id: ${module.core_network_tf14.vpc_network.id}
	
	  - name: tenant_cryptokey_tf12
	    cloudrun_key: true
	    cloudrun_key_location: europe-west3
	    secretmanager_key: true
	    secretmanager_key_location: europe-west3
	    gcs_key: true
	    gcs_key_location: europe-west3
	    
	  - name: service_account_tf12
	    sa_name: "pubsub-sa"
	    only_sa_name: true
	    sa_roles:
	      - "roles/run.invoker"
	      - "roles/pubsub.subscriber"
	      
	  - name: service_account_tf12
	    resource_name_override: "cloudrun_sa"
	    sa_name: "cloudrun-sa"
	    only_sa_name: true
	    sa_roles:
	      - "roles/secretmanager.secretAccessor"
	      - "roles/bigquery.jobUser"
	
	  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//pam?ref=pam_v1.1.1
	    name: pam
	    project_id: ${var.project_id}
	    environment: ${var.environment}
	
	  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//cloud_run?ref=cloud_run_v1.0.3
	    name: cloud_run
	    service_name: mi-reporting
	    location: europe-west3
	    region: europe-west3
	    project_id: ${var.project_id}
	    service_account_email: mirep-test@vf-grp-mirep-dev-beta.iam.gserviceaccount.com # created from console
	    image: europe-west3-docker.pkg.dev/vf-grp-mirep-dev-beta/my-repo/mirep:v17
	    ports: 
	      name: http1
	      port: 8080
	    limits:
	      cpu: 8
	      memory: 32Gi
	    no_auth: false
	    timeout_seconds: 3600
	    container_concurrency: 2
	    service_annotations:
	      run.googleapis.com/binary-authorization: default
	      run.googleapis.com/ingress: internal
	      run.googleapis.com/ingress-status: internal
	    template_annotations:
	      autoscaling.knative.dev/maxScale: '60'
	      run.googleapis.com/network-interfaces: '[{"network":"vf-grp-mirep-dev-beta-vpc","subnetwork":"vf-grp-mirep-dev-beta-subnet-trusted"}]'
	      run.googleapis.com/vpc-access-egress: all-traffic
	    env_vars:
	      - name: BQ_DATASET
	        value: vfgrp_dh_lake_edw_mipyramid_nonlive_s
	      - name: BQ_PROJECT_ID
	        value: vf-grp-mirep-dev-beta
	      - name: BUCKET_PROJECT_ID
	        value: vf-grp-mirep-dev-beta
	      - name: DEST_BUCKET_NAME
	        value: vfgrp-dh-mireporting-arch
	      - name: PROJECT_ID
	        value: vf-grp-mirep-dev-beta
	      - name: SECRET_NAME
	        value: gpg-private-key
	      - name: SEC_PROJECT_ID
	        value: vf-grp-mirep-dev-beta
	      - name: SOURCE_BUCKET_NAME
	        value: vfgrp-dh-mireporting-src
	      - name: VERSION
	        value: latest
	      - name: CLOUDRUN_URL 
	        value: "${module.cloud_run2_2.service_url}" 
	
	
	  - name: mirep_pubsub_notifications
	    source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//pubsub_notifications?ref=pubsub_notifications_v1.0.0
	    pubsub_topic: "projects/vf-grp-mirep-dev-beta/topics/mirep-reproting"
	    bucket_name:  "vfgrp-dh-mireporting-src"
	    event_types: "OBJECT_FINALIZE"
	
	  - name: mirep_pubsub_topic
	    source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terraform-modules.git//pubsubtopics_tf12?ref=feature/pubsubtopics_tf12
	    project_id: ${var.project_id}
	    region: europe-west3
	    sub_labels: {}
	    topic_labels: {}
	    key_id: ${module.tenant_cryptokey_tf12.tenant_cmek-gcs}
	    topics:
	      - name: mirep-reproting
	        key_id: ${module.tenant_cryptokey_tf12.tenant_cmek-gcs}
	        message_retention_duration: "432000s"
	        sa_roles: {}
	        subscriptions: []
	
	  - name: pubsub_subscription_tf12
	    resource_name_override: "mirep_pubsub_subscription"
	    depends_on:
	      - module.cloud_run
	    topic: "mirep-reproting"
	    topic_project: vf-grp-mirep-dev-beta
	    subscription: mirep-reporting
	    service_account_email: pubsub-sa@vf-grp-mirep-dev-beta.iam.gserviceaccount.com
	    push_endpoint: "${module.cloud_run.service_url}"
	    enable_message_ordering: false
	    minimum_backoff: ""
	    message_retention_duration: "604800s"
	    retain_acked_messages: false
	    filter: "attributes.eventType = \"OBJECT_FINALIZE\""
	    roles: {}
	
	
	  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terraform-modules.git//project_role_members?ref=project_role_members_v1.2.1
	    name: project_iam
	    project_name: "${var.project_id}"
	    member_roles:
	      "group:gcp-vf-grp-mi-reporting-dataengineer@vodafone.com":
	        - "roles/bigquery.dataEditor"
	        
	
	###############
	# 2nd Solution
	###############
	  - name: service_account_tf12
	    resource_name_override: "cloudrun2_sa"
	    sa_name: "cloudrun2-sa"
	    only_sa_name: true
	    sa_roles: 
	      - "roles/bigquery.jobUser"
	
	  - name: pubsub_subscription_tf12
	    resource_name_override: "pubsub_subscription2"
	    depends_on:
	      - module.cloud_run2
	    topic: "mi-reproting-trigger2"
	    topic_project: vf-grp-mirep-datahub-beta
	    subscription: cloud-run-notification-subscriper2
	    service_account_email: pubsub-sa@vf-grp-mirep-dev-beta.iam.gserviceaccount.com
	    push_endpoint: "${module.cloud_run2.service_url}"
	    enable_message_ordering: false
	    minimum_backoff: ""
	    message_retention_duration: "604800s"
	    retain_acked_messages: false
	    filter: "attributes.eventType = \"OBJECT_FINALIZE\""
	    roles: {}
	
	  - source: git::https://github.vodafone.com/VFGroup-CloudAnalytics/terrraform-modules.git//cloud_run?ref=cloud_run_v1.0.3
	    depends_on: 
	      - module.cloudrun2_sa
	    name: cloud_run2_2
	    service_name: mi-reporting2-orchestration2
	    location: europe-west3
	    region: europe-west3
	    project_id: ${var.project_id}
	    service_account_email: cloudrun2-sa@vf-grp-mirep-dev-beta.iam.gserviceaccount.com
	    image: europe-west3-docker.pkg.dev/vf-grp-mirep-dev-beta/mirep2-2/mirep:v1
	    ports: 
	      name: http1
	      port: 8080
	    limits:
	      cpu: 8
	      memory: 32Gi
	    no_auth: false
	    timeout_seconds: 3600
	    container_concurrency: 2
	    service_annotations:
	      run.googleapis.com/binary-authorization: default
	      run.googleapis.com/ingress: internal
	      run.googleapis.com/ingress-status: internal
	    template_annotations:
	      run.googleapis.com/network-interfaces: '[{"network":"vf-grp-mirep-dev-beta-vpc","subnetwork":"vf-grp-mirep-dev-beta-subnet-trusted"}]'
	      run.googleapis.com/vpc-access-egress: all-traffic
	    env_vars:
	      - name: PROJECT_ID
	        value: vf-grp-mirep-datahub-beta
	      - name: SOURCE_BUCKET_NAME
	        value: vfgrp-dh-advanced-analytics-beta
	      - name: BQ1_DATASET
	        value: vfgrp_dh_lake_edw_mipyramid_tactical_live_s
	      - name: BQ2_DATASET
	        value: vfgrp_dh_lake_edw_mipyramid_live_s
	  
	  - name: service_account_tf12
	    resource_name_override: stored-procedures-sa
	    tenant: "mirep"
	    sa_name: "sp"
	    application: "cb"
	    sa_roles: ['roles/cloudbuild.builds.builder','roles/cloudbuild.workerPoolUser','roles/bigquery.jobUser','roles/iam.serviceAccountTokenCreator']
	```
- Error 
```txt
resource: {2}
severity: "ERROR"
textPayload: "Traceback (most recent call last):
  File "/usr/local/lib/python3.12/site-packages/urllib3/connectionpool.py", line 787, in urlopen
    response = self._make_request(
               ^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/site-packages/urllib3/connectionpool.py", line 488, in _make_request
    raise new_e
  File "/usr/local/lib/python3.12/site-packages/urllib3/connectionpool.py", line 464, in _make_request
    self._validate_conn(conn)
  File "/usr/local/lib/python3.12/site-packages/urllib3/connectionpool.py", line 1093, in _validate_conn
    conn.connect()
  File "/usr/local/lib/python3.12/site-packages/urllib3/connection.py", line 753, in connect
    self.sock = sock = self._new_conn()
                       ^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/site-packages/urllib3/connection.py", line 213, in _new_conn
    raise NewConnectionError(
urllib3.exceptions.NewConnectionError: <urllib3.connection.HTTPSConnection object at 0x7f9fa6dc76e0>: Failed to establish a new connection: [Errno 101] Network unreachable"
timestamp: "2025-10-30T14:08:02.065248Z"
traceSampled: false

```

- network module 
	- firewall egress 
		```terraform 
		resource "google_compute_firewall" "deny_all_egress" {
		  count       = var.deny_egress ? 1 : 0
		  project     = var.project_name
		  name        = join("-", [local.common_resource_id, var.custom_deny_egress_fw_name])
		  network     = google_compute_network.vpc_network.name
		  description = "Deny all traffic by default"
		  priority    = 1100
		  direction   = "EGRESS"
		
		  deny {
		    protocol = "all"
		  }
		
		  destination_ranges = ["0.0.0.0/0"]
		  log_config {
		    metadata = "INCLUDE_ALL_METADATA"
		  }
		}
		
		resource "google_compute_firewall" "allow_internal_communication" {
		  count     = var.allow_internal_communication ? 1 : 0
		  project   = var.project_name
		  name      = join("-", [local.common_resource_id, var.custom_allow_internal_communication_fw_name])
		  network   = google_compute_network.vpc_network.name
		  direction = "EGRESS"
		
		  allow {
		    protocol = "all"
		  }
		
		  # all valid private ip ranges
		  # https://cloud.google.com/vpc/docs/vpc
		  # Private IP addresses RFC 1918
		  destination_ranges = [
		    "10.0.0.0/8",
		    "172.16.0.0/12",
		    "192.168.0.0/16"
		  ]
		
		  log_config {
		    metadata = "INCLUDE_ALL_METADATA"
		  }
		}
		
		resource "google_compute_firewall" "allow_github" {
		  project   = var.project_name
		  count     = var.allow_github_access ? 1 : 0
		  name      = join("-", [local.common_resource_id, var.custom_allow_github_fw_name])
		  network   = google_compute_network.vpc_network.name
		  direction = "EGRESS"
		
		  allow {
		    protocol = "tcp"
		    ports    = ["443"]
		  }
		
		  destination_ranges = [
		    "47.73.94.133/32",    # Old Vodafone GitHub public IP
		    "47.73.42.176/32",    # New public IP after migration
		    "47.73.23.55/32",     # New public IP after migration
		    "195.233.144.162/32", # temporary migration IP
		  ]
		
		  log_config {
		    metadata = "INCLUDE_ALL_METADATA"
		  }
		}
		
		resource "google_compute_firewall" "allow_restricted_google_apis" {
		  project   = var.project_name
		  count     = var.restricted_google_apis ? 1 : 0
		  name      = join("-", [local.common_resource_id, var.custom_allow_restricted_google_apis_fw_name])
		  network   = google_compute_network.vpc_network.name
		  direction = "EGRESS"
		
		  allow {
		    protocol = "tcp"
		    ports    = ["443"]
		  }
		
		  destination_ranges = ["199.36.153.4/30"] #https://cloud.google.com/vpc/docs/configure-private-google-access-hybrid
		
		  log_config {
		    metadata = "INCLUDE_ALL_METADATA"
		  }
		}
		
		resource "google_compute_firewall" "allow_private_google_apis" {
		  project   = var.project_name
		  count     = var.private_google_apis ? 1 : 0
		  name      = join("-", [local.common_resource_id, var.custom_allow_private_google_apis_fw_name])
		  network   = google_compute_network.vpc_network.name
		  direction = "EGRESS"
		
		  allow {
		    protocol = "tcp"
		    ports    = ["443"]
		  }
		
		  destination_ranges = ["199.36.153.8/30"]
		
		  log_config {
		    metadata = "INCLUDE_ALL_METADATA"
		  }
		}
		```
	- firewall ingress 
	```terraform 
	resource "google_compute_firewall" "allow_ingress_ssh_via_IAP" {
	  count     = var.ingress_ssh_via_IAP ? 1 : 0
	  project   = var.project_name
	  name      = join("-", [local.common_resource_id, "ingress-allow-ssh-via-iap"])
	  network   = google_compute_network.vpc_network.name
	  direction = "INGRESS"
	
	  allow {
	    protocol = "tcp"
	    ports    = ["22"]
	  }
	
	  # IAP source range
	  # https://cloud.google.com/iap/docs/using-tcp-forwarding#create-firewall-rule
	  source_ranges = ["35.235.240.0/20"]
	
	  log_config {
	    metadata = "INCLUDE_ALL_METADATA"
	  }
	}
	
	resource "google_compute_firewall" "allow_ingress_health_check" {
	  count       = var.ingress_health_check ? 1 : 0
	  project     = var.project_name
	  name        = join("-", [local.common_resource_id, "ingress-health"])
	  network     = google_compute_network.vpc_network.name
	  description = "Allow health check ingress"
	  direction   = "INGRESS"
	
	  allow {
	    protocol = "tcp"
	    ports    = ["443", "80"]
	  }
	
	  # Load Balancer Health Checks source ranges
	  # https://cloud.google.com/load-balancing/docs/health-checks#fw-rule
	  source_ranges = ["130.211.0.0/22", "35.191.0.0/16"]
	
	  log_config {
	    metadata = "INCLUDE_ALL_METADATA"
	  }
	}
	
	```
- network 
```terraform 
resource "google_compute_network" "vpc_network" {
  name                                      = var.custom_vpc_name == "" ? join("-", [local.common_resource_id, "vpc"]) : var.custom_vpc_name
  project                                   = var.project_name
  auto_create_subnetworks                   = false
  network_firewall_policy_enforcement_order = var.network_firewall_policy_enforcement_order
}

resource "google_compute_router" "router" {
  for_each = local.distinct_nat_regions
  project  = var.project_name
  name     = var.custom_router_name == "" ? join("-", [local.common_resource_id, "router", each.value]) : var.custom_router_name
  network  = google_compute_network.vpc_network.self_link
  region   = each.value
  bgp {
    asn = 64514
  }
}

resource "google_compute_address" "nat_external_ip" {
  project     = var.project_name
  for_each    = { for ip in local.nat_external_ips : ip.name => ip }
  name        = each.key
  description = each.value.description
  region      = each.value.region
}

resource "google_compute_router_nat" "router_nat" {
  for_each               = local.dont_create_nat ? [] : local.distinct_nat_regions
  provider               = google-beta
  project                = var.project_name
  region                 = each.value
  name                   = var.custom_nat_name == "" ? join("-", [local.common_resource_id, "nat-gateway", each.value]) : var.custom_nat_name
  router                 = google_compute_router.router[each.key].name
  min_ports_per_vm       = var.min_ports_per_vm
  nat_ip_allocate_option = "MANUAL_ONLY"
  nat_ips = [
    for ip in local.nat_external_ips :
    google_compute_address.nat_external_ip[ip.name].self_link
    if ip.region == each.value
  ]

  source_subnetwork_ip_ranges_to_nat = var.nat_source_mode

  dynamic "subnetwork" {
    for_each = local.allowed_natted_subnets
    content {
      name                    = subnetwork.value.self_link
      source_ip_ranges_to_nat = ["ALL_IP_RANGES"]
    }
  }

  log_config {
    enable = true
    filter = var.nat_log_filter
  }
}

resource "google_compute_subnetwork" "subnets" {
  for_each                 = { for subnet in local.subnets : subnet.name => subnet }
  project                  = var.project_name
  name                     = join("-", [local.common_resource_id, "subnet", each.value.name])
  network                  = google_compute_network.vpc_network.name
  region                   = lookup(each.value, "region", var.region)
  private_ip_google_access = true
  ip_cidr_range            = each.value.cidr

  log_config {
    aggregation_interval = "INTERVAL_30_SEC"
    flow_sampling        = 1
    metadata             = "INCLUDE_ALL_METADATA"
  }
}
```

this is good conversation 
https://claude.ai/chat/f56fd23c-951c-42bd-b7b3-349147161179