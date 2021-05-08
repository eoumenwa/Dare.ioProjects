# What is a Technology stack?
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…

LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
MEAN (MongoDB, ExpressJS, AngularJS, NodeJS


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


   
   
