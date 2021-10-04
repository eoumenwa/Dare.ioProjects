
Web Solution With WordPress
Step 1 — Prepare a Web Server
1.	Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in  the same AZ as your Web Server EC2, each of 1 GiB.
2.	Attach all three volumes one by one to your Web Server EC2 instance 

3.	Open up the Linux terminal to begin configuration
4.	Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there - their names will likely be xvdf, xvdh, xvdg.
 
5.	Use df -h command to see all mounts and free space on your server
 
6.	Use gdisk utility to create a single partition on each of the 3 disks


a.	sudo gdisk /dev/xvdf
 
 
b.	sudo gdisk /dev/xvdg
 
c.	sudo gdisk /dev/xvdh 
7. 	Use lsblk utility to view the newly configured partition on each of the 3 disks.
 
8.	Install lvm2 package using sudo yum install lvm2.  
 


9.	Run sudo lvmdiskscan  command to check for available partitions.
 
Note: In Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.
10.	Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
sudo wipefs -a /dev/xvdf && sudo pvcreate /dev/xvdf
sudo wipefs -a /dev/xvdg && sudo pvcreate /dev/xvdg
sudo wipefs -a /dev/xvdh && sudo pvcreate /dev/xvdh

 

11.	Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
sudo vgcreate webdata-vg /dev/xvdh /dev/xvdg /dev/xvdf
 
12.	Verify that your VG has been created successfully by running sudo vgs
 
13.	Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
sudo lvcreate -n apps-lv -L 1.44g webdata-vg
 
sudo lvcreate -n logs-lv -L 1.44g webdata-vg
 
sudo lvs
 

14.	Verify the entire setup
sudo vgdisplay -v #view complete setup - VG, PV, and LV
 
 

sudo lsblk 
 
15.	Use mkfs.ext4 to format the logical volumes with ext4 filesystem
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
 

sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
 
16.	Create /var/www/html directory to store website files



sudo mkdir -p /var/www/html
 
17.	Create /home/recovery/logs to store backup of log data
sudo mkdir -p /home/recovery/logs
 

18.	Mount apps-lv logical volume on /var/www/html
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
 
19.	Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
sudo rsync -av /var/log /home/recovery/logs
 
 
20.	Mount /logs-lv logical volume on var/log . (Note that all the existing data on /var/log will be deleted. That is why step 19 above is very important)
sudo mount /dev/webdata-vg/logs-lv /var/log
 
21.	Restore log files back into /var/log directory
sudo rsync -av /home/recovery/logs/log/ /var/log
 
 
22.	Update /etc/fstab file so that the mount configuration will persist after restart of the server
sudo vi /etc/fstab


#ADD THESE 2 LINES
/dev/webdata-vg/apps-lv /var/www/html ext4     defaults,nofail   0   0
/dev/webdata-vg/logs-lv /var/log ext4     defaults,nofail   0   0
 
23.	Verify your setup by running df -h, output must look like this:
 

Step 2 — Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role - ‘DB Server’ Repeat the same steps as for the Web Server, but instead of app-lv create db-lv and mount it to /db directory instead of /var/www/html/.
1.	Launch the DB Server instance and create 3 volumes in the same AZ as DB Server EC2, each of 1 GiB.
2.	Attach all three volumes one by one to your Web Server EC2 instance
 
 

3.	Open up the Linux terminal to begin configuration

4.	Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there - their names will likely be xvdf, xvdh, xvdg.
 
5.	Use df -h command to see all mounts and free space on your server

6.	Use gdisk utility to create a single partition on each of the 3 disks

 
 
a.	sudo gdisk /dev/xvdf
 
b.	sudo gdisk /dev/xvdg
 
c.	sudo gdisk /dev/xvdh

 

7. 	Use lsblk utility to view the newly configured partition on each of the 3 disks.
 

8.	Install lvm2 package using sudo yum install lvm2. 

  
 
9.	Run sudo lvmdiskscan  command to check for available partitions.

 


10.	Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
sudo wipefs -a /dev/xvdf && sudo pvcreate /dev/xvdf
sudo wipefs -a /dev/xvdg && sudo pvcreate /dev/xvdg
sudo wipefs -a /dev/xvdh && sudo pvcreate /dev/xvdh
 

 
11.	Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
sudo vgcreate webdata-vg /dev/xvdh /dev/xvdg /dev/xvdf

