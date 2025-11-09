<center><h2> System adminstartion</h1></center>

### User and Group Management 

#### General notes 
* In Linux there are privileged users and unprivileged users The default privileged user is root to get more information about users use the following command. 
	```bash 
	id <username> 
	```
* Adding user to system I recommend use `adduser` command. because adduser will ensure that password, home directory is made while adding user to system

* Configuration file of user management default found at the following files 
	```txt 
	 /etc/login.defs
	/etc/default/useradd
	```
* Adding user to sudo group (ubuntu) wheel group (redhat)
	  ````bash
	  usermod -aG sudo <username> 
	  usermod -aG wheel <username>
* To manage sudoer file 
  ````bash 
    visudo
    ````

  give user access to specific commands only edit this file and add the following 

  ````txt
	visudo /etc/sudoers 
	 # 
   haitham ALL=/usr/bin/useradd, /usr/bin/passwd
	````

### Linux File system
* to list all directories to 1 Level 
	```bash 
	tree -L 1
	```
  this command will show you all linux directories 
  * **/bin** contains *bin*aries, default command that come with Operating system.
  * ***/boot*** contains files required for starting your system.
  * ***/dev*** contains *dev*ice files.
  * ***/etc***
  * ***/lib***  where *lib*raries live.
  * ***/opt*** Stands for Optional  which mean that this folder used to install additional software. 

### Bash Scripting 

* there are two types of commands in bash 
  1. built in  (part of the shell it self!)
  2. External which linux search for them with the PATH variable. 
    paths are 
	```txt 
	/usr/bin/
	/usr/sbin
	/bin
	/usr
	```
* begin your code with shebang 
  ```text
	 #!/bin/bash
    ```

* To print all paths in you system 
  ```bash
    echo $PATH
    ```
* add comment use # 
* to print any thing 
  ```bash
    echo "haitham elabd"
    ```
* change file Permission 
  ```bash
  . chmod +x fileanme.sh
    ```
* run script 
  ```sh
  ./filename.sh
  ```
* Variables without  any spaces 
  ```bash
  FN="haitham"
  LN="Elabd"
  echo $FN $LN
  ```
* Command substitution 
  ```bash
  var=`date`
  var2=`pwd`
  ```
* Take variable from user 
  * you can take variable directly on script using 
    ```bash
    echo "my name is $1"
    ```
    and when you run script pass value to this variable like this
    ```bash
    ./file.sh haitham
    ```
  * Take variable any time using `read` command 
    1. use read without any option and variable 
       Create a Bash file with the following script that takes the input from the terminal using **the read** command without any option and variable. If no variable is used with the **read** command, the input value is stored in the **$REPLY** variable. The value of this variable is printed later after taking the input.

       ```bash 
       #!/bin/bash  
       #Print the prompt message
       echo "Enter your favorite color: "  
       #Take the input
       read  
       #Print the input value
       echo "Your favorite color is $REPLY"
       ```
    2. use read with variable 

       Create a Bash file with the following script that takes the input from the terminal using **the read** command with a variable. The method of taking the single or multiple variables using a **read** command is shown in this example. The values of all variables are printed later.

       ```bash 
       #!/bin/bash  
       #Print the prompt message
       echo "Enter the product name: "  
       read product_name
       #Take the input with a single variable
       #Print the prompt message
       echo "Enter the color variations of the product: "  
       #Take three input values in three variables
       read color1 color2 color3
       
       #Print the input value
       echo "The product name is $product_name"  
       #Print the input values
       echo "Available colors are $color1, $color2, and $color3."
       ```
       
    3. The most common and used command to take variable from user is ?
  
* If and If else statment

  1. if: represents the condition that you want to check
  2. then: if the previous condtion is Ture, then execute the following command 
  3. esle: if the previous condition is false, then execute antoher command 
  4. fi: closes the "if, then, else" statement "finish"

  syntax 

  ```bash
  if [ condition ] # don't forget space
  then 
  	<execute command> 
  else
  	< execute another command>
  fi
  ```

* elif

  ```bash
  if [ condition ] 
  then 
  	<execute command>
  elif [ condition ]
  then 
  	<execute another command>
  else 
  	<execute the default command>
  fi 	
  ```

* nested if statement 

  ```bash
  if [ condition1 ]
  then 
  	if [ condition2 ]
  	then
  		<execute command>
  	else
  		<execute command>
  	fi
  fi    
  ```

* while loop

  ```bash
  while [ condition ]
  do
  	command1
  	command2
  	command3
  done
  ```
* while loop on one line 
  ```bash
  while [ condition ]; do command1; done
  ```
* Regular expressions
  *  are sequences of characters that define a search pattern, mainly used for pattern matching within text
  * used with vim, sed & awk.  
  * **Examples** 
    * to search on word that start with cat 
      ```bash
      grep ^cat /usr/share/dict/words
      ```
    * search for word that end with cat 
      ```bash 
      grep cat$ /usr/share/dict/words
      ```
    * to search for exec word
      ```bash
      grep ^cat$ /usr/share/dict/words
      ```
    * **THIS IS ONE IS VERY IMPORTANT** search for word with inside folder 
      ```bash 
      grep -r [word] [folder]
      grep -r haitham  /etc       
      ```
