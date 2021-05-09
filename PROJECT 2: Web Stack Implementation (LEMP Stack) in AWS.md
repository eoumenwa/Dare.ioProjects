
## WEB STACK IMPLEMENTATION (LEMP STACK)

Project 2 covers similar concepts as Project 1 and helps to cement your skills of deploying Web solutions using LA(E)MP stacks.
You have done a great job with successful completion of Project 1.

In this project you will implement a similar stack, but with an alternative Web Server - NGINX, which is also very popular and widely used by many websites in the Internet.


Step 0 - Preparing prerequisites

Hint: In previous project we used Putty on Windows to connect to our EC2 Instance, but there is a simpler way that do not require conversion of .pem key to .ppk - using Git Bash.

## Error and Resolution 1

Failure to connect using 

ssh -i "projectreload.pem" ubuntu@ec2-54-147-3-6.compute-1.amazonaws.com

Resolution was to edit security group settings to include access from anywhere 0.0.0.0/0

## Step 1 – Installing the Nginx Web Server

In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.

Since this is our first time using apt for this session, start off by updating your server’s package index. Following that, you can use apt install to get Nginx installed:

$ sudo apt update
$ sudo apt install nginx
When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

$ sudo systemctl status nginx
If it is green and running, then you did everything correctly - you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web brousers use to access web pages in the Internet.

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

_images/OpenPort80.gif

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:

$ curl http://localhost:80
or
$ curl http://127.0.0.1:80

These 2 commands above actually do pretty much the same - they use ‘curl’ command to request our Nginx on port 80 (actually you can even try to not specify any port - it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one - by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called “resolution”). We will touch DNS in further lectures and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Nginx web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Nginx server can respond to requests from the Internet. Open a web browser of your choice and try to access following url

http://<Public-IP-Address>:80

Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

curl -s http://169.254.169.254/latest/meta-data/public-ipv4
The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.

_images/Nginx_welcome.png

In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browser.

Step 2 — Installing MySQL
Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use apt to acquire and install this software:

    $ sudo apt install mysql-server
When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, it’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running:

    $ sudo mysql_secure_installation

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

**Note:** Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.
Answer Y for yes, or anything else to continue without enabling.

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words.

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
Regardless of whether you chose to set up theVALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure. We’ll talk about this in a moment.

If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
For the rest of the questions, press Y and hit the ENTER key at each prompt. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

When you’re finished, test if you’re able to log in to the MySQL console by typing:

     $ sudo mysql
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:

Output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 22
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> 
To exit the MySQL console, type:

    mysql> exit
Notice that you didn’t need to provide a password to connect as the root user, even though you have defined one when running the mysql_secure_installation script. That is because the default authentication method for the administrative MySQL user is unix_socket instead of password. Even though this might look like a security concern at first, it makes the database server more secure because the only users allowed to log in as the root MySQL user are the system users with sudo privileges connecting from the console or through an application running with the same privileges. In practical terms, that means you won’t be able to use the administrative database root user to connect from your PHP application. Setting a password for the root MySQL account works as a safeguard, in case the default authentication method is changed from unix_socket to password.

For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead. We’ll demonstrate how to do that in Step 6.
Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LEMP stack.

## Step 3 – Installing PHP

You have Nginx installed to serve your content and MySQL installed to store and manage your data. Now you can install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, run:

    $ sudo apt install php-fpm php-mysql
When prompted, type Y and press ENTER to confirm installation.

You now have your PHP components installed. Next, you will configure Nginx to use them.

## Step 4 — Configuring Nginx to Use PHP Processor

When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

Create the root web directory for your_domain as follows:

     $ sudo mkdir /var/www/projectLEMP
Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

     $ sudo chown -R $USER:$USER /var/www/projectLEMP

Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:

    $ sudo nano /etc/nginx/sites-available/projectLEMP

This will create a new blank file. Paste in the following bare-bones configuration:

#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
Here’s what each of these directives and location blocks do:

listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.
root — Defines the document root where the files served by this website are stored.
index— Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.
server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.
location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.
location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.
location ~ /\.ht— The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.
When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/

This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

$ sudo nginx -t

You shall see following message:

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

If any errors are reported, go back to your configuration file to review its contents before continuing.

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

sudo unlink /etc/nginx/sites-enabled/default

When you are ready, reload Nginx to apply the changes:

$ sudo systemctl reload nginx

Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
Now go to your browser and try to open your website URL using IP address:

http://<Public-IP-Address>:80
If you see the text from ‘echo’ command you wrote to index.html file, then it means your Nginx site is working as expected. In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP - try it out, the result must be the same (port is optional)

