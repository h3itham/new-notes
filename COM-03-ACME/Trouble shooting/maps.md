
#### Dev Environment (sail)

1. Environment Variables
   ```txt 
	DEFAULT_TILES=normal
	MAX_ZOOM_LEVEL=17
	MIN_ZOOM_LEVEL=11
	MAP_URL_HYBRID=http://192.168.2.84/storage/map/tiles/hybrid/{z}/{x}/{y}.jpg
	MAP_URL_NORMAL=http://192.168.2.84/storage/map/tiles/normal/{z}/{x}/{y}.png
	```

2. Location in 
   ```txt
     /var/www/html/storage/app/public/map
	```
3. Don't forget to link storage with the following file
   ```bash
    php artisan storage:link
    ```
#### Dev Environment (Dev Cluster)
1. Environment Variables
   ```txt 
      DEFAULT_TILES: normal
      MIN_ZOOM_LEVEL: "11"
      MAX_ZOOM_LEVEL: "17"
      MAP_URL_NORMAL: https://cta-dev.local/storage/map/tiles/hybrid/{z}/{x}/{y}.jpg
      MAP_URL_NORMAL: https://cta-dev.local/storage/map/tiles/normal/{z}/{x}/{y}.png
	```
	
2. Location in 
   ```txt
     /var/www/html/storage/app/public/map
     ```


#### Prod Environment
1. Environment Variables
   ```txt 
      DEFAULT_TILES: normal
      MIN_ZOOM_LEVEL: "11"
      MAX_ZOOM_LEVEL: "17"
      MAP_URL_NORMAL: https://cta-dev.local/storage/map/tiles/hybrid/{z}/{x}/{y}.jpg
      MAP_URL_NORMAL: https://cta-dev.local/storage/map/tiles/normal/{z}/{x}/{y}.png
	```
	
2. Location in 
   ```txt
     /var/www/html/storage/app/public/map
