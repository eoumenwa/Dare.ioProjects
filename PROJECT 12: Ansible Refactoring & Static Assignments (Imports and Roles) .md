## Ansible Refactoring & Static Assignments (Imports and Roles)

In this project we will continue working with ansible-config-mgt repository and make some improvements of the existing code by refactoring it, creating assignments, and 

learning how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows tasks to be organized and reused 

when needed.


### Code Refactoring

Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of 

refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.


### Step 1 - Jenkins job enhancement

Since every new change in the codes creates a separate directory which is not very convenient and consumes space on Jenkins servers with each subsequent change. This will be 

enhanced by introducing a new Jenkins project/job requiring Copy Artifact plugin.

1. Go to Jenkins-Ansible server and create a new directory called ansible-config-artifact for storing all artifacts after each build.

        sudo mkdir /home/ubuntu/ansible-config-artifact

2. Change permissions to this directory, so Jenkins could save files there
          
        chmod -R 0777 /home/ubuntu/ansible-config-artifact

3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

4. Create a new Freestyle project and name it save_artifacts.

   This project will be triggered by completion of existing ansible project. Configure it accordingly:
   
   a. Under General Tab, check "Discard old builds" and enter the max # of builds to keep
   
   b. Under Build Triggers, check "Build after other projects are built", enter the projects to watch (ansible_automation) and select "Trigger only if build is stable"

   The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. 

   c. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible_automation as a source project and /home/ubuntu/ansible-config-
     
      artifact as a target directory.
   
   
5. Test the set up by making some change in README.MD file inside ansible-config-mgt repository (inside master branch).
    
   If both Jenkins jobs have completed one after another - you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every       
   commit to the master branch.

