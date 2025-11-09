#### DataBase 
1. Bluebook 
	```bash 
	 mysqldump -u root -phaitham -h 86.48.3.99 -P 3366 --set-gtid-purged=OFF bluebookProd > /media/haitham/work/backup/database/bluebook/bluebook_$(date +\%Y-\%m-\%d).sql 
	```
2. Hicode 
	```bash 
	 mysqldump -u root -phaitham -h 86.48.3.99 -P 3366 --set-gtid-purged=OFF hicodeProd > /media/haitham/work/backup/database/hicode/bluebook_$(date +\%Y-\%m-\%d).sql 
	```


##### Media File
1. Make sure that the root user at `source` server can access the `backup` server with ssh keys. 
2. Add the following script to server which you need to backup it 
	```bash
	#!/bin/bash
	# Variables
	REMOTE_IP="86.48.3.99"
	SOURCE_BASE="/var/lib/docker/volumes"
	REMOTE_BASE="/home/haitham/backup/bluebook"
	FOLDERS=("bluebook-prod-media" "bluebook-prod-static")
	SSH_KEY="/root/.ssh/id_ed25519"  # Make sure to set this to the correct p
	ath
	ARCHIVE_DIR="/tmp/backup_archives"  # Temporary directory for storing arc
	hives
	
	# MySQL backup variables
	DB_NAME="bluebookProd"  # Database name variable
	DB_PASSWORD="Blueb00k2024"  # Database password variable
	DB_USER="root"  # Fixed user as requested
	DB_HOST="127.0.0.1"  # Fixed host as requested
	DB_PORT="3366"  # Fixed port as requested
	
	# Get today's date for daily rollover
	TODAY=$(date +%Y-%m-%d)
	REMOTE_DIR="$REMOTE_BASE/$TODAY"  # Remote directory for today's backups
	
	# Log file
	LOGFILE="/var/log/docker_volume_backup.log"
	
	# Create temporary archive directory
	mkdir -p "$ARCHIVE_DIR"
	
	# Function to archive a folder
	archive_folder() {
	    local folder=$1
	    local archive_name="$ARCHIVE_DIR/${folder}_${TODAY}.tar.gz"
	    echo "Archiving $folder at $(date)" >> $LOGFILE
	    tar -czf "$archive_name" -C "$SOURCE_BASE" "$folder" >> $LOGFILE 2>&1
	    if [ $? -eq 0 ]; then
	        echo "Archive of $folder created successfully at $(date)" >> $LOGFILE
	    else
	        echo "Archive of $folder failed at $(date)" >> $LOGFILE
	    fi
	    echo "$archive_name"  # Return the archive path
	}
	
	# Function to backup MySQL database
	backup_mysql() {
	    local mysql_backup_file="$ARCHIVE_DIR/mysql_backup_${TODAY}.sql.gz"
	    echo "Backing up MySQL database $DB_NAME at $(date)" >> $LOGFILE
	
	    # Using mysqldump with password in environment variable for security
	    MYSQL_PWD="$DB_PASSWORD" mysqldump -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" "$DB_NAME" | gzip > "$mysql_backup_file" 2>> $LOGFILE
	
	    if [ $? -eq 0 ]; then
	        echo "MySQL backup completed successfully at $(date)" >> $LOGFILE
	        echo "$mysql_backup_file"  # Return the backup file path
	    else
	        echo "MySQL backup failed at $(date)" >> $LOGFILE
	        return 1
	    fi
	}
	
	# Function to run rsync for an archive
	run_rsync() {
	    local archive=$1
	    local folder=$(basename "$archive" | cut -d'_' -f1)  # Extract folder name from archive name
	    echo "Backing up $folder at $(date)" >> $LOGFILE
	    rsync -av -e "ssh -i $SSH_KEY" "$archive" "haitham@$REMOTE_IP:$REMOTE_DIR/" >> $LOGFILE 2>&1
	    if [ $? -eq 0 ]; then
	        echo "Backup of $folder completed successfully at $(date)" >> $LOGFILE
	    else
	        echo "Backup of $folder failed at $(date)" >> $LOGFILE
	    fi
	}
	
	# Function to delete backups older than 3 days
	cleanup_old_backups() {
	    echo "Cleaning up backups older than 3 days at $(date)" >> $LOGFILE
	    ssh -i "$SSH_KEY" "haitham@$REMOTE_IP" "find $REMOTE_BASE -mindepth 1 -maxdepth 1 -type d -mtime +3 -exec rm -rf {} \;" >> $LOGFILE 2>&1
	    if [ $? -eq 0 ]; then
	        echo "Cleanup completed successfully at $(date)" >> $LOGFILE
	    else
	        echo "Cleanup failed at $(date)" >> $LOGFILE
	    fi
	}
	
	# Create remote directory for today's backups
	ssh -i "$SSH_KEY" "haitham@$REMOTE_IP" "mkdir -p $REMOTE_DIR" >> $LOGFILE 2>&1
	
	# Loop through folders, archive them, and run rsync
	for folder in "${FOLDERS[@]}"; do
	    archive=$(archive_folder "$folder")
	    if [ -f "$archive" ]; then
	        run_rsync "$archive"
	        rm -f "$archive"  # Clean up the local archive after rsync
	    fi
	done
	
	# Backup MySQL database and send to remote server
	mysql_backup=$(backup_mysql)
	if [ -f "$mysql_backup" ]; then
	    run_rsync "$mysql_backup"
	    rm -f "$mysql_backup"  # Clean up the local MySQL backup after rsync
	fi
	
	# Cleanup old backups
	cleanup_old_backups
	
	# Clean up temporary archive directory
	rm -rf "$ARCHIVE_DIR"
	```