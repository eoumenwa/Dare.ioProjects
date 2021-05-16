# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

  In this project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub will be automatically be   updated to the Tooling Website.

  Continuous integration:
  The automated building and testing of an application on every new commit.

  Continuous delivery:
  A state where an application is always ready to be deployed. A manual step is required to actually deploy the application.

  Continuous deployment:
  The automation of building, testing, and deploying. If all tests pass, every new commit will push new code through the entire development pipeline to     
  production with no manual intervention..

 ![image](https://user-images.githubusercontent.com/78841364/117133210-f64bbb00-ad71-11eb-9323-1891c2c57c9f.png)


## Task
   Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS    server

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

   Ensure that Jenkins is up and running

        sudo systemctl status jenkins

    Output:
    
       ● jenkins.service - LSB: Start Jenkins at boot time
    
        Loaded: loaded (/etc/init.d/jenkins; generated)
     
        Active: active (exited) since Fri 2021-04-23 23:07:49 UTC; 2min 49s ago
     
        Docs: man:systemd-sysv-generator(8)
       
        Tasks: 0 (limit: 1160)
      
        Memory: 0B
     
        CGroup: /system.slice/jenkins.service

    
    By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound Rule in your EC2 Security Group
  

3. Perform initial Jenkins setup.
   From the browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

4. Enter the default admin password (retrieve it from server) by running

        sudo cat /var/lib/jenkins/secrets/initialAdminPassword

5. Enable Jenkins

       sudo systemctl enable jenkins

   Output
   
        jenkins.service is not a native service, redirecting to systemd-sysv-install.
        
        Executing: /lib/systemd/systemd-sysv-install enable jenkins
        
6. Install suggested plugins.

  

## Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

  1. Enable webhooks in GitHub repository settings

     ![image](https://user-images.githubusercontent.com/78841364/118402347-8eca2100-b637-11eb-90bf-2245d8a28912.png)

     
     Payload URL http://3.143.243.155:8080/github-webhook/
 
  2. On Jenkins web console, click “New Item” and create a “Freestyle project”

  3. Provide github repository URL


  4. Under freestyle project configuration/Git repository, provide the link to Tooling GitHub repository and credentials (user/password)
     so Jenkins could access files in the repository.

  5. Save the configuration and run the build manually by clicking “Build Now” button.
  
  6. Open the build and check in “Console Output” if it has run successfully.


## Automation
 
 Click “Configure”  job/project and add these two configurations
 
   1. Configure triggering the job from GitHub webhook:


   2. Configure “Post-build Actions” to archive all the files - files resulted from a build are called “artifacts”.

  Make some change in any file in GitHub repository and push the changes to the master branch. Check to see if new build has been launched automatically   
  (by webhook) and its results - artifacts, saved on Jenkins server.
 
      By default, the artifacts are stored on Jenkins server locally

            ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

 
 ## Step 3 - Configure Jenkins to copy files to NFS server via SSH
  
  1. Install “Publish Over SSH” plugin.
     
     On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.
      
     On “Available” tab search for “Publish Over SSH” plugin and install it


  2. Configure the job/project to copy artifacts over to NFS server.
    
     On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

  3. Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

  4. Provide a private key (content of .pem file that was used to connect to NFS server via SSH/Putty)
     
     Arbitrary name
     
     Hostname - can be private IP address of  NFS server
   
     Username - ec2-user (since NFS server is based on EC2 with RHEL 8)
  
     Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
     
  5. Test the configuration and make sure the connection returns Success. TCP port 22 on NFS server must be open to receive SSH connections.


  6. Save the configuration, open  Jenkins job/project configuration page and add another  “Post-build Action”


  7. Configure it to send all files produced by the build into previously define remote directory using **.

 
  
  ## Test

  Make changes to README.MD file in GitHub Tooling repository.

  Webhook will trigger a new job and in the “Console Output” of the job with the following result:

  SSH: Transferred 25 file(s)
  Finished: SUCCESS
  To make sure that the files in /mnt/apps have been udated - connect via SSH/Putty to your NFS server and check README.MD file

      cat /mnt/apps/README.md
 
  Cat out the README.md file and confirm the changes previously made in GitHub - the job works as expected.


  ## Challenges

  Got the error message below when I tried to test for SSH transfer of build artifacts
  ERROR: Exception when publishing, exception message [Permission denied]
  Build step 'Send build artifacts over SSH' changed build result to UNSTABLE
  Finished: UNSTABLE


## Resolution

Changed ownership of /mnt/apps from root to ec2-user as shown in the lines below
  nnsudo chown -R ec2-user: /mnt/apps


## Summary

In this project, I learnt how to implement a Continous Integration solution using Jenkins CI which involved pushing commits directly from github automatically using a web-hook and also copying build artifacts over SSH from jenkins to the NFS-Server which also makes our webservers (being mnounted) to get updates to /var/www/html for website updates