* For loop 
  ```bash
    for var_name in [List]
    do 
	 [ action to perform ]
	 done
    ```
* functions 
  ```bash
   function name(){
  
      commands
    
    } 
   ```
  calling 
  ```bash
    functionname
   ```
* Select Command 

  Select command is used to create simple menu-driven shell script. It provides an interactive way to present a list of options to the user, allowing them to choose from a set of items. 

  syntax 

  ```bash 
  select variable in list
  do 
  	# Statements to executs 
  	# based in user selection 
  done
  ```

  So there is the syntax of creating list 

  ```bash 
  ListName=("Item1" "Item2" "Item3")
  ```

  ```bash 
  #!/bin/bash
  # menu options
  echo "Select an option:"
  options=("Option 1" "Option 2" "Option 3" "Quit")
  
  # Display menu using select
  select choice in "${options[@]}"
  do
      case $REPLY in
          1) 
              echo "You chose Option 1"
              # Perform actions for Option 1
              ;;
          2) 
              echo "You chose Option 2"
              # Perform actions for Option 2
              ;;
          3) 
              echo "You chose Option 3"
              # Perform actions for Option 3
              ;;
          4)
              echo "Exiting..."
              break
              ;;
          *)
              echo "Invalid option. Please choose a number between 1 and 4."
              ;;
      esac
  done
  ```
  

regular variable substitution is $var but it you need to perform some operations for parameter expansion on list hose "${ListName}"



### Real Life Scripting  

1. Taking More than one variable form txt file. 
    1. txt file should be on the following format
       ```txt
         name="haitham"
         age="25"
         job="Engineer"
         ```

    2. import this file in script use 

       ```bash
       source filename.txt
       echo my name is $name and my age is $age my job is $job
       ```

2. To Check file and folder size use 
   * files size 
	```bash 
	ll -h 
	```
   * folder size 
	```bash 
	du -sh /folder 
	```
     -s option for summry
     
     -h for human readabl

3. To Print specific column from txt file use the following script 

   ```bash 
   cat file_name | while read a b c d 
   # wher a b c d stands for culms 
   pip while> do 
   pip while> echo $c 
   pip while> done 
   ```

   * Read content of the file 
   * Create four fields a, b, c, d. 
   * Print all values in c field. 

4. Send Data to the top of file, of course you can send data to button of the file with `echo "data" >> file_name`

   ```bash 
   echo "Data" | cat - file_name > temp && mv temp file_name
   ```

5. using cut command 

   ```bash 
   date | cut -d " " -f 1,2,3
   ```

6. concatenate all files in one file with cat command 

   ```bash 
   cat file1 file2 file3 file3 > Total
   ```

7. Create old file with old date 

   ```bash 
   touch -d "day, mounth year clock"
   exp 
   touch -d "mon, Fev 2018 8:30:56"
   touch -d "mon, Fev 2018 9:9:45" /tmp/haitham
   ```

8. toggle between two command 

   i use this to change Arabic language to English language and vice versa (old method :laughing: ), the logic is respectful you create file on home directory which will work as a flag to toggle between comma

   ```bash 
   #!/bin/
   TOGGLE=$HOME/.toggle
   
   if [ ! -e $TOGGLE ]; then
       touch $TOGGLE
       command1
   else
       rm $TOGGLE
       command2
   fi
   ```
### Linking Files and Directories. 
the links are essentially the pointers which associate to files and directories. there are two types of links 
* soft link or smybolic link
* hard link 
#### Hard Link
 - Two files share the same inode number, this mean that file or directory has one index (inode) but two different names 
 - the  original file must exist, linked file must not exist
   ```bash 
     ln original_file   Hardlink_file
     ```
 - to unlink them use 
   ```bash 
     unlink originla_file Hardlik_file
     ```
#### Soft link 
* Pointer or shortcut. 
* are usually an alternative path (or an alias) for the original file, the two files don't share inode numbers because they do not point to the same data 
  ```bash 
    ln -s Original_file SoftLink_file
    ```
### Text Manipulation Tools 
1. **Cut**
	```bash 
	cut -d -f 
	# d for delemter 
	# f for filed number
	```
   Delimiter is one or more characters that create a boundary between different data items within a record
	```bash 
	cut -d ":" -f 1 /etc/passwd 
	```
2. **Sort**
   used to sort data on a text files 
   ```bash
     sort file_name
     ```
3. **WC**
   is used to determine word count. it is very usefull when you have to know how many lines in some files, Or can I open this file without suspend my terminal. 
### Server first Configuration 
1. Change root password. 
2. Add non root user. 
3. Give non root user user privileges  the in the following path 
	```bash 
	visduo /etc/sudoer.tmp 
	```
   give your user sudoers permission 
   ```txt
     haitham  ALL=(ALL:ALL) ALL 
     ```
