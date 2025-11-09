##### Introduction 
I will using ElK Stack to handel and manage all logs on my server for Future use and to provide more flexibility with logs 
#### Create ELK Network 
```bash 
docker network create \
  --driver=bridge \
  --subnet=172.25.0.0/16 \
  --gateway=172.25.0.1 \
  elk-network || true
```
#### Create Docker volumes 
```bash 
docker volume create elasticsearch_data
```
#### Create and configure elastic search 

```bash 
docker run -d \
  --name elasticsearch \
  --network elk-network \
  --ip 172.25.0.10 \
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
  --network elk-network \
  --ip 172.25.0.11 \
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
  hosts: ["http://172.25.0.10:9200"]
  username: "elastic"
  password: "Haitham@01555"
  ssl.verification_mode: none
  indices:
    - index: "filebeat-%{[agent.version]}-%{+yyyy.MM.dd}"

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
  --network elk-network \
  --ip 172.25.0.12 \
  -p 5601:5601 \
  -e ELASTICSEARCH_HOSTS="http://172.25.0.10:9200" \
  -e ELASTICSEARCH_SERVICEACCOUNTTOKEN=<""> 
  -e XPACK_SECURITY_ENCRYPTIONKEY=<""> 
  -e XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=<""> 
  -e XPACK_REPORTING_ENCRYPTIONKEY=<""> 
  -e XPACK_SECURITY_SECURE_COOKIES="true" \
  -e ELASTICSEARCH_SSL_VERIFICATIONMODE="none" \

  docker.elastic.co/kibana/kibana:8.17.2

```

#### Run filebeat in Hicode Server 
- Create instance 
```bash 
docker run -d \
  --name filebeat \
  --user=root \
  --network bluebook
  --ip 192.168.1.181 \
  -v /var/lib/docker:/var/lib/docker:ro \
  -v /var/run/docker.sock:/var/run/docker.sock \
  docker.elastic.co/beats/filebeat:8.17.2
```