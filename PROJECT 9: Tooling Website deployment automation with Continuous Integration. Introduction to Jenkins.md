# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

In this project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub will be automatically be updated to the Tooling Website.


Continuous Integration
Continuous Delivery
Continuous Deployment.

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server

## Target setup

![image](https://user-images.githubusercontent.com/78841364/115937552-41a6c500-a466-11eb-8719-8b34014c106b.png)


## Step 1 - Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”
2. Install JDK (since Jenkins is a Java-based application)
   sudo apt update
   sudo apt install default-jdk-headless
Install Jenkins
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
Make sure Jenkins is up and running

sudo systemctl status jenkins
By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound Rule in your EC2 Security Group
_images/open_port8080.png

Perform initial Jenkins setup.
From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

_images/unlock_jenkins.png

Retrieve it from your server:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Then you will be asked which plugings to install - choose suggested plugins.

_images/jenkins_plugins.png

Once plugins installation is done - create an admin user and you will get your Jenkins server address.

The installation is completed!

_images/jenkins_ready.png


## Summary
In this project, I learnt how to implement a Continous Integration solution using Jenkins CI which involved pushing commits directly from github automatically using a web-hook and also copying build artifacts over SSH from jenkins to the NFS-Server which also makes our webservers (being mnounted) to get updates to /var/www/html for website updates
