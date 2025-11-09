#### Sysbench tool 
This is General tool which used to bench marking Linux servers. 
1. Installation 
	```bash 
	sudo apt install sysbench
	```
2. CPU test 
	```bash
	sysbench cpu --cpu-max-prime=20000 run
	```
	The most important output of this command is `events per second`
	<span style="color:rgb(255, 0, 0)">Note</span> **Higher values** indicate better CPU performance.
3. Test cpu 
	```bash 
	sysbench memory run 
	```
#### Server Load 
- Using `h2load` tool with the following parameter 
	```bash 
	h2load -n 1000 -c 50 -m 10 https://bluebook.iceage.me.uk 
	```

	- `n` Number or requests. 
	- `c` Concurrent Clients. 
	- `m` Maximum concurrent stream per connection. 
#### Storage Bechmarking 
- With `dd` command 
	- For write 
		```bash 
		dd if=/dev/zero of=testfile bs=1G count=1
		```
	- For read 
		```bash 
		dd if=testfile of=/dev/null bs=1G count=1 
		```
#### Server Metrics (sar)
- Installation 
	```bash
	sudo apt install sysstat
	```
- Enable data collection by editing `/etc/default/sysstat` (set ENABLED to "true")
- Configure collection intervals in `/etc/cron.d/sysstat` and make it run every minute 
	```bash 
	* * * * * root command -v debian-sa1 > /dev/null && debian-sa1 1 1
	```
- Edit the following file `/usr/lib/systemd/system/sysstat-collect.timer` 
	```bash
	# Read the current configuration 
	sudo systemctl cat sysstat-collect.timer
	# Edit it  
	vim /usr/lib/systemd/system/sysstat-collect.timer
	# Show status 
	sudo systemctl status sysstat-collect.timer
	```
- Then restart services 
	```bash
	sudo systemctl restart sysstat
	```

- check data 
	- View Load Average 
		```bash 
		sar -q -f /var/log/sysstat/sa[DAY]
		```
	- view memory usage 
		```bash 
		sar -r -f /var/log/sysstat/sa[DAY]
		```
	- View CPU usage 
		```bash 
		sar -u -r /var/log/sysstat/sa[DAY]
		```


#### Top RAM and CPU 
- Ram 
	```bash 
	ps aux --sort=-%cpu 
	```
- CPU
	```bash 
	ps aux --sort=-%mem
	```