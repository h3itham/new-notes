
#### Description 
- Akanksha try to test data transfer change on cloud run but she faced timeout issue.
- Error which she faced 
```txt 
2025-11-05 15:53:50.486 IST   INFO:root:Waiting 10s...

2025-11-05 15:53:54.102 IST  [2025-11-05 10:23:54 +0000] [1] [CRITICAL] WORKER TIMEOUT (pid:2)

2025-11-05 15:53:55.022 IST  [2025-11-05 10:23:55 +0000] [1] [CRITICAL] WORKER TIMEOUT (pid:2)

2025-11-05 15:53:55.030 IST  [2025-11-05 10:23:55 +0000] [2] [ERROR] Error handling request /

2025-11-05 15:53:55.030 IST  Traceback (most recent call last): File "/usr/local/lib/python3.12/site-packages/gunicorn/workers/sync.py", line 134, in handle_self.handle_request(listene...

2025-11-05 15:53:55.030 IST  [2025-11-05 10:23:55 +0000] [2] [INFO] Worker exiting (pid: 2)

2025-11-05 15:53:55.255 IST  [2025-11-05 10:23:55 +0000] [3] [INFO] Booting worker with pid: 3

2025-11-05 15:53:55.802 IST  [2025-11-05 10:23:55 +0000] [1] [ERROR] Worker (pid:2) was sent SIGKILL! Perhaps out of memory?

2025-11-05 15:53:56.224 IST  INFO:root:0 waiting records for table fact_base_transform_lib_rep_e_20250824014040_1 and schema common_schema

2025-11-05 15:53:57.251 IST  INFO:root:Table fact_base_transform_lib_rep_fr_20250824014040_1 belongs to schema: common_schema

2025-11-05 15:53:57.251 IST  INFO:root:Delay time for file Fact_Base_Transform_LIB_REP_FR_20250824014040_1.csv.gpg before sleep: 61 at 2025-11-05 10:23:57.251888+00:00

2025-11-05 15:53:57.601 IST  [2025-11-05 10:23:57 +0000] [384] [INFO] Booting worker with pid: 384

2025-11-05 15:54:01.325 IST  INFO:root:0 in_progress records for table fact_base_transform_lib_rep_e_20250824014040_1 and schema common_schema

2025-11-05 15:54:08.725 IST  INFO:root:Total in-progress files: 1
```

- Cloudrun instance configuration 
```terraform 
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

- name: WEB_CONCURRENCY

value: 2

- name: WEB_TIMEOUT

value: 3600
```

#### Lessons 
1. Cloud run not ideal solution for heavy or long running processes. 
2. it is best for 
   - Short, request/response workloads (REST APIs, webhooks, lightweight processing).
  - Stateless microservices that scale fast and finish quickly (under a few minutes).
#### Potential Problem is 
1. Gunicorn problem 