4. Prevent ssh for root user, make changes in `sshd_config` file as you learned before if you will make changes in specific file take a copy from it first 
	```bash 
	cd /etc/ssh 
	cp sshd_config sshd_config.7-dec-2020
	vim sshd_config 
	# Change <yes> to <no> in `permitRootLogin`
	```
    After that restart ssh service 
   ```bash 
     systemctl restart ssh 
     ```
### Man Page 
* man divide into sections every section has it's own configuration or for specific user 
  * section 1 : User command (executable programs or shell commands)
  * section 2 : system calls (functions provided by the kernel)
  * section 3 : Library calls (functions within program libraries)
  * section 4 : special files (usually found in /dev)
  * section 5 : File formats and conventions eg /etcpasswd
  * section 8 : System administration command (usually only for root)

  ofcoures you can list them by the following commadn 
	```bash 
	man man 
	```
* structure of getting help 
	```bash 
	man <chapter num> <command> 
	```
### General notes 
* what is kernel 
​	it is an interface between operating system and hardware 
* to fined your shell write 
  ```txt
    echo $0
    # to list all shells in your operating systems 
    cat /etc/shells
    ```
*  Change Machine hostname 
  ```bash 
  hostnamectl set-hostname host_name
  ```
**basic administration tasks**
```bash 
# To list all running process 
top 
# to show all space in hard disk
df -hv
# to see free RAM
free -hm
# to see uptime info 
uptime 
```
#### Run script form any where 
1. Write your script inside specific folder, Then add this folder to PATH 
	```bash 
	export PATH=$PATH:/new/path
	```
2. To make it permanent add this line in `.bashrc` file. 

<span style='color:red'>What is the different between bin and sbin? </span>  
* bin contains essential commands for all users "ls, cp, pwd, mkdir"
* sbin contains system administrations sensitive commands such as "fdisk, ifconfig, shutdown, reboot"
## Storage Management 
### Partitions 
- sequence of creating partition 
	- fdisk to create partion `fdisk /dev/sdb`
	- partprobe to tell kernel that there is change in partition table `partprobe /dev/sdb`
	- fromt this partition `mkfs.ext4 /dev/sdb1` 
	- mount partition  `mount /dev/sdb1 /dir/you/want`
- The default mount point in Linux is `/mnt` 
-  to list all disks and partitions 
   ```bash 
     lsblk
     ```
-  Info about specific partition
   ```bash 
     mount | grep [partition]
     ```
-  types of partitions 
   1. primary : Allowed Disks 4 
   2. logical : Extended partitions form the the fourth primary partition  it always start count form 5 because the first four partitions are reserved to primary partitions 
- to list all disks in your devices 
   ```bash 
   lsblk 
   ```
-  to list all UUID for disks 
   ```bash
   sudo blkid 
   ```
-  fdisk utility works with partitions created using the traditional Master Boot Record (MBR) partitioning scheme and GUID Partition Table GPT scheme  
- to list partitions with fdisk 
   ```bash 
   fdisk -l /dev/sda
   ```
-  to create partition on disk 
   ```bash
   fdisk  /dev/Disk_you_want_modify
   # use help type m to list basic fdisk commands 
   ```
   know that after any change in partitions your should reload the kernal status to read this change be the following  
   ```bash 
   partprobe  /dev/Disk_you_modify	
   ```
   after creating any partition you should format this partition then create file system 

   ```bash 
   mkfs.ext4  /dev/sdb1     # note that you should chose partion which you create or modify it
   ```
-  mount command 
   ```bash 
     mount -t type /dev/partion /mount/point 
     Ex:
     mount -t ext4 /dev/sdb2 /home/haitham/work 
     ```
    you can not add type of partition it will be auto detect 
-  umount command 
   ```bash 
     umount /dev/partition
     Ex:
     unount /dev/sdb6
     ```
-  to mount device automatic or every time machine make reboot you can modify the /etc/fstab file 

   ```bash
   vim /etc/fstab
   dev			mount point		type		dump 	file-systemc-check 
   /dev/sdb3    /work          ext4         0             0
   ```

   if stab file is not found create it with the following command 
   ```bash 
   cat /proc/mounts > /etc/fstab
   ```
   after doing this step check that all thing that you modify is well by using 
   
   ```bash
   mount -a 
   ```
   
-  If you want to reformat an existing partition, logical volume or RAID array, take the following precautions 
    1. Back up any existing data on the partition 
    2. Unmount the partition 

### RAID 

- RAID stands for Redundant Array of independent  Disks,  RAID is a group of disks that used together 
- RAID is a way of logically putting multiple disks together into a single array. we use RAID to have the speed and reliability of expensive disks.   

RAID LEVELS

  1. RAID 0 
  2. RAID 1 
  3. RAID 2

**RAID 0 (strippingتقطيع ):** is taking any number of disks and merging them into one large volume, this will increase the speed of read an write data. the loss of any individual disk will cause complete data lose. 

**RAID 1 (Mirroring):** a pair of identically mirror/copy the data equally across the drivers in the array. if you completely lose drive you can still stay up and running off the additional drive. 

### Logical Volume Management (LVM)

