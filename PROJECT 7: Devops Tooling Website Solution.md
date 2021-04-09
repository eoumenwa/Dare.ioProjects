# Devops Tooling Website Solution

This project describes a set of DevOps tools that will help a team in day to day activities in managing, developing, testing, deploying and monitoring different projects. This single DevOps Tooling Solution consists of:

1. Jenkins - free and open source automation server used to build CI/CD pipelines.

2. Kubernetes - an open-source container-orchestration system for automating computer application deployment, scaling, and management.

3. Jfrog Artifactory - Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.

4. Rancher - an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.

5. Grafana - a multi-platform open source analytics and interactive visualization web application.

6. Prometheus - An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting      approach.

7. Kibana - Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

Some Definitions

* Network-attached storage (NAS) is a file-level (as opposed to block-level storage) computer data storage server connected to a computer network providing 
  data access to a heterogeneous group of clients. A NAS unit is a computer connected to a network that provides only file-based data storage services to  
  other devices on the network. Although it may technically be possible to run other software on a NAS unit, it is usually not designed to be a general-
  purpose server. For example, NAS units usually do not have a keyboard or display, and are controlled and configured over the network, often using a 
  browser.

  A full-featured operating system is not needed on a NAS device, so often a stripped-down operating system is used. For example, FreeNAS or NAS4Free, both   open source NAS solutions designed for commodity PC hardware, are implemented as a stripped-down version of FreeBSD.

  NAS systems contain one or more hard disk drives, often arranged into logical, redundant storage containers or RAID.

  NAS uses file-based protocols such as NFS (popular on UNIX systems), SMB (Server Message Block) (used with MS Windows systems), AFP (used with Apple     
  Macintosh computers), or NCP (used with OES and Novell NetWare). NAS units rarely limit clients to a single protocol.    
   
* A storage area network (SAN) or storage network is a computer network which provides access to consolidated, block-level data storage. SANs are primarily   used to access data storage devices, such as disk arrays and tape libraries from servers so that the devices appear to the operating system as direct-     attached storage. A SAN typically is a dedicated network of storage devices not accessible through the local area network (LAN).

* Network File System (NFS) is a distributed file system protocol originally developed by Sun Microsystems (Sun) in 1984, allowing a user on a client   
  computer to access files over a computer network much like local storage is accessed. 
  
* In computer networking, Server Message Block (SMB), one version of which was also known as Common Internet File System (CIFS /sɪfs/), is a    
  communication protocol for providing shared access to files, printers, and serial ports between nodes on a network. It also provides an authenticated     
  inter-process communication (IPC) mechanism. Most usage of SMB involves computers running Microsoft Windows, where it was known as "Microsoft Windows  
  Network" before the introduction of Active Directory. Corresponding Windows services are LAN Manager Server for the server component, and LAN Manager  
  Workstation for the client component. 
  
* In computing, the SSH File Transfer Protocol (also Secure File Transfer Protocol, or SFTP) is a network protocol that provides file access, file   
  transfer, and file management over any reliable data stream. It was designed by the Internet Engineering Task Force (IETF) as an extension of the Secure   Shell protocol (SSH) version 2.0 to provide secure file transfer capabilities. The IETF Internet Draft states that, even though this protocol is   
  described in the context of the SSH-2 protocol, it could be used in a number of different applications, such as secure file transfer over Transport Layer   Security (TLS) and transfer of management information in VPN applications.

* In computing, iSCSI is an acronym for Internet Small Computer Systems Interface, an Internet Protocol (IP)-based storage networking standard for   
  linking data storage facilities. It provides block-level access to storage devices by carrying SCSI commands 
  over a TCP/IP network. iSCSI is used to facilitate data transfers over intranets and to manage storage over long distances. It can be used to transmit  
  data over local area networks (LANs), wide area networks (WANs), or the Internet and can enable location-independent data storage and retrieval.
  The protocol allows clients (called initiators) to send SCSI commands (CDBs) to storage devices (targets) on remote servers.
  
* Block-level storage is a concept in cloud-hosted data persistence where cloud services emulate the behaviour of a traditional block device, such as a  
  physical hard drive. It is a form of network-attached storage (NAS).
  Storage in such is organised as blocks. This emulates the type of behaviour seen in traditional disks or tape storage through storage virtualization.   
  Blocks are identified by an arbitrary and assigned identifier by which they may be stored and retrieved, but this has no obvious meaning in terms of  
  files or documents. A filesystem must be applied on top of the block-level storage to map 'files' onto a sequence of blocks.

  Amazon EBS (Elastic Block Store) is an example of a cloud block store. Cloud block-level storage will usually offer facilities such as replication for   
  reliability, or backup services.
  
* Object storage (also known as object-based storage) is a computer data storage architecture that manages data as objects, as opposed to other storage       architectures like file systems which manages data as a file hierarchy, and block storage which manages data as blocks within sectors and tracks. Each     object typically includes the data itself, a variable amount of metadata, and a globally unique identifier. Object storage can be implemented at multiple   levels, including the device level (object-storage device), the system level, and the interface level. Object storage systems allow retention of massive   amounts of unstructured data. Object storage is used for purposes such as storing photos on Facebook, songs on Spotify, or files in online collaboration   services, such as Dropbox.

## Solution components:

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub

