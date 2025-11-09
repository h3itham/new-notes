##### Introduction 
I will using ElK Stack to handel and manage all logs on my server for Future use and to provide more flexibility with logs 
#### Create Docker volumes 
```bash 
docker volume create elasticsearch_data
```
#### Create and configure elastic search 

```bash 
docker run -d \
  --name elasticsearch \
  --network teslm \
  --ip 192.168.1.6 \
  -p 9200:9200 \
  -e ES_JAVA_OPTS="-Xms1g -Xmx1g" \
  -e discovery.type=single-node \
  -v elasticsearch_data:/usr/share/elasticsearch/data \
  docker.elastic.co/elasticsearch/elasticsearch:8.17.2
```

Reset password 
```bash 
docker exec -it elasticsearch bash
cd /usr/share/elasticsearch/bin
./elasticsearch-reset-password -u elastic -i
```
Test Elastic Search connection 
```bash 
curl   -u elastic:Haitham@01555 http://localhost:9200
```

#### Create and configure filebeat 
```bash 
docker run -d \
  --name filebeat \
  --user=root \
  --network teslm \
  --ip 192.168.1.7 \
  -v /var/lib/docker:/var/lib/docker:ro \
  -v /var/run/docker.sock:/var/run/docker.sock \
  docker.elastic.co/beats/filebeat:8.17.2
```

Filebeat configuration 
```bash
# exec filebeat
docker exec -it filebeat bash 
# vim /usr/share/filebeat/filebeat.yml
```

```yml 
filebeat.inputs:
  - type: container
    paths: 
      - /var/lib/docker/containers/*/*.log

processors:
  - add_docker_metadata:
      host: "unix:///var/run/docker.sock"

  - decode_json_fields:
      fields: ["message"]
      target: "json"
      overwrite_keys: true

output.elasticsearch:
  hosts: ["http://192.168.1.6:9200"]
  username: "elastic"
  password: "Haitham@01555"
  ssl.verification_mode: none
  indices:
    - index: "teslm-%{[agent.version]}-%{+yyyy.MM.dd}"

logging.json: true
logging.metrics.enabled: false
```

Restart filebeat 
```bash 
docker restart filebeat 
```
#### Install and configure kibana 
- First Generate service account token (save it)
	```bash 
	curl -u elastic:Haitham@01555 -X POST "http://localhost:9200/_security/service/elastic/kibana/credential/token/my-token"

	```
- Generate Encryption key (save it)
```bash 
openssl rand -hex 32
```
- Create Kibana 
```bash 
docker run -d \
  --name kibana \
  --network teslm \
  --ip 192.168.1.8 \
  -p 5601:5601 \
  -e ELASTICSEARCH_HOSTS="http://192.168.1.6:9200" \
  -e ELASTICSEARCH_SERVICEACCOUNTTOKEN=<""> 
  -e XPACK_SECURITY_ENCRYPTIONKEY=<""> 
  -e XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=<""> 
  -e XPACK_REPORTING_ENCRYPTIONKEY=<""> 
  -e XPACK_SECURITY_SECURE_COOKIES="true" \
  -e ELASTICSEARCH_SSL_VERIFICATIONMODE="none" \

  docker.elastic.co/kibana/kibana:8.17.2

```

#### Create Index Life Cycle 
1. Create the ILM Policy
	```json 
	PUT _ilm/policy/teslm-policy
	{
	  "policy": {
	    "phases": {
	      "hot": {
	        "min_age": "0ms",
	        "actions": {
	          "rollover": {
	            "max_size": "200mb",
	            "max_age": "1d"
	          },
	          "set_priority": {
	            "priority": 100
	          }
	        }
	      },
	      "warm": {
	        "min_age": "3d",
	        "actions": {
	          "set_priority": {
	            "priority": 50
	          }
	        }
	      },
	      "cold": {
	        "min_age": "7d",
	        "actions": {
	          "set_priority": {
	            "priority": 0
	          }
	        }
	      },
	      "delete": {
	        "min_age": "10d",
	        "actions": {
	          "delete": {}
	        }
	      }
	    }
	  }
	}
	```
2. Create Index Template (Applies to All teslm Indices)
```json
PUT _index_template/teslm-template
{
  "index_patterns": ["teslm-*"],
  "priority": 500,
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "teslm-policy"
    }
  }
}
```

3. Apply Policy to Existing Indices
	```json
	PUT teslm-*/_settings
	{
	  "index.lifecycle.name": "teslm-policy"
	}
	```