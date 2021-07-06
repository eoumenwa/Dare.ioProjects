# Load Balancer Solution With Nginx and SSL/TLS

By now you have learned what Load Balancing is used for and have configured an LB solution using Apache, but a DevOps engineer must be a versatile professional and know different alternative solutions for the same problem. That is why, in this project we will configure an Nginx Load Balancer solution.

It is also extremely important to ensure that connections to your Web solutions are secure and information is encrypted in transit - we will also cover connection over secured HTTP (HTTPS protocol), its purpose and what is required to implement it.

When data is moving between a client (browser) and a Web Server over the Internet - it passes through multiple network devices and, if the data is not encrypted, it can be relatively easy intercepted by someone who has access to the intermediate equipment. This kind of information security threat is called Man-In-The-Middle (MIMT) attack.

This threat is real - users that share sensitive information (bank details, social media access credentials, etc.) via non-secured channels, risk their data to be compromised and used by cybercriminals.

SSL and its newer version, TSL - is a security technology that protects connection from MITM attacks by creating an encrypted session between browser and Web server. Here we will refer this family of cryptographic protocols as SSL/TLS - even though SSL was replaced by TLS, the term is still being widely used.

SSL/TLS uses digital certificates to identify and validate a Website. A browser reads the certificate issued by a Certificate Authority (CA) to make sure that the website is registered in the CA so it can be trusted to establish a secured connection.

There are different types of SSL/TLS certificates - you can learn more about them here. You can also watch a tutorial on how SSL works here or an additional resource here

In this project you will register your website with LetsEnrcypt Certificate Authority, to automate certificate issuance you will use a shell client recommended by LetsEncrypt - cetrbot.

## Task

This project consists of two parts:

1. Configure Nginx as a Load Balancer
2. Register a new domain name and configure secured connection using SSL/TLS certificates
   Our target architecture will look like this:

