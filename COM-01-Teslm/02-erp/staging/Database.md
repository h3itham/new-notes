1. Installation and setup script 
	```bash 
	#!/bin/bash
	
	# Configuration variables
	DB_NAME="erp_db"
	DB_USER="erp_admin"
	DB_PASSWORD="teslmpassword123!"
	POSTGRES_VERSION="15"  # Change to your preferred version
	
	echo "=== PostgreSQL Installation and Setup Script ==="
	
	# Update package list
	echo "Updating package list..."
	sudo apt update
	
	# Install PostgreSQL
	echo "Installing PostgreSQL..."
	sudo apt install -y postgresql postgresql-contrib
	
	# Start and enable PostgreSQL service
	echo "Starting PostgreSQL service..."
	sudo systemctl start postgresql
	sudo systemctl enable postgresql
	
	# Wait a moment for PostgreSQL to fully start
	sleep 2
	
	# Create database user with password
	echo "Creating database user: $DB_USER"
	sudo -u postgres psql -c "CREATE USER $DB_USER WITH PASSWORD '$DB_PASSWORD';"
	
	# Create database
	echo "Creating database: $DB_NAME"
	sudo -u postgres psql -c "CREATE DATABASE $DB_NAME OWNER $DB_USER;"
	
	# Grant privileges
	echo "Granting privileges..."
	sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE $DB_NAME TO $DB_USER;"
	
	# Optional: Allow password authentication (modify pg_hba.conf)
	echo "Configuring authentication..."
	PG_HBA_FILE=$(sudo -u postgres psql -t -c "SHOW hba_file;" | xargs)
	sudo sed -i '/^local.*all.*all.*peer/s/peer/md5/' "$PG_HBA_FILE"
	sudo sed -i '/^host.*all.*all.*127.0.0.1/s/ident/md5/' "$PG_HBA_FILE"
	
	# Reload PostgreSQL configuration
	echo "Reloading PostgreSQL configuration..."
	sudo systemctl reload postgresql
	
	# Test connection
	echo ""
	echo "=== Setup Complete ==="
	echo "Database Name: $DB_NAME"
	echo "Database User: $DB_USER"
	echo "Database Password: $DB_PASSWORD"
	echo ""
	echo "Testing connection..."
	PGPASSWORD=$DB_PASSWORD psql -h localhost -U $DB_USER -d $DB_NAME -c "SELECT version();"
	
	echo ""
	echo "Connection string: postgresql://$DB_USER:$DB_PASSWORD@localhost:5432/$DB_NAME"
	```