- Is a form of storage visualization that offers a system administrators flexible approach to managing disk  storage
- <span style='color:cyan'>LVM Provides an easy and flexible way to scale storage capacity. </span>  
- Abreviation
  - Pv (physical volumes)  sda, sdb, sdc
  - VG (volume groups)     sda+sdb+sdc
  - LV (Logical volumes)  equivalent to partitions on hard disks 

- Notes 
  - every LV consist from physical extent 
  - to return disk to raw state 
	```bahs 
	dd if=/dev/zero of=/dev/sdb bs=1M count=1
	```
- LVM Steps 
  - Create physical volumes (PV)
  - Create Volume group  (VG)
  - Create logical volume (Lv)
Now we should know any thing a bout Gdisk 
```bash 
# to open Gdisk 
gdisk 
# there is a help such as  fdisk utility u can use it 
# Note that there is a file system that suport LVM you can use L to list all file system or use (8e00)
```

* apply LVM 

  ```bash 
  #create pv 
  pvcreate  /dev/sdb1 /dev/sdc1 /dev/sdd1
  # verify pv 
  pvs
  pvdisplay 
  # Create vg
  vgcreate [group_name] /dev/sdb1 /dev/sdc1 /dev/sdd1
  # verify 
  vgdisplay 
  vgs 
  # crate logical volume form volume Group 
  lvcreate --size 1G --name lv1 [group_name]
  lvdisplay 
  lvs
  # foramt this logical volume
  mkfs.ext4 /dev/group_name/logical_volume_name 
  # mout this volume 
  mkdir /data 
  mount /dev/group_name/logical_volume_name  /data 
  ```
* Note that Lvm package dose not exist in ubuntu put you can install lvm package on it 
#### Extend  Logical Volume 
If current space of lvm finished you can add new partition to this Volume Group
```bash
# create new pv 
pvcreate /dev/sde1 
# exetend volume group 
vgextend [group_name] /dev/sde1 
# exetend logical volume that you wnat to increase it's space 
lvextend --size +100M /dev/groupname/logicalvolume
```
### files compressing and directories archives 
Compression files is to reduce the size of files 
1. zip (filename.zip original_one)
   * compress single file 
     ````bash 
     zip compressed_filename.zip [filename]
     ````
   * Compress a group of files 
     ````bash
     zip compressed_filename.zip file1 file2 file3 
     ````
   * Compress directory 
     ```bash 
     zip -r compressed_filename.zip [foldername]
     ```
1. gzip 
   * is fast to way to compress fies 
   * no hight compression ratio 
     ```bash 
     # to compress file 
     gzip file
     # to uncomperss file 
     gunzip file 
     ```
2. bzip2 
   * is slow way to compress files. 
   * put has a very high compression ratio. 
     ```bash
     # to compress file 
     bzip2 file 
     # to uncompress file 
     bunzip2 file
     ```
directories archives 
​	archive is combine multiple files in one file 
1. this mean to collect directory which contain some directories 

   تار تار بمعنى دوت تار فى الاول

   ```bash
   tar cf etc-backup.tar  /etc/
   # cf for create file 
   ```

   ```bash 
   tar xf etc-backup.tar
   # xf for extract file 
   ```

2. you can make archive and in the same time compress it 

   ```bash 
   tar cfvz etc-backup.tar.zip /etc/
   # cfvz create file verpose zip
   ```

   ```bash
   tar xfvz etc-backup.tar.zip
   ```

### ISCSI 

The Following reference is the best 

https://www.linuxteck.com/how-to-configure-iscsi-target-initiator-on-rhel-centos-7-6/#

ISCSI stands for (Internet Small Computer System Interface), it is an  Industry standard protocol which is mainly used to share the storage device over the TCP/IP layer. Unlike Samba or NFS which work on file  system level whereas iSCSI works only on the block-level device .I recommend to use iscsi over lvm to enable storage extend for the future.

why we should using block-level storage over File Sharing Storage?

1. Lowest possible Latency. 

2. High performance of IOPS (Input \ Output Opeation per second). 

3. High data redundant 

   iSCSI handles client-server architecture. It uses iSCSI components to  communicate with each other. For Client, it uses “initiators” and for  Server, it uses “targets”. Target is responisble to export the disk/block to the iscsi clinet 

packages used

* server "targetcli"
* Client "iscsi-initiator-utils"

**Implementation**

1. List all available blocks 

   ```bash 
   lsblk 
   ```

2. Install target utility package 

   ```bash 
   sudo yum install -y targetcli
   ```

3. Enter the interactive shell of the ISCSI target 

   ```bash 
   targetcli
   ```

4. For help command 

   ```bash 
   help 
   ```

5. type ls and you 

   <img src="../img/iscsils.png" style="zoom: 80%;" />

    see there are 3 options under '/' which are "backstores, iscsi, and  loopback". Under "backstores" there are 4 sub-options which are "block,  fileio, pscsi, and ramdisk" note that backstores may be block device or LVM or a file. 

   Tab is your friend :heart_eyes: :writing_hand: 

6. Create backstore block device 

   ```bash 
   /> /backstores/block create ltecklun1 /dev/sdb
   ```

   lecklun1 is lun name 

