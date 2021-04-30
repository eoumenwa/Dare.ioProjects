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

   Restart Nginx and make sure the service is up and running

   sudo systemctl restart nginx

   sudo systemctl status nginx
   
   

   Side Self Study: Read more about HTTP load balancing methods and features supported by Nginx on this page
   

## Part 2 - Register a new domain name and configure secured connection using SSL/TLS certificates

In the following steps, we shall make necessary configurations to make connections to our Tooling Web Solution secured!
In order to get a valid SSL certificate - we need to registrar a new domain name, uing any Domain name registrar - a company that manages reservation of domain names. The most popular ones are: Godaddy.com, Domain.com, Bluehost.com. we shall howevevr be using my.freenom.com in this project

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
   
   Create an A record on AWS Route 53 pointing to the LB elastic IP (We use a static IP address to prevent changes in IP after an EC2 instance is rebooted. 
   
   Input the Elastic IP and leave everything in default then click create
   
   Create another A record for www referencing Nginx LB Elastic IP
   
   We have that LB, Route 53 and Domain are noe connected
   
   ![image](https://user-images.githubusercontent.com/78841364/116737905-c8582680-a9bf-11eb-9234-f560b54ca308.png)

 
5. Next connect LB instance to the terminal
   
   

Side Self Study: Read about different DNS record types and learn what they are used for.


6. Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol - http://<your-domain-name.com>

   Configure Nginx to recognize your new domain name
   Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com

Install certbot and request for an SSL/TLS certificate
Make sure snapd service is active and running

sudo systemctl status snapd
Install certbot

sudo snap install --classic certbot
Request your certificate (just follow the certbot instructions - you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4).

sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
Test secured access to your Web Solution by trying to reach https://<your-domain-name.com>

You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string. Click on the padlock icon and you can see the details of the certificate issued for your website.