![image](https://user-images.githubusercontent.com/78841364/120471399-a9f49a80-c372-11eb-86da-9622dc135c1e.png)

![image](https://user-images.githubusercontent.com/78841364/120471459-bbd63d80-c372-11eb-9a97-0766900b79f4.png)



### Step 2 - Refactor Ansible code by importing other playbooks into site.yml

1. Pull down the latest code from master (main) branch, and create a new branch named "refactor".

        ~/ansible/ansible-config-mgt$ git pull
        
        remote: Enumerating objects: 16, done.
        remote: Counting objects: 100% (15/15), done.
        remote: Compressing objects: 100% (12/12), done.
        remote: Total 12 (delta 7), reused 0 (delta 0), pack-reused 0
        Unpacking objects: 100% (12/12), 3.52 KiB | 300.00 KiB/s, done.
        From https://github.com/eoumenwa/ansible-config-mgt
           7f550c5..4e6812d  master                 -> origin/master
           a4113b1..4de3d24  feature01-prj11-layout -> origin/feature01-prj11-layout
        Updating 7f550c5..4e6812d
        Fast-forward
         README.md            | 43 +++++++++++++++++++++++++------------------
         playbooks/common.yml |  7 +++++++
         2 files changed, 32 insertions(+), 18 deletions(-)
        
        ubuntu@ip-172-31-36-216:~/ansible/ansible-config-mgt$
         
         ~/ansible/ansible-config-mgt$ git checkout -b refactor
                Switched to a new branch 'refactor'
         
         ~/ansible/ansible-config-mgt$ 

2. Within playbooks folder, create a new file and name it site.yml - This file will now be considered as an entry point into the entire infrastructure configuration. Other 

   playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that was    
   created previously.
   
        /ansible/ansible-config-mgt/playbooks$ touch site.yml
   
3. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored.

        ~/ansible/ansible-config-mgt$ mkdir static-assignments

4. Move common.yml file into the newly created static-assignments folder.

        ~/ansible/ansible-config-mgt$ mv playbooks/common.yml static-assignments/

5. Inside site.yml file, import common.yml playbook.
   
        ---
        - hosts: all
        - import_playbook: ../static-assignments/common.yml
   
   The code above uses built in import_playbook Ansible module.

   New folder structure should look like this;

        ├── static-assignments
        │   └── common.yml
        ├── inventory
            └── dev
            └── stage
            └── uat
            └── prod
        └── playbooks
            └── site.yml

6. Run ansible-playbook command against the dev environment

7. create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

        ---
        - name: update web, nfs and db servers
          hosts: webservers, nfs, db
          remote_user: ec2-user
          become: yes
          become_user: root
          tasks:
          - name: delete wireshark
            yum:
              name: wireshark
              state: removed

        - name: update LB server
          hosts: lb
          remote_user: ubuntu
          become: yes
          become_user: root
          tasks:
          - name: delete wireshark
            apt:
              name: wireshark-qt
              state: absent
              autoremove: yes
              purge: yes
              autoclean: yes
      
8. update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

        ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yml
        
 
9. Make sure that wireshark is deleted on all the servers by running wireshark --version

![image](https://user-images.githubusercontent.com/78841364/120863764-18934d00-c559-11eb-8472-2aa479ff1f7c.png)

       
## Step 3 - Configure UAT Webservers with a role ‘Webserver’

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly - Web1-UAT and Web2-UAT.

2. To create a role, create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

   There are two ways to create this folder structure:

  a. Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory

        mkdir roles

        cd roles

        ansible-galaxy init webserver

        ubuntu@ip-172-31-36-216:~/ansible/ansible-config-mgt/roles$ ansible-galaxy init webserver
        - Role webserver was created successfully

  b. Create the directory/files structure manually

        
After removing unnecessary directories and files, the roles structure should look like this

                └── webserver
                    ├── README.md
                      ├── defaults
                      │   └── main.yml
                      ├── handlers
                      │   └── main.yml
                      ├── meta
                      │   └── main.yml
                      ├── tasks
                      │   └── main.yml
                      └── templates

![image](https://user-images.githubusercontent.com/78841364/120867098-3bc0fb00-c55f-11eb-8912-d9e20c314eec.png)


3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of the 2 UAT Web servers

[uat-webservers]

<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

3b. Configure ssh connection as shown below on both UAT servers to enable connections from ansible to the servers

        Run
         sudo vi /etc/ssh/sshd_config
         Set password authentication yes
        Run
          sudo systemctl restart sshd
        Run
          sudo passwd ubuntu or sudo passwd ec2-user
          This will set a password
        Run
          ssh-copy-id ec2-user@private-IP-add


4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory 
   
        roles_path = /home/ubuntu/ansible/ansible-config-mgt/roles
   
   so Ansible could know where to find configured roles.

5. Go into tasks directory and within the main.yml file, start writing configuration tasks to do the following:

  Install and configure Apache (httpd service)

  Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.

  Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.

  Make sure httpd service is started

  Copy the content below to main.yml

        ---
        - name: install apache
          become: true
          ansible.builtin.yum:
            name: "httpd"
            state: present

        - name: install git
          become: true
          ansible.builtin.yum:
            name: "git"
            state: present

        - name: clone a repo
          become: true
          ansible.builtin.git:
            repo: https://github.com/<your-name>/tooling.git
            dest: /var/www/html
            force: yes

        - name: copy html content to one level up
          become: true
          command: cp -r /var/www/html/html/ /var/www/

        - name: Start service httpd, if not started
          become: true
          ansible.builtin.service:
            name: httpd
            state: started

        - name: recursively remove /var/www/html/html/ directory
          become: true
          ansible.builtin.file:
            path: /var/www/html/html
            state: absent

### Step 4 - Reference ‘Webserver’ role

1. Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where we will reference the role.

        touch uat-webservers.yml
        
        
        ---
        - hosts: uat-webservers
          roles:
             - webserver


   The entry point to ansible configuration is the site.yml file therefore we will refer to the uat-webservers.yml role inside site.yml as shown below

        ---
        - hosts: all
        - import_playbook: ../static-assignments/common.yml

        - hosts: uat-webservers
        - import_playbook: ../static-assignments/uat-webservers.yml

### Step 5 - Commit & Test

1. Commit changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the 
   files to Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.

2. Run the playbook against uat inventory

        sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

Your Ansible architecture now looks like this:

_images/project12_architecture.png

### Challenges

1. I tried to run common-del.yml on the development servers but it kept failing with the errors below (step 8)

        :~$ sudo ansible-playbook -i /home/ubuntu/ansible/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible/ansible-config-      
        mgt/playbooks/site.yml

        PLAY [all] ********************************************************************************************************************

        TASK [Gathering Facts] ********************************************************************************************************
        fatal: [172.31.xx.xx]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ec2-user@172.31.23.215: Permission denied (publickey,gssapi-
        keyex,gssapi-with-mic,password).", "unreachable": true}
        fatal: [172.31.xx.xx]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ec2-user@172.31.26.39: Permission denied (publickey,gssapi-
        keyex,gssapi-with-mic).", "unreachable": true}
        
    I finally ran the command without "sudo" and it worked
    
2. I could not run the playbook after Jenkins copied files to my selected path with the errors below

        ERROR! the role 'webserver' was not found in /home/ubuntu/ansible/ansible-config-mgt/playbooks/../static-assignments/roles:/home/ubuntu/ansible-config-         
        mgt/roles:/home/ubuntu/ansible/ansible-config-mgt/playbooks/../static-assignments

        The error appears to be in '/home/ubuntu/ansible/ansible-config-mgt/static-assignments/uat-webservers.yml': line 4, column 7, but may
        be elsewhere in the file depending on the exact syntax problem.

        The offending line appears to be:

          roles:
            - webserver
              ^ here


   I realized that the path to the roles was wrong in my ansible configuration file
   
3. I ran the playbook site.yml using the dry run mode (--check) and the result showed that there may be an issue with the get block of the playbook
       
       ~/ansible/ansible-config-mgt/playbooks$ ansible-playbook -i /home/ubuntu/ansible/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible/ansible-config-mgt/playbooks/site.yml --check
       TASK [webserver : clone a repo] ***********************************************************************************************
        fatal: [17x.xx.18.19]: FAILED! => {"changed": false, "msg": "Failed to find required executable git in paths: /sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin"}
        fatal: [17x.xx.24.11]: FAILED! => {"changed": false, "msg": "Failed to find required executable git in paths: /sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin"}

        PLAY RECAP ********************************************************************************************************************
        172.31.18.19               : ok=5    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
        172.31.24.11               : ok=5    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0 


### Summary

In this project, I learnt how to deploy and configure UAT Web Servers using Ansible imports and roles!

 




### References

http://man.hubwiz.com/docset/Ansible.docset/Contents/Resources/Documents/docs.ansible.com/ansible/playbooks_checkmode.html#check-mode-dry-run