7. To see what you create type ls 

8. Create Iscsi Qualified Name `IQN` 

   ```txt 
   /iscsi create iqn.2020-01.reverse_domain_name:server_name
   ```

9. 

### Boot Process In linux 
1. Power on Machine 
2. BIOS Starting  
3. <span style='color:red'>**BIOS**</span> Perform post (Power on self test) check device hardware
4. <span style='color:red'>**BIOS**</span>  Find Bootable devices. 
5. Load IPL "Initial Program Loader" to run boot loader.  GRUB "Grand unified bootloader "
6. GRUB Start kernel 
7. Kernel start systemd
8. systemd call all services. 
   ![](../img/02-linuxboot.png)
### Process management 
1. every process has process ID   <-------------------> PID
2. every process have parent process ID <------------> PPID
3. the first process that created on linux
   4. In the Last days it was init.d 
   5. in current days it is systemd  it's PID num is 1
6. to list all running process in your device 
   `````bash 
     ps aux 
     # au for all user
     `````

7. to kill specific process 
   ``````bash 
   kill PID
   ``````

8. to list all kill signal 
   `````bash 
   kill -l 
   `````
9. to force kill process 
   ```bash 
     kill -9 PID
	 ```
10. to list all process in tree shape 
   ```bash 
   pstree
   ```
11. find process id 
    ```bash
    pgrep  firefox 
    ```
12. to run program or process in the back gourd use & at the end of the process 
    ```bash 
    firefox &
    ```
    or use ctrl+z this mean this program will be suspend until trun it in the fg in the back ground
13. list all runing process in the back ground 
     ```bash 
      jobs 
      ```


