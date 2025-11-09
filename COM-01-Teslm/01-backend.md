- I applied changes from github action 
	```bash 
	docker run -d \
	  --name teslm-backend \
	  --network teslm \
	  --ip 192.168.1.4 \
	  --restart unless-stopped \
	  -p 4050:4050 \
	  -p 5050:5050 \
	  -p 5051:5051 \
	  -p 5052:5052 \
	  -p 5068:5068 \
	  -e POSTGRES_HOST=192.168.1.2 \
	  -e POSTGRES_DATABASE=postgres \
	  -e POSTGRES_USER=teslmuser \
	  -e POSTGRES_PASSWORD=teslmpass \
	  -e POSTGRES_PORT=5432 \
	  -e DB_SSL=false \
	  -e MONGO_URI="mongodb://admin:secret@192.168.1.3:27017/teslm-db?authSource=admin" \
	  -e PORT=5050 \
	  -e MODE=DEV \
	  -e RUN_MIGRATIONS=true \
	  -e OTP_SERVER=http://localhost:8080 \
	  -e MSEGAT_API_KEY=D0A33FB434111DFE02585FF2394D3AB7 \
	  -e MSEGAT_USERNAME=mostafa1021999 \
	  -e MSEGAT_USER_SENDER=teslm \
	  -e CLOUDINARY_CLOUD_NAME=dmzdzq3ug \
	  -e CLOUDINARY_API_KEY=926956693483974 \
	  -e CLOUDINARY_API_SECRET=p9YFAgKI8bwm4zI_VfrzYF3NVlI \
	  -e GOOGLE_MAPS_API_KEY=AIzaSyCpC4N6-Wwa1CYn3KgTX_6qw7i284N5b1E \
	  -e CHAT_PORT=5051 \
	  -e DELIVERY_PARTNER_PORT=5052 \
	  -e NOTIFICATION_PORT=5068 \
	  -e ENCRYPTION_KEY=22f3e24c4f7355cb6db72328f3bedfe658e4ebdb32bc0c3149b484df2dfe612f \
	  -e AWS_REGION=eu-central-1 \
	  -e AWS_ACCESS_KEY_ID=AKIATREFKCYAE5MPPROJ \
	  -e AWS_SECRET_ACCESS_KEY=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 \
	  -e AWS_BUCKET=teslm-dev-assets \
	  teslm:v0.0.2
	
	```

- Nnginx 