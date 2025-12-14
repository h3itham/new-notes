- Volumes 
	```bash 
	docker volume create mycash-prod-static 
	docker volume create mycash-prod-media
	```
- Build image 
```bash 
docker build 
```
- Container it self 
```bash 
	docker run -itd \
  --name mycash \
  --restart always \
  --network bluebook \
  --ip 192.168.1.100 \
  -v mycash-prod-static:/app/static \
  -v mycash-prod-media:/app/media \
  mycash:v1 
```