14. if you want to restore any program that you run in the back ground type

       fg	%1
       ```

15. To list the high process that take the device resources 

     ```bash
      ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head
     ```

     note that please haitham make is code as a  alias in .bashrc file or memorize it 

     ````bash 
     nano /home/haitham/.bashrc
     hps= ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head
     ````

#### Searching and locate files 
1. locate command binary location 
	```bash 
	whereis <command> 
	```
2. search files with locate 
	```bash 
	locate file_name
	```
   but some times there is no output this mean that your database need to update to update it write the following command 
	```bashsh 
   updatedb
   ```

3. the second utility find 
   ```bash 
   # search in the current location 
   find -iname file_naem
   # search specific location 
   find /any/path  -iname file_name 
   # search the all file system 
   find / -iname fiel_name
   ```

4. to copy result of any command and put it in specific folder 

   ```bash 
   find /etc/ -iname network  -exec cp {} work/ \;
   ```
   command explanation 
   * first part is known
   * Second part 
     ````bash 
     -exec cp {} work/ \;
     # -exec \; This is command and it's termination. 
     # {} is a placeholder which will contain the founded file and put it inside work dir. 
     ````

5. grep command you can use it if front of the command 
   ```bash 
   grep root /etc/passwd 
   ```
6. cut command s
   ```bash 
   cut -d ":" -f 1 /etc/p
   ```
### Package management 

 * Using rpm, you can install, update, query, validate, and remove RPM packages 
   Most RPM packages are depend on some other software [library, executables, and so on] 
 *  all configurations file located in the following directory 
    ```txt
    /etc/yum.repos.d 
    ```
 * Example of RPM package
   ````txt 
   mailcap.2.4.41.2.el7.noarch.rpm 
   ##################################
   mailcap: package name
   2.4.41: package version 
   2 : release
   el7: Enterprise Linux 7   
   ````
   noarch: No architecture this mean this software comptiable with many of architecture this section may be x86 or x64 depending on supported architecture. 
 * Install packages with RPM 
    1. download package which you want to install 
    2. install package with 
        ```bash 
        rpm -ivh package_name
        # -i == --install
        # -v used to more output verpose 
        # -h to show percentage of installation 
        ```
    3. to remove package 
        ```bash 
        rpm -e pcakage_name 
        # -e == --erase
        ```
    4. to search for specific package 
        ```bash
        rpm -qa |grep pakcage_name
        ```
    5. if you already download package and want to install it use 
        ```bash 
        yum localinstall package.rpm
        ```
    6. GPG key 
         GNU Privacy Guard key providing a means of secure communication and data integrity verification.
    
        ```bash
        rpm -K packag.rpm
        ```
    
    
    

**Create yum server.** 

 you will need to machine one as client and the second one is server (yum server). 

Install package vsftpd which will convert machine into ftp server 

```bash 
sudo yum install vsftpd
systemctl enable vsftpd
systemctl start vsftpd
```

*ftp default folder sharing*

```txt 
/var/ftp/pub/
```

Every packages has it's own meta data xml files, which is responsible for make installation easier, so to create meta data use package name `createrepo`

```bash 
sudo yum install createrepo
createrepo -v /var/ftp/pub/
```

install ftp client on client machine and test it 

```bash 
yum ins
```

**RPM database**

all rpm database packages stored in 

```txt
/var/lib/rpm 
```

### scheduling scripts and process 

* use cron-job utility 

* steps 

  1. write your script which you want to automate, but don't forget make it executable 

  2. open crontab  eddint file 

     ```bash 
     crontab -e
     ```

  3. syntax 

     ```txt
     *		*		  *					*		*
     minuit	hours 	day of month	mounth	day-of-the-week 
     * * * * * /home/haitham/test.sh
     ```

* examples 

  ```bash 
  30 * * * * /home/haitham/test.sh   # this script will be run every 30 minuit 
  30 1 * * * /home/haitham/test.sh # this script will be run every day at 1.30	
  ```


## Linux Security 

### SELinux 

* developed by the National Security Agency (NSA)

* Linux Kernel Security Module. which define access control for application, process and files. 

  ![](../img/selinuxmodule.png)

  

* Processes and users  only have the minimum required privileges.

* Every System call is denied unless it has been specifically allowed. 

<span style='color:cyan'>**Terminologies**</span> 

1.  **Policy** : A <u>collection of rules</u> that define which source has access to which target
2. **Source Domain** : The object that is trying to access a target. Typically a user or a process.
3. **Target Domain** : The thing that a source domain is trying to access. Typically a file or a port.
4. **Context** : A security label that is used to categorize objects in SELinux.
5. **Rule** :  A specific part of the policy that determines which source domain has which access 
               permissions to which target domain.
6. **Label** :  Same as a context label, defined to determine which source domain has access to 
                which target domain.

<span style='color:red'>**NOTE That**</span> Changing between SELinux enabled mode and SELinux disabled mode requires a reboot of your system. The reason is that SELinux is a feature that is deeply interwoven with the Linux kernel. There are two modes while SELinux is on 

1. **<span style='color:red'>Enforcing</span>  mode** 

   SELinux is fully operational and <span style='color:red'>enforcing</span>  all SELinux rules in the policy

2. **Permissive mode**

   All SELinux-related activity is logged, but no access is blocked. This makes SELinux permissive mode an excellent mode to do troubleshooting. All logs are save in the following location 

   ````txt 
   /var/log/audit/audit.log
   ````

To toggle between this modes 

````bash 
vim /etc/sysconfig/selinux
````

Toggle between permissive and enforcing mode temporarily 

```bash 
# SELinux in permissive mode
setenforce 0
# SELinux in enforcing mode
setenforce 1 
```

See current selinux status 

````bahs 
sestatus -v 
````

####  Context Labels

Security attributes assigned to system objects (Files, process, Socketes)

To see current context settings on the objects (Users, Files, Directories, Ports)

````bash 
ls -Z 
ps Zaux 
````

**User**: The user can be recognized by _u
**Role**: The role can be recognized by _r 
**Type**: The type context can be recognized by _t <span style="color:red"> **Important**  </span> 

As an administrator, you need to know how to set context types. You can set these context types on files and directories and other objects such as network ports. You can use two commands to set context type:

1. **chcon** : This command is for use in specific cases only and normally should be avoided. because if 
                your file system is relabeled, all changes applied with chcon are lost. 

2. **semanage** :  writes the new context to the SELinux policy To set context using semanage, you first need to find the appropriate context  An easy way to find the appropriate context is by looking at the default context settings on already-existing items. If you want to change the context for a web server, for example, type

   ```bash 
   ls -Z /var/www/
   ```

   To set this context type to any new directory that you want to be accessible by the Apache
   web server, use the following command

   ````bash 
   semanage fcontext -a -t httpd_sys_content_t "/mydir(/.*)?"
   ````

   Apply the policy to file system as the follow

   ````bash 
   restorecon -R -v /mydir
   ````

   You’ll see that the new context is now applied, which allows the httpd process to access the
   directory

   <span style="color:green"> Note </span>you don't need to remember semange command refer to the following man 

   ````bash   
   man semanage-fcontext 
   # search for /examples 
   ````

### Linux Firewall 

Firewall is used to limit traffic coming form outside the server or going out of server. Firewall is implemented in the Linux kernel by means of (بمعرفة ) the netfilter subsystem. Netfilter allows kernel modules to inspect every incoming, outgoing, or forwarded packet and act upon such a packet by either allowing it or blocking it. 

#### Iptables 

command-line firewall utility that uses policy chains to allow or block  traffic that will be enforced by the linux kernel’s netfilter framework, there are three types of structure of tables (tables, chains & Targets). 

Network traffic is made up of packets. Iptables identifies the packets  received and then uses a set of rules to decide what to do with the  packets that matches.

**Tables**

1. Filter 

   This is default table. It consists of following default chains:

   * INPUT           handle all traffic come from out side server. 
   * FORWARD   packets routed through system. 
   * OUTPUT      handle all traffic exit from your server. 

2. NAT 

3. RAW

4. Mangle 

##### Demo 

1. check if IP tables is installed or not 

   ```bash
   rpm -qa | grep iptables-services
   ```

2. Install it with the following 

   ```bash 
   apt install iptables-services 
   ```

   Note that you can't start iptables and firewalld service in the same time. 

3. stop firewalld 

   ```bash 
   sudo systemctl stop firewalld 
   sudo systemctl mask firewalld 
   ```

4. start iptables services 

   ```bash 
   sudo systemctl start iptables
   sudo systemctl enable iptables 
   ```

5. Basic syntax 

   ```bash 
    sudo iptables [option] CHAIN_rule [-j target]
   ```

6. List all rules 

   ```bash 
   iptables -L 
   ```

7. Remove all rules. or Flush them 

   ```bash 
   iptables -F 
   ```

8. Block Specific IP Address in IPtables Firewall

   ```bash 
   iptables -A INPUT -s 192.168.72.128 -j DROP
   iptables -A INPUT -p tcp -s 192.168.72.128 -j DROP
   ```

9. REJECT traffic from a range of IP addresse

   ```bash 
    sudo iptables –A INPUT –m iprange ––src–range 192.168.72.1–192.168.72.255  -j  REJECT
   ```

10. Unblock IP Address in IPtables Firewall

    ```bash 
    iptables -D INPUT -s 192.168.72.128 -j DROP
    -D for Delete chain 
    ```

11. Allow traffic on specific port 

    ```bash 
    sudo iptables –A INPUT –p tcp  ––dport 80  –j ACCEPT
    sudo iptables –A INPUT –p tcp  ––dport 22  –j ACCEPT
    sudo iptables –A INPUT –p tcp  ––dport 443 –j ACCEPT
    ```

12. Block specific port 

    ```bash 
     iptables -A OUTPUT -p tcp --dport 80 -j DROP
    ```

    

#### Firewalld 

**Firewalld** is a system service that can configure firewall rules by using different interfaces. 
Firewalld use **Zone** concept to make firewall management easier,  **Zone** is  a collection of rules that are applied to incoming packets only by default. and no filtering happens on outgoing packets.  

<span style="color:cyan"> Note </span> Use zone in servers that have multiple interfaces, but in server that have on Interface use default one. 

**Zones Table** 

| Zone Name | Default settings                                             |
| --------- | ------------------------------------------------------------ |
| block     | Incoming network connections are rejected with an “icmp-host-prohibited” message. Only network connections that were initiated on this system are allowed. |
| dmz       | For use on computers in the demilitarized zone. Only selected incoming connections are accepted, and limited access to the internal network is allowed. |
| public    | For use in public areas. Other computers in the same network are not trusted, and limited connections are accepted. This is the default zone for all newly created network interfaces. |

**Command Lines**

1. To get default zones 

   ````bash 
   firewall-cmd --get-default-zone 
   ````

   Note that Puplic zone is the deafult zone in firewall 

2. List available services in your server 

   ````bash 
   firewall-cmd --get-services 
   ````

3. Add specific service in firewall in permanent state. 

   `````bash 
   firewall-cmd --add-service [services_name] --permanent 
   `````

4. List or verify services 

   ````bash  
   firewall-cmd --list-all
   ````

5. Add Specific port permanently 

   ````bash 
   firewall-cmd --add-port=2202/tcp --permanent 
   # You should reload the daemon 
   firewall-cmd --reload
   ````

   

6. To get default zones 

   ```bash 
   firewall-cmd --get-default-zone 
   ```

Note that Puplic zone is the default zone in firewall. 

List available services in your server 

````bash 
firewall-cmd --get-services 
````

Add 


# Track two "linux trouble shotting"
### Understand boot process

there are tow stages of booting 

1. before operating system booting 		2. after operating system booting 

   

After push power button , power circle run BIOS (basic input output system) and triger POST operation (Power on self test)

then BIOS search and find bootable devices in your machine (hard, flash, CD) and compare them with it's settings the second steps after that is trigger or load IPL Initial Program Loader 

steps 

1.  push power button
2.  Start BIOS
3.  BIOS start POST operation 
4.  BIOS search for boot able devices in your machine 
5.  start  IPL  sart GRUB 
6.  GRUB  load kernel 
7.  kernel start systemd process 
8.  systemd start all services 

#### Boot loader (Grub)

* Grand unified Boot loader 

* Allows  user to select more than one operating system or more than one kernel 

* Allow the user to pass arguments to kerenel 

* configuration file location 

  1. on traditional BIOS-based machines 

     ```bash 
     /boot/grub2/grub.cfg
     ```

  2. in UEFI machines 

     ```bash
     /boot/efi/EFI/redhat/grub.cfg 
     ```

     

* grub.cfg is generated during installation, and is automatically updateed by grubby each time a new kernel is installed 

* it can manually generated with utility called `grub2-mkconfig` in redhat this tool use template which locate in  but in ubuntu the tool name is different `grub-mmconfig`

  ```bash 
  /etc/grub.d/
  ```
  
* kernel boot after grub so what is the name of disks that grub can deal with them ?

  hd0, msdos1     > disk one partition one 
  
  hd0, msdos2 	  > disk one partition two 
  
  hd1, msdos1		> disk tow partition one 
  
* to make permanent configuration edit the following configuration file 

  ```bash 
  vim /etc/grub2.cfg # is a sympolic link form /boot/grub2/grub.cfg
  vim /etc/sysconfig/grub
  # if yuo make any changes here you should regenerate grub.cfg file 
  grub2-mkconfig -o /boot/grub2/grub.cfg
  ```

* to regenerate grub2.cfg 

  ```bash
  grub2-mkconfig -o /boot/grub2/grub.cfg
  ```

  

#### change root password 

  1. reboot your machine and on the GRUB 2  boot screen, press the `e` key to interrupt the boot process. 

  2. search for line that start with Linux and end with quite or rhgb (Red Hat graphical boot) and add the `rd.break` in the end of this line 

     and add 'rd.break' before initrd16 

  2. ctrl+x to start kernel with changed parametres 

  3. remount `/sysroot`, remount as  writable allows you to change the password 

     ```bash 
     mount -o remount,rw /sysroot
     ```

  4. change your root files 

     ```bash 
     chroot /sysroot/
     ```
     
   5. change your passwd 

      ```bash 
      passwd
      ```

   6. Enable the SELinux relabeling process on the next system boot. 

      ```bash 
      touch /.autorelabel
      ```

   7. exit form that user 

      ```bash 
      exit 
      ```

   8. exit switch_root prompt 

      ```bash 
      exit 
      ```

   9. wait until the SELinux relabeling process is finished. 

* if there is any problem with selinux 

  1. set selinux 0 in kernel file 

     ```bash 
     utf8 setlinux=0
     ```

  2. reboot your machine 

  3. remove selinux-policy and re install it 

     ```bash 
     yum remove selinux-policy
     yum install selinux-policy 
     ```




#### Kernel

1. kernel location in 

   ```bash
   /boot
   ```

2. kernel divide into files 

   * kernel --> core 
   * initramfs --> models and drivers 

### Systemd 

1. start all process in parrallel 

2. consist from unit-files

   ```bas
   ls /etc/systemd/system
   ```
   
3. to start services 

   ```bash
   systemctl start [service_name]
   ```

4. to stop services 

   ```bash 
   systemctl stop [service_name]
   ```

5. to restart service 

   ```bash 
   systemctl restart [service_name]
   ```

6. to make sure that specific service will start during device boot you can enable it 

   ```bash 
   systemctl enable [service_name]
   ```

#### Systemd tragets 

1. target units file ends with the .target file extension, for example, the graphical.target unit which is used to start a graphical session

2. to list all systemctl targets 

   ```bash 
   systemctl list-units --type target
   ```

3. viewing the default target 

   * default target unit is represented by the /etc/systemd/system/default.target

   * to determine which target unit is used by default 

     ```bash 
     systemclt get-default 
     ```

   * to list all loaded units regardless of theeir state

     ```bash 
     systemctl list-units --type target --all
     ```

   * to list all currently loaded target units 

     ```bash 
     systemctl list-units --type target 
     ```

4. to changing default target to be command line or disable graphical 

   ```bash 
   systemctl set-default multi-user.target
   ```

   you should reload your machine to show changes 

5. to changing default target to be graphical 

   ```bash
   systemctl set-default graphical.target
   ```

   you should reload your machine to show changes 

6. you can move between graphical and multi-user by using isolate command to set changes on the fly 

   ```bash 
   systemclt isolate graphical.target
   
   ```

### Quota management 
 * Enable quota management in linux is basically a four step process 
    1. Enable quota management for groups or users in `/etc/fstab` 
    2. Remount the filesystem or check mount status with `mount -a `command 
    3. Create quota database and generate disk usage table 
    4. Assign quato policies 
 * Enable Quota in fstab file 

  ```txt
  /dev/sdb1    /mount/point      fs       defaults,usrquota,grpquota   
  ```
 * Remount file system 

  ```bash 
  mount -a 
  ```
 * Create Quota Database files 

 ```bash
  quotacheck -cvug 
 ```
 * Add quato limits per user 

  ```bash 
  edquota <username> 
  ```

 * enable quota 

  ```bash 
  quotaon /mount/point
  ```
 * check the Disk quota usage for users and groups using requota 

  ```bash 
  repquota /mount/point
  ```

### Mount USB from termainal 

 There are four steps in terminal to manage usb 
    1. Detect USB 
        ```bash 
        sudo fdsik -l 
        ``` 
    2. Create Custom mountpoint 
        ```bash 
        mkdir /media/haitham-usb
        mount /dev/sdb /media/haitham-usb 
        ``` 
    3. Acccess USB 



# Services 

### DNS 

The Domain Name System (DNS) is an application–layer protocol that is part of the standard TCP/IP protocol suite. This protocol implements the DNS naming service, which is the naming service that is used on the Internet.
<center> <img src="../img/dns.png" style="zoom:67%;" /> </center>

the domain name system of the INTERNET works in a inverted tree structure. At the top of the tree is the root name server .The root server is followed by TLD’s or Top Level Domains,and then TLD’s are followed by SLD’s or Second Level Domains. All of these are separated by dots


### Entering Recovery Mode In Ubuntu 

1. Start your pc (power on) 
2. Type or Enter Shift 
3. Esc to select advanced option 
4. Chose Root drop 