![image](https://user-images.githubusercontent.com/78841364/112709900-76d5ec80-8e93-11eb-9ea8-78d8fac70f43.png)

Based on the set up above, we require four Red Hat instances and one Ubuntu instance 

## Step 1 - Prepare NFS Server
1. Spin up a new EC2 instance with RHEL Linux 8 Operating System.
2. Configure LVM on the Server based on Project 6 experience - To do this, I created an EBS volume of 15GB and attached it to the NFS server instance.
3. Instead of formating the disk as ext4, I formatted as xfs

Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs
Create mount points on /mnt directory for the logical volumes as follow: Mount lv-apps on /mnt/apps - To be used by webservers Mount lv-logs on /mnt/logs - To be used by webserver logs Mount lv-opt on /mnt/opt - To be used by Jenkins server in Project 8

## Step 2 — Configure the database server

1. Install MySQL server
$ sudo apt install mysql-server -y
2. Create a database and name it tooling 
mysql> create database tooling;
Query OK, 1 row affected (0.01 sec)

3. Create a database user and name it webaccess
mysql> CREATE USER `webaccess`@`%` IDENTIFIED WITH mysql_native_password BY 'emmanuel';
Query OK, 0 rows affected (0.01 sec)

4. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr
mysql> GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

I will have to change the % to the subnet cidr

![image](https://user-images.githubusercontent.com/78841364/113601058-dd36d980-960e-11eb-9d71-136cf8ab3d01.png)


## Step 3 — Prepare the Web Servers

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in this case - NFS Server and MySQL database. We know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers will use - we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.

During the next steps we will do following:
Configure NFS client (this step must be done on all three servers)
Deploy a Tooling application to our Web Servers into a shared NFS folder
Configure the Web Servers to work with a single MySQL database

1. Launch a new EC2 instance with RHEL 8 Operating System.

2. Install NFS client
sudo yum install nfs-utils nfs4-acl-tools -y

3. Mount /var/www/ and target the NFS server’s export for apps
   sudo mkdir /var/www
   sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
 
4. Verify that NFS was mounted successfully by running df -h
  ![image](https://user-images.githubusercontent.com/78841364/113607377-20954600-9617-11eb-938a-81eb9c83471e.png)

4.1 Make sure that the changes will persist on Web Server after reboot:
   sudo vi /etc/fstab

   Add following line

   <NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
   <NFS-Server-Private-IP-Address>:/mnt/apps                   /var/www          nfs     defaults        0 0

5. Install Apache
   sudo yum install httpd -y

![image](https://user-images.githubusercontent.com/78841364/113610588-576d5b00-961b-11eb-908a-7792427c3bcc.png) Shot 5a


![image](https://user-images.githubusercontent.com/78841364/113610392-0d847500-961b-11eb-9fc8-8796a5c60a9b.png) Shot 5b


6. Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files    - it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other    Web Servers.
   
   Creating test.txt from the NFS server ( in  shot 5b above, the created file can be seen in shot 6a)
   ![image](https://user-images.githubusercontent.com/78841364/113610809-9e5b5080-961b-11eb-8d70-6edbc4dfd6d1.png) Shot 6a

7. Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step 4.1 to make sure the mount point will    
   persist after reboot.

  <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0
  <NFS-Server-Private-IP-Address>:/mnt/logs                   /var/log/httpd          nfs     defaults        0 0

sudo mount -a
sudo systemctl daemon-reload

8. Fork the tooling source code from Darey.io Github Account to your Github account.

![image](https://user-images.githubusercontent.com/78841364/113620935-fba9ce80-9628-11eb-8897-abeca378ac66.png)

Paste the public IP address of the webserver into the browser to confirm the apache default page

![image](https://user-images.githubusercontent.com/78841364/113621488-ac17d280-9629-11eb-965d-027274599ab5.png)

9. Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

cd tooling/

sudo cp -R html/. /var/www/html/

Disable selinux using 
sudo setenforce 0

Refresh the webpage or access the it using the instance public or private IP address

![image](https://user-images.githubusercontent.com/78841364/114185599-e88e4b80-9913-11eb-8b21-ef75b706b052.png)




Note difference between mv /var/log/httpd /var/log/httpd.bak and mv /var/log/httpd/. /var/log/httpd.bak
/var signifies go to root/var.  httpd/. means close directory and copy only contents without making folder

Taking a short break. Hopeful to continue later tonight

Notice the error in the output. Can you confirm if you configured local DNS to resolve web to an IP address

You should use  the IP addresses of the servers instead of Web1 and Web2

Exactly, or you can use the IP address directly. Just be aware that when IP changes your configuration will not work again
What i do every time is to attach an elastic IP to every server i create just to prevent the IPs from changing and breaking the setup

Now thinking of exploring vsc code as one platform for editing and also connecting to my instances.

Looks like VSC is only an alternative to powershell in windows. Fruther research needed.
In trying to set up three webservers, how can set up one and have commands replicated in three. For the sake of practice I will have it done one after the other. 
C Run chmod 700 /var/log/httpd and restart. Try to unmount and proceed

New steps
sudo chmod -R 777 /mnt/logs on NFS
sudo umount -t nfs rw,nosuid 172.xx.23.xxx:/mnt/logs /var/log/httpd on webserver to unmount
sudo mkdir /home/log
sudo cp -R -v /var/log/http/. /home/log
delete content of mnt/logs on nfs
mount again
observe permissions on new httpd
chown
chmod
copy back restart


References
https://en.wikipedia.org/wiki/Network-attached_storage
https://en.wikipedia.org/wiki/Storage_area_network
https://en.wikipedia.org/wiki/Network_File_System
https://en.wikipedia.org/wiki/Server_Message_Block
https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol
https://en.wikipedia.org/wiki/ISCSI
https://en.wikipedia.org/wiki/Block-level_storage
https://en.wikipedia.org/wiki/Object_storage
https://dzone.com/articles/confused-by-aws-storage-options-s3-ebs-amp-efs-explained

  
 
