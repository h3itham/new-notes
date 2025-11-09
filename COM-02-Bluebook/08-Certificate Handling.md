1. Create Script which will handle certificate `renew_ssl.sh`
	```bash 
	#!/bin/bash
	# List your domains here, space-separated
	DOMAINS_LIST=("sebalenses.com" "hicode.academy")
	EMAIL="haithamelabd@outlook.com"  # <-- Change this to your email
	LOG_FILE="/home/haitham/cert_renewal.log"  # <-- Set the desired path for your log file
	
	# Loop through each domain and renew certificate inside the 'proxy' container
	for DOMAIN in "${DOMAINS_LIST[@]}"; do
	  echo "Renewing SSL certificate for $DOMAIN..."
	
	  docker exec proxy certbot --nginx -d "$DOMAIN" --non-interactive --agree-tos --email $EMAIL
	
	  if [ $? -eq 0 ]; then
	    echo "SSL certificate renewed for $DOMAIN."
	    # Reload nginx inside the 'proxy' Docker container
	    docker exec proxy nginx -s reload
	    # Log renewal time in local readable format
	    echo "[$(date '+%d %b %Y at %I:%M %p')] $DOMAIN: renewal successful" >> "$LOG_FILE"
	  else
	    echo "SSL certificate renewal failed for $DOMAIN."
	    echo "[$(date '+%d %b %Y at %I:%M %p')] $DOMAIN: renewal FAILED" >> "$LOG_FILE"
	  fi
	done
	```
2. Just change crontab to run every 2 months 
	```txt 
	0 17 1 1,3,5,7,9,11 * /home/haitham/renew_ssl.sh >> /home/haitham/cert_renewal.log 2>&1
	```
