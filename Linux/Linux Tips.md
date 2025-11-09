#### Important commands 
- search about word inside folder 
	```bash 
	grep -r [word] [folder]
	grep -r haitham /etc
	```

- check file and folder size 
	```bash 
	# File size 
	ll -h 
	# Folder size 
	du -sh folderName
	```

- Create Folder or File with old date 
	```bash 
	touch -d "day, mounth year clock"
	exp 
	touch -d "mon, Fev 2018 8:30:56"
	touch -d "mon, Fev 2018 9:9:45" /tmp/haitham
	```

- Linking files and directories
	```bash 
	# Hard linking 
	ln origianl hardlinkfile
	ulink origianl hardlinkfile 
	#Soft linking 
	ln -s origanalfile_path softlink_pass
	
	```

- First Server configurations 
	1. Change root password 
	2. Create non root user. 
	3. Give non root user root privilege 
		```bash 
		visudo /etc/sudoer.tmp
		# Give user sudoer permissions 
		haitham  ALL=(ALL:ALL) ALL 
		```
	4. Prevent ssh for root user 
		```bash 
		cd /etc/ssh 
		cp sshd_config sshd_config.7-dec-2020
		vim sshd_config
		#change "yes" to "no" in `permitRootLogin`
		systemctl restart ssh 
		```

- Run Script from any path 
	```bash 
	export PATH=$PATH:/new/path 
	```
	To make it permanent add it to `.bashrc` file. 

- Create Archive folder with `tar`
	```bash 
	# Create 
	tar cvzf newfolder.tar folder
	# Extract 
	tar xvzf newfolder.tar 
	```
	
- Live logs 
	```bash 
	tail -f /var/log/nginx/access.log
	```

- Check open ports 
	```bash 
	ss -tulnp
	# t for tcp 
	# u for udb 
	# l for lisend 
	# n Show numeric ports. 
	# p to list all process which use the 
	```

- Quickly create large files 
	```bash 
	# Create a 1GB file with dd (slower but more flexible)
	dd if=/dev/zero of=largefile.img bs=1M count=1024
	```

- compare two directories. 
	```bash 
	# Compare two directories
	diff -rq dir1/ dir2/
	```

- Display the biggest 10 folders in specific directory 
	```bash 
	du -ha | sort -hr | head -n 10 
	```
- Show Status of Cron job 
	```bash 
	grep CRON /var/log/syslog 
	```
- Trace system calls made by a process. 
	```bash 
	strace -p [PID]
	```
- List opened files which associated to process 
	```bash 
	lsof -i :80 
	# :80 to see the process which use port 80. 
	```

#### Bench Marking 
##### Storage. 
- Use simple command `dd` 
	- Writing benchMarking
		```bash 
		dd if=/dev/zero of=testfile bs=1G count=1 oflag=direct
		```
	- Reading benchMarking 
		```bash 
		dd if=testfile of=/dev/null bs=1G count=1 oflag
		```


##### Server Load 
- Using `h2load` tool with the following parameter 
	```bash 
	h2load -n 1000 -c 50 -m 10 https://bluebook.iceage.me.uk 
	```

	- `n` Number or requests. 
	- `c` Concurrent Clients. 
	- 