12.	Verify that your VG has been created successfully by running sudo vgs
 


13.	Use lvcreate utility to create 2 logical volumes. db-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: db-lv will be used to store data for the database while, logs-lv will be used to store data for logs.

sudo lvcreate -n db-lv -L 1.44g webdata-vg

sudo lvcreate -n logs-lv -L 1.44g webdata-vg

sudo lvs
 
 
14.	Verify the entire setup
sudo vgdisplay -v #view complete setup - VG, PV, and LV
 
 


sudo lsblk 
 

15.	Use mkfs.ext4 to format the logical volumes with ext4 filesystem

sudo mkfs -t ext4 /dev/webdata-vg/db-lv

sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
 

16.	Create /db directory to store database files
17.	Create /home/recovery/logs to store backup of log data
 
 
18.	Mount db-lv logical volume on /db
sudo mount /dev/webdata-vg/db-lv /db/
 

19.	Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
sudo rsync -av /var/log /home/recovery/logs
 
 

20.	Mount /logs-lv logical volume on var/log . (Note that all the existing data on /var/log will be deleted. That is why step 19 above is very important)
sudo mount /dev/webdata-vg/logs-lv /var/log
 
21.	Restore log files back into /var/log directory
sudo rsync -av /home/recovery/logs/log/ /var/log

 
 

22.	Update /etc/fstab file so that the mount configuration will persist after restart of the server
sudo vi /etc/fstab


#ADD THESE 2 LINES
/dev/webdata-vg/db-lv /var/www/html ext4     defaults,nofail   0   0
/dev/webdata-vg/logs-lv /var/log ext4     defaults,nofail   0   0

 

23.	Verify your setup by running df -h

 



Step 3 — Install Wordpress on your Web Server EC2

1.	Install WordPress and other necessary packages
sudo yum -y update
 
 

sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
 
 

cd /var/www/html
 

sudo wget http://wordpress.org/latest.tar.gz

 

sudo tar xzvf latest.tar.gz
 

cp -r wordpress /var/www/html
 
sudo systemctl enable httpd
 










Step 4 — Install MySQL on your DB Server EC2

sudo yum update
 

sudo yum install mysql-server
 
Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:
sudo systemctl restart mysqld
sudo systemctl enable mysqld
 
Step 5 — Configure DB to work with WordPress
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;


 
New user account created after for new webserver instance
 
exit


Step 6 — Configure WordPress to connect to remote database.
Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32
 
1.	Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client
2.	Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases
sudo yum install mysql
 



sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
sudo mysql -u teamleads -p -h 172.31.14.166
sudo mysql -u tmleads -p -h 172.31.14.166
 

3.	Change permissions and configuration so Apache could use WordPress:
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 

Challenge encountered
I ran the chcon command as above but errors. I tried to resolve it by enabling back selinux using vi /etc/sysconfig/selinux to start enforcing again but after I rebooted my instance, it failed to connect to the terminal. I opened all the ports on webserver security group to allow all traffic but did not succeed.
I created a new instance for the webserver, detached the 3 volumes from the previous instance and reattached to the new instance with the hope that the files will be recopied automatically. This did not work so it is just like am configuring the webserver instance from the scratch
sudo setsebool -P httpd_can_network_connect=1
 
4.	Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)
 
5.	Try to access from browser the link to WordPress http://<Web-Server-Public-IP-Address>/wordpress/
http://3.12.150.212/wordpress/
 

Creating the wp-config.php file manually...
 


 

Summary 
In this project, I learnt how to configure Linux storage subsystem and was able to deploy a full-scale Web Solution using WordPress Content Management System and MySQL RDBMS!













Resources

https://www.thegeekdiary.com/centos-redhat-how-to-set-selinux-modes/#:~:text=kernel%20boot%20parameters.-,Edit%20the%20%2Fetc%2Fgrub.,disable%20SELinux%20at%20the%20booting.

https://unix.stackexchange.com/questions/274360/chcon-cant-apply-partial-context-to-unlabeled-file-usr-sbin-xrdp