http://<Public-DNS-Name>:80
You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.

Your LEMP stack is now fully configured. In the next step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within your newly configured website.

Step 5 – Testing PHP with Nginx
Your LEMP stack should now be completely set up.

At this point, your LAMP stack is completely installed and fully operational.

You can test it to validate that Nginx can correctly hand .php files off to your PHP processor.

You can do this by creating a test PHP file in your document root. Open a new file called info.php within your document root in your text editor:

$ nano /var/www/projectLEMP/info.php
Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

<?php
phpinfo();
You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:

http://`server_domain_or_IP`/info.php
You will see a web page containing detailed information about your server:

https://drive.google.com/uc?export=view&id=1ibJUPX_HrKyL-SuSrLfvcgWmH_aUdEAY

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file:

$ sudo rm /var/www/your_domain/info.php
You can always regenerate this file if you need it later.

Step 6 — Retrieving data from MySQL database with PHP
In this step you will create a test database (DB) with simple “To do list” and configure access to it, so the Nginx website would be able to query data from the DB and display it.

At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.

We will create a database named example_database and a user named example_user, but you can replace these names with different values.

First, connect to the MySQL console using the root account:

$ sudo mysql
To create a new database, run the following command from your MySQL console:

mysql> CREATE DATABASE `example_database`;
Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
Now we need to give this user permission over the example_database database:

mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

mysql> exit
You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

$ mysql -u example_user -p
Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. After logging in to the MySQL console, confirm that you have access to the example_database database:

mysql> SHOW DATABASES;
This will give you the following output:

Output
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );
Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
To confirm that the data was successfully saved to your table, run:

mysql>  SELECT * FROM example_database.todo_list;
You’ll see the following output:

Output
+---------+--------------------------+
| item_id | content                  |
+---------+--------------------------+
|       1 | My first important item  |
|       2 | My second important item |
|       3 | My third important item  |
|       4 | and this one more thing  |
+---------+--------------------------+
4 rows in set (0.000 sec)
After confirming that you have valid data in your test table, you can exit the MySQL console:

mysql> exit
Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

$ nano /var/www/projectLEMP/todo_list.php
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

Copy this content into your todo_list.php script:

<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