![image](https://user-images.githubusercontent.com/78841364/116086691-10004a80-a66e-11eb-9154-18e77e32749b.png)


# Part 1 - Configure Nginx As A Load Balancer

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB
2. Open TCP port 80 for HTTP connections, also open TCP port 443 - this port is used for secured HTTPS connections)


   ![image](https://user-images.githubusercontent.com/78841364/116269984-b15dce00-a74c-11eb-9ce2-b0fdc013543b.png)

3. Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

   
   ![image](https://user-images.githubusercontent.com/78841364/116270649-4791f400-a74d-11eb-98ca-8d77e15f70ca.png)



4. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

   sudo apt update
  
   sudo apt install nginx
   
   sudo systemctl enable nginx
   
   sudo systemctl restart nginx
   
   sudo systemctl status nginx


   ![image](https://user-images.githubusercontent.com/78841364/116273182-888b0800-a74f-11eb-99fc-c841c34d7954.png)


  
5. Configure Nginx LB using Web Servers’ names defined in /etc/hosts


   sudo vi /etc/nginx/nginx.conf

   ![image](https://user-images.githubusercontent.com/78841364/116810828-f0f13500-ab13-11eb-8db6-9efcb3d88a3a.png)


   Restart Nginx and make sure the service is up and running

   sudo systemctl restart nginx

   sudo systemctl status nginx
   
   

   Side Self Study: Read more about HTTP load balancing methods and features supported by Nginx on this page
   

## Part 2 - Register a new domain name and configure secured connection using SSL/TLS certificates

   In the following steps, we shall make necessary configurations to make connections to our Tooling Web Solution secured!
   In order to get a valid SSL certificate - we need to registrar a new domain name, using any Domain name registrar - a company that manages reservation of domain names. The    most popular ones are: Godaddy.com, Domain.com, Bluehost.com. we shall howevevr be using my.freenom.com in this project

1. Register the new domain name with freenom.com

   Register and confirm email address
   
   Check domain name availability and register

   We shall be using tooling4eou.tk as our domain name.
   
   Go to manage domains to see domain information.
   
2. Search and select Route 53 on AWS console. On Route 53 dashboard, click create hosted zone.

   Enter the domain name - select public hosted zone and create zone.
   
3. Go to freenom dashboard under domains/management tools/nameservers

   Select use custom nameservers and enter the nameservers (under type NS) from AWS Route 53 into customer nameservers field in freenom
   
   Click change nameservers
   
   ![image](https://user-images.githubusercontent.com/78841364/116728379-d6a04580-a9b3-11eb-92de-32dd8e6c446d.png)



   ![image](https://user-images.githubusercontent.com/78841364/116728458-f0da2380-a9b3-11eb-8cc7-d189b26a54fd.png) 
    AWS Route 53 dashboard
   
   
   We have successfully connected Route 53 and Freenom domain.
   
   
 4. Start the Nginx LB instance 
   
 
4. Assign an Elastic IP to your Nginx LB server and associate the domain name with this Elastic IP
   
    * Create an A record on AWS Route 53 pointing to the LB elastic IP (We use a static IP address to prevent changes in IP after an EC2 instance is       
      rebooted. 
   
    * Input the Elastic IP and leave everything in default then click create
   
    * Create another A record for www referencing Nginx LB Elastic IP
   
      We have that LB, Route 53 and Domain are noe connected
   
      ![image](https://user-images.githubusercontent.com/78841364/116737905-c8582680-a9bf-11eb-9234-f560b54ca308.png)

 
5. Next connect LB instance to the terminal
   
   

   Side Self Study: Read about different DNS record types and learn what they are used for.


6. Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol - http://tooling4eou.tk

   ![image](https://user-images.githubusercontent.com/78841364/116742459-639fca80-a9c5-11eb-82bd-9cff2cfc9805.png)


   Configure Nginx to recognize your new domain name
   
      Update nginx.conf with server_name tooling4eou.tk instead of server_name www.domain.com
   
   Use command below to create a new config file. Remember to comment out the necessary lines
   
         sudo vi /etc/nginx/sites-available/load_balancer.conf
   
   Use the command below to remove the default nginx site to redirect new config file
   
         sudo rm -f /etc/nginx/sites-enabled/default
   
   Use the command below to check if configuration is ok
      
         sudo nginx -t

   nginx: the configuration file /etc/nginx/nginx.conf syntax is ok

   nginx: configuration file /etc/nginx/nginx.conf test is successful

   Use the command below to create as symbolic link to the new config file existing under /etc/nginx/sites-available/
   
         sudo ln -s /etc/nginx/sites-available/load_balancer.conf /etc/nginx/sites-enabled/
   
   Confirm with the command below
   
         sudo ls -l /etc/nginx/sites-enabled/
   
   total 0
   
   lrwxrwxrwx 1 root root 41 May  2 15:41 load_balancer. -> /etc/nginx/sites-available/load_balancer.

   Error as a result of not commenting out the following line
   
   #####  include /etc/nginx/sites-enabled/*;
   
   ![image](https://user-images.githubusercontent.com/78841364/116821966-506a3780-ab4a-11eb-819f-0d1ed37067f2.png)

   
   Encountered error below after configuration of LB.
    
   ![image](https://user-images.githubusercontent.com/78841364/116820848-1e0a0b80-ab45-11eb-80d0-d90397a0ba72.png)
    
   Error persisted until I ran the command "sudo setenforce 0" on both webservers.

   ![image](https://user-images.githubusercontent.com/78841364/116821260-2cf1bd80-ab47-11eb-9811-678643e95a47.png)
    
    

7. Install certbot and request for an SSL/TLS certificate
   
   Make sure snapd service is active and running
   
   sudo systemctl status snapd
   
   ● snapd.service - Snap Daemon
        Loaded: loaded (/lib/systemd/system/snapd.service; enabled; vendor preset: enabled)
        Active: active (running) since Sun 2021-05-02 13:40:17 UTC; 3h 50min ago
   TriggeredBy: ● snapd.socket
      Main PID: 430 (snapd)
         Tasks: 8 (limit: 1160)
        Memory: 65.7M
        CGroup: /system.slice/snapd.service
                └─430 /usr/lib/snapd/snapd

   Install certbot

         sudo apt install certbot -y
   
   Reading package lists... Done
   
   Building dependency tree
   
   Reading state information... Done
   
   The following additional packages will be installed:
     
     python3-acme python3-certbot python3-configargparse python3-future python3-icu python3-josepy python3-mock
    
     python3-parsedatetime python3-pbr python3-requests-toolbelt python3-rfc3339 python3-tz python3-zope.component
     
     python3-zope.event python3-zope.hookable

   
       sudo snap install --classic certbot
   
   certbot 1.14.0 from Certbot Project (certbot-eff✓) installed

   Install additional dependency package
   
         sudo apt install python3-certbot-nginx -y

   Confirm configurations using
   
         sudo nginx -t && sudo nginx -s reload
   
   nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
   
   nginx: configuration file /etc/nginx/nginx.conf test is successful

   
8. Request your certificate 
   
         sudo ln -s /snap/bin/certbot /usr/bin/certbot
   
   
         sudo certbot --nginx -d tooling4eou.tk -d www.tooling4eou.tk
   
   
   ![image](https://user-images.githubusercontent.com/78841364/116822681-2e72b400-ab4e-11eb-8a5b-7fe0004ea8e6.png)
   

   ![image](https://user-images.githubusercontent.com/78841364/116822704-56faae00-ab4e-11eb-83c4-44364985e627.png)

  
   
   Test secured access to your Web Solution by trying to reach https://tooling4eou.tK
   
   ![image](https://user-images.githubusercontent.com/78841364/116822764-bc4e9f00-ab4e-11eb-8898-5be38ce4f2a7.png)


   You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string.      Click on the padlock icon and you can see the details of the certificate issued for your website.

   
9. Set up periodical renewal of your SSL/TLS certificate
   
   By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

   Test renewal command in dry-run mode

         sudo certbot renew --dry-run
   
   Best practice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

   Edit the crontab file with the following command:

   crontab -e
   
   Add following line:

       * */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
   

   
   Side Self Study: Refresh your cron configuration knowledge by watching this video.


## Summary

In this project, I implemented an Nginx Load Balancing Web Solution with secured HTTPS connection with periodically updated SSL/TLS certificates.


## References

https://stackoverflow.com/questions/22143565/which-nginx-config-file-is-enabled-etc-nginx-conf-d-default-conf-or-etc-nginx
https://crontab.guru/#23_0-20/2_*_*_*

