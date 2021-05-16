## Ansible Configuration Management - Automate Project 7 to 10


### Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. In the current architecture, the webservers are inside a secured network which cannot be reached directly from the Internet. This implies that even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server - it provides better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets - Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

   ![image](https://user-images.githubusercontent.com/78841364/118401574-32b1cd80-b634-11eb-8ae5-b16730057fae.png)

### Task
Install and configure Ansible client to act as a Jump Server/Bastion Host
Create a simple Ansible playbook to automate servers configuration

### Step 1 - Install and configure Ansible on EC2 Instance

1. Update Name tag on existing Jenkins EC2 Instance to Jenkins-Ansible for running playbooks.

2. In GitHub account create a new repository and name it ansible-config-mgt.

3. Install Ansible
   
       sudo apt update

       sudo apt install ansible

  Check your Ansible version by running ansible --version

 ![image](https://user-images.githubusercontent.com/78841364/118402996-62fc6a80-b63a-11eb-9464-541909f6b750.png)


4. Configure Jenkins build job to save your repository content every time change is made.

   ![image](https://user-images.githubusercontent.com/78841364/118403463-6f81c280-b63c-11eb-8267-809dbbb12168.png)
   
   - Install Jenkins
   - Perform initial Jenkins setup
   - From the browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080
   - Retrieve password from server using sudo cat /var/lib/jenkins/secrets/initialAdminPassword.
      

5. Create a new Freestyle project ansible in Jenkins and point it to ‘ansible-config-mgt’ repository.


Configure Webhook in GitHub and set webhook to trigger ansible build.
Configure a Post-build job to save all (**) files, like you did it in Project 9.
Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
Note: Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:

_images/jenkins_ansible.png

Tip Every time you stop/start your Jenkins-Ansible server - you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

Step 2 - Prepare your development environment using Visual Studio Code
First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable - you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs - Visual Studio Code (VSC), you can get it here.

After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

Step 3 - Begin Ansible Development
In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.
Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool - include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about - a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

Checkout the newly created feature branch to your local machine and start building your code and directory structure
Create a directory and name it playbooks - it will be used to store all your playbook files.
Create a directory and name it inventory - it will be used to keep your hosts organised.
Within the playbooks folder, create your first playbook, and name it common.yml
Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
Step 4 - Set up an Ansible Inventory
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host - for this you need to copy your private (.pem) key to your server. Do not forget to change permissions to your private key chmod 400 key.pem, otherwise EC2 will not accept the key. Now you need to import your key into ssh-agent:

eval `ssh-agent -s`
ssh-add <path-to-private-key>
Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update your inventory/dev.yml file with this snippet of code:

[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
Step 5 - Create a Common Playbook
It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    yum:
      name: wireshark
      state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    apt:
      name: wireshark
      state: latest
Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

Create a directory and a file inside it
Change timezone on all servers
Run some shell script
…
For a better understanding of Ansible playbooks - watch this video from RedHat and read this article.

Step 6 - Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes - it is also called “Four eyes principle”.

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

use git commands to add, commit and push your branch to GitHub.
git status

git add <selected files>

git commit -m "commit message"
Create a Pull request (PR)
Wear a hat of another developer for a second, and act as a reviewer.
If the reviewer is happy with your new feature development, merge the code to the master branch.
Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.
Once your code changes appear in master branch - Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

Step 7 - Run first Ansible test
Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/playbooks/common.yml
Note: Previous command we ran without sudo, this is because we had added an ssh key to ssh-agent for our regular user. If you try to run this command with sudo you will have to explicitly pass the ssh key with --private-key <path-to-private-key> parameter.

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

_images/wireshark.png

Your updated with Ansible architecture now looks like this:

_images/ansible_architecture.png

Optional step - Repeat once again
Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a servers fleet of any size with just one command!

## Summary

In this project, I learnt how to automate routine tasks using Ansible Configuration Management and writing code using declarative language such as YAML.











## References

https://stackoverflow.com/questions/33762738/specifying-the-os-ansible

https://medium.com/@christyjacob4/using-vscode-remotely-on-an-ec2-instance-7822c4032cff