http://<Public_domain_or_IP>/todo_list.php
You should see a page like this, showing the content you’ve inserted in your test table:

 ![image](https://user-images.githubusercontent.com/78841364/117588930-a3b52a80-b0f4-11eb-9c90-77693b3812ba.png)


That means your PHP environment is ready to connect and interact with your MySQL server.

## Summary
In this project, I built a flexible foundation for serving PHP websites and applications to my visitors, using Nginx as web server and MySQL as database management system.

################################################################################
oeume@KRISOLIZ-SFFHPDPC MINGW64 ~/Downloads
$ ssh -i "projectreload.pem" ubuntu@ec2-54-147-3-6.compute-1.amazonaws.com
ssh: connect to host ec2-54-147-3-6.compute-1.amazonaws.com port 22: Connection timed out

oeume@KRISOLIZ-SFFHPDPC MINGW64 ~/Downloads
$ 

oeume@KRISOLIZ-SFFHPDPC MINGW64 ~/Downloads
ubuntu@ip-172-31-29-38:~$ sudo apt update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]  
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security InRelease [109 kB]
Get:5 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 Packages [8628 kB]
Get:6 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe Translation-en [5124 kB]
Get:7 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [629 kB]         
Get:8 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 c-n-f Metadata [265 kB]
Get:9 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [144 kB]  
Get:10 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/multiverse Translation-en [104 kB]      
Get:11 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal/multiverse amd64 c-n-f Metadata [9136 B]
Get:12 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [955 kB]    
Get:13 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/main Translation-en [218 kB]       
Get:14 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/main amd64 c-n-f Metadata [13.2 kB]
Get:15 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [208 kB] 
Get:16 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/restricted Translation-en [30.9 kB]
Get:17 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [768 kB]
Get:18 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/universe Translation-en [165 kB]       
Get:19 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/universe amd64 c-n-f Metadata [17.2 kB]
Get:20 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [21.7 kB]      
Get:21 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/multiverse Translation-en [5508 B]       
Get:22 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 c-n-f Metadata [600 B]  
Get:23 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/main amd64 c-n-f Metadata [112 B]      
Get:24 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/restricted amd64 c-n-f Metadata [116 B]
Get:25 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [4032 B]       
Get:26 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/universe Translation-en [1448 B]       
Get:27 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/universe amd64 c-n-f Metadata [224 B]  
Get:28 http://us-east-1.ec2.archive.ubuntu.com/ubuntu focal-backports/multiverse amd64 c-n-f Metadata [116 B]
Get:29 http://security.ubuntu.com/ubuntu focal-security/main Translation-en [128 kB]
Get:30 http://security.ubuntu.com/ubuntu focal-security/main amd64 c-n-f Metadata [7476 B]
Get:31 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [185 kB]
Get:32 http://security.ubuntu.com/ubuntu focal-security/restricted Translation-en [27.2 kB]
Get:33 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [558 kB]
Get:34 http://security.ubuntu.com/ubuntu focal-security/universe Translation-en [84.1 kB]
Get:35 http://security.ubuntu.com/ubuntu focal-security/universe amd64 c-n-f Metadata [10.8 kB]
Get:36 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [14.9 kB]    
Get:37 http://security.ubuntu.com/ubuntu focal-security/multiverse Translation-en [3160 B]     
Get:38 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 c-n-f Metadata [340 B]
Fetched 18.7 MB in 4s (4495 kB/s) 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
15 packages can be upgraded. Run 'apt list --upgradable' to see them.
Selecting previously unselected package libnginx-mod-http-image-filter.
Preparing to unpack .../11-libnginx-mod-http-image-filter_1.18.0-0ubuntu1_amd64.deb ...
Unpacking libnginx-mod-http-image-filter (1.18.0-0ubuntu1) ...
Selecting previously unselected package libnginx-mod-http-xslt-filter.
Preparing to unpack .../12-libnginx-mod-http-xslt-filter_1.18.0-0ubuntu1_amd64.deb ...
Unpacking libnginx-mod-http-xslt-filter (1.18.0-0ubuntu1) ...
Selecting previously unselected package libnginx-mod-mail.
Preparing to unpack .../13-libnginx-mod-mail_1.18.0-0ubuntu1_amd64.deb ...
Unpacking libnginx-mod-mail (1.18.0-0ubuntu1) ...
Selecting previously unselected package libnginx-mod-stream.
Preparing to unpack .../14-libnginx-mod-stream_1.18.0-0ubuntu1_amd64.deb ...
Unpacking libnginx-mod-stream (1.18.0-0ubuntu1) ...
Selecting previously unselected package nginx-core.
Preparing to unpack .../15-nginx-core_1.18.0-0ubuntu1_amd64.deb ...
Unpacking nginx-core (1.18.0-0ubuntu1) ...
Selecting previously unselected package nginx.
Preparing to unpack .../16-nginx_1.18.0-0ubuntu1_all.deb ...
Unpacking nginx (1.18.0-0ubuntu1) ...
Setting up libxpm4:amd64 (1:3.5.12-1) ...
Setting up nginx-common (1.18.0-0ubuntu1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /lib/systemd/system/nginx.service.
Setting up libjbig0:amd64 (2.1-3.1build1) ...
Setting up libnginx-mod-http-xslt-filter (1.18.0-0ubuntu1) ...
Setting up libwebp6:amd64 (0.6.1-2) ...
Setting up fonts-dejavu-core (2.37-1) ...
Setting up libjpeg-turbo8:amd64 (2.0.3-0ubuntu1.20.04.1) ...
Setting up libjpeg8:amd64 (8c-2ubuntu8) ...
Setting up libnginx-mod-mail (1.18.0-0ubuntu1) ...
Setting up fontconfig-config (2.13.1-2ubuntu3) ...
Setting up libnginx-mod-stream (1.18.0-0ubuntu1) ...
Setting up libtiff5:amd64 (4.1.0+git191117-2ubuntu0.20.04.1) ...
Setting up libfontconfig1:amd64 (2.13.1-2ubuntu3) ...
Setting up libgd3:amd64 (2.2.5-5.2ubuntu2) ...
Setting up libnginx-mod-http-image-filter (1.18.0-0ubuntu1) ...
Setting up nginx-core (1.18.0-0ubuntu1) ...
Setting up nginx (1.18.0-0ubuntu1) ...
Processing triggers for ufw (0.36-6) ...
Processing triggers for systemd (245.4-4ubuntu3.6) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
ubuntu@ip-172-31-29-38:~$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2021-05-09 16:41:21 UTC; 5min ago
       Docs: man:nginx(8)
   Main PID: 2015 (nginx)
      Tasks: 2 (limit: 1160)
     Memory: 5.3M
     CGroup: /system.slice/nginx.service
             ├─2015 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             └─2019 nginx: worker process

May 09 16:41:18 ip-172-31-29-38 systemd[1]: Starting A high performance web server and a reverse proxy server...
May 09 16:41:21 ip-172-31-29-38 systemd[1]: Started A high performance web server and a reverse proxy server.
ubuntu@ip-172-31-29-38:~$ curl http://localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
ubuntu@ip-172-31-29-38:~$ curl http://127.0.0.1:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
ubuntu@ip-172-31-29-38:~$ curl -s http://169.254.169.254/latest/meta-data/public-ipv4
54.147.3.6ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ sudo apt install mysql-server
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libcgi-fast-perl libcgi-pm-perl libencode-locale-perl libevent-core-2.1-7 libevent-pthreads-2.1-7 libfcgi-perl
  libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl libhttp-date-perl libhttp-message-perl libio-html-perl
  liblwp-mediatypes-perl libmecab2 libtimedate-perl liburi-perl mecab-ipadic mecab-ipadic-utf8 mecab-utils mysql-client-8.0    
  mysql-client-core-8.0 mysql-common mysql-server-8.0 mysql-server-core-8.0
Suggested packages:
  libdata-dump-perl libipc-sharedcache-perl libwww-perl mailx tinyca
reading /usr/share/mecab/dic/ipadic/Noun.name.csv ... 34202
reading /usr/share/mecab/dic/ipadic/Postp.csv ... 146
reading /usr/share/mecab/dic/ipadic/Symbol.csv ... 208
reading /usr/share/mecab/dic/ipadic/Prefix.csv ... 221
reading /usr/share/mecab/dic/ipadic/Others.csv ... 2
reading /usr/share/mecab/dic/ipadic/Noun.demonst.csv ... 120
reading /usr/share/mecab/dic/ipadic/Auxil.csv ... 199
reading /usr/share/mecab/dic/ipadic/Noun.number.csv ... 42
reading /usr/share/mecab/dic/ipadic/Postp-col.csv ... 91
reading /usr/share/mecab/dic/ipadic/Suffix.csv ... 1393
reading /usr/share/mecab/dic/ipadic/Noun.verbal.csv ... 12146
reading /usr/share/mecab/dic/ipadic/Adj.csv ... 27210
reading /usr/share/mecab/dic/ipadic/Noun.proper.csv ... 27328
reading /usr/share/mecab/dic/ipadic/Noun.csv ... 60477
reading /usr/share/mecab/dic/ipadic/Verb.csv ... 130750
reading /usr/share/mecab/dic/ipadic/Noun.adjv.csv ... 3328
reading /usr/share/mecab/dic/ipadic/Filler.csv ... 19
reading /usr/share/mecab/dic/ipadic/Noun.adverbal.csv ... 795
reading /usr/share/mecab/dic/ipadic/Noun.org.csv ... 16668
emitting double-array: 100% |###########################################|
reading /usr/share/mecab/dic/ipadic/matrix.def ... 1316x1316
emitting matrix      : 100% |###########################################|

done!
update-alternatives: using /var/lib/mecab/dic/ipadic-utf8 to provide /var/lib/mecab/dic/debian (mecab-dictionary) in auto mode 
Setting up libhtml-parser-perl (3.72-5) ...
Setting up libhttp-message-perl (6.22-1) ...
Setting up mysql-server-8.0 (8.0.23-0ubuntu0.20.04.1) ...
update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Renaming removed key_buffer and myisam-recover options (if present)
mysqld will log errors to /var/log/mysql/error.log
mysqld is running as pid 2659
Created symlink /etc/systemd/system/multi-user.target.wants/mysql.service → /lib/systemd/system/mysql.service.
Setting up libcgi-pm-perl (4.46-1) ...
Setting up libhtml-template-perl (2.97-1) ...
Setting up mysql-server (8.0.23-0ubuntu0.20.04.1) ...
Setting up libcgi-fast-perl (1:2.15-1) ...
Processing triggers for systemd (245.4-4ubuntu3.6) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
ubuntu@ip-172-31-29-38:~$ sudo mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No:
Please set the password for root here.

New password:

Re-enter new password:
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
ubuntu@ip-172-31-29-38:~$ sudo mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.23-0ubuntu0.20.04.1 (Ubuntu)

Creating config file /etc/php/7.4/mods-available/sysvmsg.ini with new version

Creating config file /etc/php/7.4/mods-available/sysvsem.ini with new version

Creating config file /etc/php/7.4/mods-available/sysvshm.ini with new version

Creating config file /etc/php/7.4/mods-available/tokenizer.ini with new version
Setting up php7.4-mysql (7.4.3-4ubuntu2.4) ...

Creating config file /etc/php/7.4/mods-available/mysqlnd.ini with new version

Creating config file /etc/php/7.4/mods-available/mysqli.ini with new version

Creating config file /etc/php/7.4/mods-available/pdo_mysql.ini with new version
Setting up php7.4-readline (7.4.3-4ubuntu2.4) ...

Creating config file /etc/php/7.4/mods-available/readline.ini with new version
Setting up php7.4-opcache (7.4.3-4ubuntu2.4) ...

Creating config file /etc/php/7.4/mods-available/opcache.ini with new version
Setting up php7.4-json (7.4.3-4ubuntu2.4) ...

Creating config file /etc/php/7.4/mods-available/json.ini with new version
Setting up php-mysql (2:7.4+75) ...
Setting up php7.4-cli (7.4.3-4ubuntu2.4) ...
update-alternatives: using /usr/bin/php7.4 to provide /usr/bin/php (php) in auto mode
update-alternatives: using /usr/bin/phar7.4 to provide /usr/bin/phar (phar) in auto mode
update-alternatives: using /usr/bin/phar.phar7.4 to provide /usr/bin/phar.phar (phar.phar) in auto mode

Creating config file /etc/php/7.4/cli/php.ini with new version
Setting up php7.4-fpm (7.4.3-4ubuntu2.4) ...

Creating config file /etc/php/7.4/fpm/php.ini with new version
Created symlink /etc/systemd/system/multi-user.target.wants/php7.4-fpm.service → /lib/systemd/system/php7.4-fpm.service.       
Setting up php-fpm (2:7.4+75) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for systemd (245.4-4ubuntu3.6) ...
Processing triggers for php7.4-cli (7.4.3-4ubuntu2.4) ...
Processing triggers for php7.4-fpm (7.4.3-4ubuntu2.4) ...
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo mkdir /var/www/projectLEMP
ubuntu@ip-172-31-29-38:~$ ls -l /var/www/projectLEMP
total 0
ubuntu@ip-172-31-29-38:~$ ls -la /var/www/projectLEMP
total 8
drwxr-xr-x 2 root root 4096 May  9 17:04 .
drwxr-xr-x 4 root root 4096 May  9 17:04 ..
ubuntu@ip-172-31-29-38:~$ ls -la /var/www/
total 16
drwxr-xr-x  4 root root 4096 May  9 17:04 .
drwxr-xr-x 14 root root 4096 May  9 16:41 ..
drwxr-xr-x  2 root root 4096 May  9 16:41 html
drwxr-xr-x  2 root root 4096 May  9 17:04 projectLEMP
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo chown -R $USER:$USER /var/www/projectLEMP
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ ls -la /var/www/
total 16
drwxr-xr-x  4 root   root   4096 May  9 17:04 .
drwxr-xr-x 14 root   root   4096 May  9 16:41 ..
drwxr-xr-x  2 root   root   4096 May  9 16:41 html
drwxr-xr-x  2 ubuntu ubuntu 4096 May  9 17:04 projectLEMP
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo nano /etc/nginx/sites-available/projectLEMP
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo unlink /etc/nginx/sites-enabled/default
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo systemctl reload nginx
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ nano /var/www/projectLEMP/info.php
ubuntu@ip-172-31-29-38:~$
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ sudo mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.23-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> CREATE DATABASE `projectreload_database`;
Query OK, 1 row affected (0.02 sec)

mysql> CREATE USER 'projectreload_user1'@'%' IDENTIFIED WITH mysql_native_password BY '12345'
    ->
    -> ^C  

^C
mysql> CREATE USER 'projectreload_user1'@'%' IDENTIFIED WITH mysql_native_password BY '12345';
Query OK, 0 rows affected (0.81 sec)

mysql> GRANT ALL ON projectreload_database.* TO 'projectreload_user1'@'%';
Query OK, 0 rows affected (0.01 sec)

mysql> exit
Bye
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ 
ubuntu@ip-172-31-29-38:~$ mysql -u projectreload_user1 -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.23-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+------------------------+
| Database               |
+------------------------+
| information_schema     |
| projectreload_database |
+------------------------+
2 rows in set (0.01 sec)

mysql> CREATE TABLE example_database.todo_list (
    -> mysql>     item_id INT AUTO_INCREMENT,
    -> mysql>     content VARCHAR(255),
    -> mysql>     PRIMARY KEY(item_id)
+---------+---------------------------------+
|       1 | My first important item         |
|       2 | My second important item        |
|       3 | My third important item         |
|       4 | and finally this one more thing |
+---------+---------------------------------+
4 rows in set (0.00 sec)

mysql> exit
Bye

Edited info.php and commented out a line to prevent it from loading.

