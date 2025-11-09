
## Introduction to ceph 

<center> <span style='color:cyan'>**Dynamic Workload need Dynamic Storage**</span> </center> 

* The name "Ceph" is an abbreviation of "*cephalopod*", a class of molluscs that includes the octopus.
* Ceph is Open source and Distributed Storage system.  [ <span style='color:red'>IMP</span>  ]
* Ceph provide all storage type in one solution (All in one)
    1. Object Storage 
    2. Block Storage 
    3. File storage
* Provide 
    1. Excellent Performance
    2. Reliability (High availability, Fault tolerance)
    3. Scalability 
- Architecture 
![[cepharchitecure.webp]]

* Ceph storage Cluster consist of multiple types of daemons 
	1. Ceph Monitor (MON)
		- Maintain cluster state, authentication, and configuration. 
	2. Ceph Manager (MGR)
		- Provides monitoring, metrics and external integrations. 
	3. Ceph Object storage Daemons (OSD). 
		- Handel Data storage, replications and recovery.
	4. Ceph Metadata Server (MDS)
		- Manages metadata for CephFS (Ceph's POSIX-compliant filesystem).
	5. Ceph RADOS Gateway 

- Erasure Coding (EC)
	- Data Protection and redundancy technique. 
	- Divide Data into chunks coding them with redundant parity data and store them in different nodes. 
- Philosophy behind ceph
	- Every component must be scalable.  
	- There is can be no single point of failure. 
	- The solution must be software based, open source and adaptable. 
	- Should run on a commodity hardware. 
	- Every thing should be self manageable. 

- Like Linux "Every thing is a file", In ceph "Every thing is an object"
- Objects are location independent. 
#### Installation 
##### Installation on VMs. 
- You can follow the following documentation to stat installing ceph [Good Tutorial](https://kifarunix.com/install-and-setup-ceph-storage-cluster-on-ubuntu-2204/)
- Prerequisites 
	1. Python3 
	2. Systemd 
	3. Docker 
	4. Time Sychronization (chrony or NTP)
	5. LVM2 for provisioning storage devices. 
- Architecture 
	Create Four Ubuntu 22.04 Virtual Machines with the following specs
	- RAM `6GB`
	- Storage 
		1. Root `40GB`
		2. LVM RAW Disk `60GB0` For OSD Mahcines only. 
###### Installation on VMs 
- <span style="color:rgb(255, 255, 0)">For All Machines </span>
	1. For ALL `OSD` Mahcines Create LVM 🗒(Not needed in ceph-admin machine)
		```bash 
		sudo pvcreate /dev/sdb
		sudo vgcreate ceph-vg /dev/sdb
		sudo lvcreate --size 69.8G --name ceph-lv ceph-vg
		```
	2. Update system 
		```bash 
		sudo apt update 
		```
	3. Create Hosts file 
		```bash 
		sudo vim /etc/hosts
		```
		```txt 
		# CUSTOMIZE IT FOR YOUR ENVIRONMENT
		10.0.0.111 ceph-admin
		10.0.0.112 ceph-mon
		10.0.0.113 ceph-osd1
		10.0.0.114 ceph-osd2
		```

	4. Set time synchronization 
		```bash 
		sudo apt install chrony -y
		vim /etc/chrony/chrony.conf
		pool ntp.kifarunix-demo.com iburst # replace it with your ntp server
		sudo systemctl restart chronyd
		```
	5. Install docker In all mahcines . 

- <span style="color:rgb(255, 255, 0)">In all workers (OSD-MON) Nodes</span> 
	1. Enable Root Login from ceph Admin Node 
		```bash 
		sudo vim /etc/ssh/sshd_config
		```
		add `PermitRootLogin yes` 
		Reload `ssh` service 
		```
		systemctl reload sshd 
		```
	2. Create Root Password 
		```bash 
		sudo su 
		passwd 
		```

- In Ceph Admin node 
	1. Create user 
		```bash 
		sudo useradd -m -s /bin/bash cephadmin
		```
	2. Change Password 
		```bash
		sudo passwd cephadmin
		```
	3. Ensure that there any password need when use `sudo` permission 
		```bash
		echo "cephadmin ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers.d/cephadmin
		```

	4. Verify that 
		```bash 
		visudo -cf /etc/sudoers.d/cephadmin
		```
		If `/etc/sudoers.d/cephadmin: parsed ok` then processed 
	5. change file permission 
		```
		chmod 0440 /etc/sudoers.d/cephadmin
	   ```

	6. Install the latest version from cephadm

		```bash 
		wget -q -O- 'https://download.ceph.com/keys/release.asc' | \
		gpg --dearmor -o /etc/apt/trusted.gpg.d/cephadm.gpg
		echo deb https://download.ceph.com/debian-reef/ $(lsb_release -sc) main \ > /etc/apt/sources.list.d/cephadm.list
		
		```
		```bash 
		apt update 
		apt install cephadm 
		```


	7. Intializae ceph monitor in ceph-admin node 
		```bash 
		su - cephadmin 
		sudo cephadm bootstrap --mon-ip 10.0.0.111
		```
		Don't forget to take note about dashboard username and password. and cluster Id you will use it i the next step

	8. Enalbe `ceph cli` use cluster Id form the previous step. 
		```bash 
		sudo /usr/sbin/cephadm shell \
			--fsid 70d227de-83e3-11ee-9dda-ff8b7941e415 \ 
			-c /etc/ceph/ceph.conf \
			-k /etc/ceph/ceph.client.admin.keyring
		```
		This will opne ceph cli and you can check the status of the cluster with the following command 
		```bash 
		ceph -s 
		```
		Install ceph cli tools 
		```bah 
		sudo cephadm add-repo --release reef
		sudo cephadm install ceph-common
		```

	9. Copy ssh keys to work nodes 
		```bash 
		sudo ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-mon
		sudo ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-osd1
		sudo ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-osd2
		```
	10. Drop into ceph CLI Note change your cluster ID
		```bash 
		sudo /usr/sbin/cephadm shell \
			--fsid df11c04a-4087-11ef-94da-b345cbfdd4dd \
			-c /etc/ceph/ceph.conf \
			-k /etc/ceph/ceph.client.admin.keyring
		```
	11. add worker nodes 
		- List all current nodes in the cluster 
			```bash 
			sudo ceph orch host ls 
			```
		- Add monitor node 
			```bash 
			sudo ceph orch host add ceph-mon
			```
		 The oupt will be 
			```bash 
			Added host 'ceph-mon' with addr '10.0.0.112'
			```
		- change it's lable 
			```bash 
			sudo ceph orch host label add ceph-mon mon/osd0 
			```

	12. Add OSD Node to ceph cluster and label them 
		```bash 
		sudo ceph orch host add ceph-osd1
		sudo ceph orch host add ceph-osd2
		for i in {1..2}; do sudo ceph orch host label add ceph-osd$i osd$i; done
		```

Note there some things you missed them in the last of the documentation. 

> Ceph Operations and Maintenance (Chapter 7)

