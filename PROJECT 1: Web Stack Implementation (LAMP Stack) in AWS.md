# What is a Technology stack?
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…

LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
MEAN (MongoDB, ExpressJS, AngularJS, NodeJS

Having created the instance, the set up is as shown below

![image](https://user-images.githubusercontent.com/78841364/117536627-137ac680-afca-11eb-99fc-4df7fb7d5c9b.png)
Connect to EC2 via SSH

## Step 1 — Installing Apache and Updating the Firewall

Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting providers use Apache as their web server software. However, websites and other applications can run on other web server software as well. Such as Nginx, Microsoft’s IIS, etc.

The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.

Install Apache using Ubuntu’s package manager ‘apt’:

1. update a list of packages in package manager
       
    sudo apt update
      
2. run apache2 package installation

    sudo apt install apache2

3. verify that apache2 is running as a Service in our OS

    sudo systemctl status apache2
    
   ● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
     Active: active (running) since Sat 2021-05-08 02:28:30 UTC; 3min 7s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 2230 (apache2)
      Tasks: 55 (limit: 1160)
     Memory: 5.3M
     CGroup: /system.slice/apache2.service
             ├─2230 /usr/sbin/apache2 -k start
             ├─2232 /usr/sbin/apache2 -k start
             └─2233 /usr/sbin/apache2 -k start

   
4. Open TCP port 80 which is the default port that web browsers use to access web pages on the Internet. We have TCP port 22 open by default on our EC2   
   machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:
   
5. First, let us try to check how we can access it locally in our Ubuntu shell, run:

          curl http://localhost:80
     or
          curl http://127.0.0.1:80
          
We use ‘curl’ command to request our Apache HTTP Server on port 80 (actually you can even try to not specify any port - it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one - by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called “resolution”).

As an output you can see some strangely formatted test, do not worry, we just made sure that our Apache web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet. Open a web browser of your choice and try to access following url

http://<Public-IP-Address>:80
   

## Step 2 — Installing MySQL

Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use ‘apt’ to acquire and install this software:

    sudo apt install mysql-server
    
When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, it’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running:

    sudo mysql_secure_installation

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

Answer Y for yes, or anything else to continue without enabling.


Test log in to the MySQL console by typing:

    sudo mysql

This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command.

Welcome to the MySQL monitor.  Commands end with ; or \g.

To exit the MySQL console, type:

mysql> exit


Notice that you didn’t need to provide a password to connect as the root user, even though you have defined one when running the mysql_secure_installation script. That is because the default authentication method for the administrative MySQL user is unix_socket instead of password. Even though this might look like a security concern at first, it makes the database server more secure because the only users allowed to log in as the root MySQL user are the system users with sudo privileges connecting from the console or through an application running with the same privileges. In practical terms, that means you won’t be able to use the administrative database root user to connect from your PHP application. Setting a password for the root MySQL account works as a safeguard, in case the default authentication method is changed from unix_socket to password.

For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead.

Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.

## Step 3 — Installing PHP


You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these 3 packages at once, run:

    sudo apt install php libapache2-mod-php php-mysql

Once the installation is finished, you can run the following command to confirm your PHP version:

    php -v

PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
At this point, your LAMP stack is completely installed and fully operational.

[x] Linux (Ubuntu)
[x] Apache HTTP Server
[x] MySQL
[x] PHP

To test your setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold your website’s files and folders. Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.

![image](https://user-images.githubusercontent.com/78841364/117536569-baab2e00-afc9-11eb-91d4-ba07f561173e.png)



We will configure our first Virtual Host in the next step.

## Step 4 — Creating a Virtual Host for your Website using Apache

In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice.

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. We will leave this configuration as is and will add our own directory next next to the default one.

Create the directory for projectlamp using ‘mkdir’ command as follows:

$ sudo mkdir /var/www/projectlamp
Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

         $ sudo chown -R $USER:$USER /var/www/projectlamp


ubuntu@ip-172-31-26-86:~$ ls -la /var/www
total 16
drwxr-xr-x  4 root root 4096 May  8 11:00 .
drwxr-xr-x 14 root root 4096 May  8 02:28 ..
drwxr-xr-x  2 root root 4096 May  8 02:28 html
drwxr-xr-x  2 root root 4096 May  8 11:00 projectlamp

ubuntu@ip-172-31-26-86:~$ sudo chown -R $USER:$USER /var/www/projectlamp

ubuntu@ip-172-31-26-86:~$ ls -la /var/www
total 16

drwxr-xr-x  4 root   root   4096 May  8 11:00 .
drwxr-xr-x 14 root   root   4096 May  8 02:28 ..
drwxr-xr-x  2 root   root   4096 May  8 02:28 html
drwxr-xr-x  2 ubuntu ubuntu 4096 May  8 11:00 projectlamp

ubuntu@ip-172-31-26-86:~$


Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

$ sudo vi /etc/apache2/sites-available/projectlamp.conf
This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

You can use the ls command to show the new file in the sites-available directory

     $ sudo ls /etc/apache2/sites-available

ubuntu@ip-172-31-26-86:~$ sudo ls /etc/apache2/sites-available
000-default.conf  default-ssl.conf  projectlamp.conf


With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

You can now use a2ensite command to enable the new virtual host:

    $ sudo a2ensite projectlamp

    ubuntu@ip-172-31-26-86:~$ sudo a2ensite projectlamp      

Enabling site projectlamp.

To activate the new configuration, you need to run:
              
              systemctl reload apache2

ubuntu@ip-172-31-26-86:~$ sudo systemctl reload apache2

ubuntu@ip-172-31-26-86:~$

## Note: the above step has the same effect as creating a symbolic link using 

    sudo ln -s /etc/apache2/sites-available/projectlamp.conf /etc/apache2/sites-enabled/


You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:

     $ sudo a2dissite 000-default

ubuntu@ip-172-31-26-86:~$ sudo a2dissite 000-default

Site 000-default disabled.

The above command has the same effect as unlinking the default conf file in sites-enabled or removing it using rm -f

To make sure your configuration file doesn’t contain syntax errors, run:

     $ sudo apache2ctl configtest

ubuntu@ip-172-31-26-86:~$ sudo apache2ctl configtest

Syntax OK


Finally, reload Apache so these changes take effect:

     $ sudo systemctl reload apache2
     
Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

      sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s       
      http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

Now go to your browser and try to open your website URL using IP address:

     http://<Public-IP-Address>:80
       
       ![image](https://user-images.githubusercontent.com/78841364/117537478-293eba80-afcf-11eb-9cc2-a91e6f21a997.png)

If you see the text from ‘echo’ command you wrote to index.html file, then it means your Apache virtual host is working as expected. In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP - try it out, the result must be the same (port is optional)

    http://<Public-DNS-Name>:80
       
       
You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.
       
       
# Step 5 — Enable PHP on the website

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

     sudo vim /etc/apache2/mods-enabled/dir.conf
     
              <IfModule mod_dir.c>
                      #Change this:
                      #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
                      #To this:
                      DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
              </IfModule>
              
After saving and closing the file, you will need to reload Apache so the changes take effect:

     $ sudo systemctl reload apache2
Finally, we will create a PHP script to test that PHP is correctly installed and configured on your server.

Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

Create a new file named index.php inside your custom web root folder:

     $ vim /var/www/projectlamp/index.php
This will open a blank file. Add the following text, which is valid PHP code, inside the file:

     <?php
     phpinfo();
When you are finished, save and close the file, refresh the page and you will see a page similar to this:

https://drive.google.com/uc?export=view&id=1ibJUPX_HrKyL-SuSrLfvcgWmH_aUdEAY

This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

If you can see this page in your browser, then your PHP installation is working as expected.

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:

$ sudo rm /var/www/projectlamp/index.php
You can always recreate this page if you need to access the information again later.


## Summary

In this project I learnt how to deploying a LAMP stack website in AWS Cloud.

## References

https://starter-pbl.darey.io/en/latest/project1.html
digitalocean

