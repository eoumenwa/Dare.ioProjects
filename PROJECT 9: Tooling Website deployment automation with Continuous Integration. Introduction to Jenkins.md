# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

  In this project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub will be automatically be   updated to the Tooling Website.

  Continuous integration
  The automated building and testing of an application on every new commit.

  Continuous delivery
  A state where an application is always ready to be deployed. A manual step is required to actually deploy the application.

  Continuous deployment
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


## Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, We will configure a simple Jenkins job/project. This job will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in GitHub repository settings
_images/webhook_github.gif

Go to Jenkins web console, click “New Item” and create a “Freestyle project”
_images/create_freestyle.png

To connect GitHub repository, we need to provide its URL which cannbe copied from the repository itself

_images/github_url.png

In configuration of Jenkins freestyle project choose Git repository, provide there the link to Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

_images/github_add_jenkins.png

Save the configuration and run the build manually by clicking “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under #1
_images/jenkins_run1.png

You can open the build and check in “Console Output” if it has run successfully.

If so - congratulations! You have just made your very first Jenkins build!


## Automation

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click “Configure” your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

_images/jenkins_trigger.png

Configure “Post-build Actions” to archive all the files - files resulted from a build are called “artifacts”.

_images/archive_artifacts.gif

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server.

_images/build_success_archive.png

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

Step 3 - Configure Jenkins to copy files to NFS server via SSH
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called “Publish Over SSH”.

Install “Publish Over SSH” plugin.
On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.

On “Available” tab search for “Publish Over SSH” plugin and install it

_images/plugin_ssh_install.png

Configure the job/project to copy artifacts over to NFS server.
On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
Arbitrary name
Hostname - can be private IP address of your NFS server

Username - ec2-user (since NFS server is based on EC2 with RHEL 8)
Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

_images/publish_ssh_config.png

Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”

_images/send_build.png

Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories - so we use **. If you want to apply some particular pattern to define which files to send - use this syntax.

_images/send_build1.png

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the “Console Output” of the job you will find something like this:

SSH: Transferred 25 file(s)
Finished: SUCCESS
To make sure that the files in /mnt/apps have been udated - connect via SSH/Putty to your NFS server and check README.MD file

cat /mnt/apps/README.md
If you see the changes you had previously made in your GitHub - the job works as expected.

## Challenges

Got the error message below when I tried to test for SSH transfer of build artifacts
ERROR: Exception when publishing, exception message [Permission denied]
Build step 'Send build artifacts over SSH' changed build result to UNSTABLE
Finished: UNSTABLE

## Resolution

Changed ownership of /mnt/apps from root to ec2-user as shown in the lines below
 sudo chown -R ec2-user: /mnt/apps



## Summary

In this project, I learnt how to implement a Continous Integration solution using Jenkins CI which involved pushing commits directly from github automatically using a web-hook and also copying build artifacts over SSH from jenkins to the NFS-Server which also makes our webservers (being mnounted) to get updates to /var/www/html for website updates
