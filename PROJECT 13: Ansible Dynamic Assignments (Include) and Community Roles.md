## Ansible Dynamic Assignments (Include) and Community Roles


In this project we will introduce dynamic assignments by using include module.

import = Static
include = Dynamic

When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.


### Introducing Dynamic Assignment Into Our structure

1. In  https://github.com/oeumenwa/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.

2. Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook    
   later. For now, we will keep building up the structure.

3. Create a folder to keep each environment’s variables file. Create a new folder env-vars, then for each environment, create new YAML nfiles which we will use to set   

    variables.

    The layout should now look like this.

        ├── dynamic-assignments
        │   └── env-vars.yml
        ├── env-vars
        └── dev.yml
            └── stage.yml
            └── uat.yml
            └── prod.yml
        ├── inventory
            └── dev
            └── stage
            └── uat
            └── prod
        ├── playbooks
            └── site.yml
        └── static-assignments
            └── common.yml
            └── webservers.yml
        
 ![image](https://user-images.githubusercontent.com/78841364/121199143-b11f1b00-c840-11eb-8786-953226d973d9.png)


4. Paste the instruction below into the env-vars.yml file.

        ---
        - name: collate variables from env specific file, if it exists
          include_vars: "{{ item }}"
          with_first_found:
            - "{{ playbook_dir }}/../env_vars/{{ "{{ inventory_file }}.yml"
            - "{{ playbook_dir }}/../env_vars/default.yml"
          tags:
            - always


### Note

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:

      include_role
      include_tasks
      include_vars

In the same version, variants of import were also introduces, such as:

      import_role
      import_tasks

We made use of a special variables {{ playbook_dir }} and {{ inventory_file }}. 

{{ playbook_dir }} will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. 

{{ inventory_file }} will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.

The variables are included using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.


5. Update site.yml with dynamic assignments

   Update site.yml file to make use of the dynamic assignment.Ssite.yml should be as shown below;

         ---
         - name: Include dynamic variables 
           hosts: all
           tasks:
             - import_playbook: ../static-assignments/common.yml 
             - include_playbook: ../dynamic-assignments/env-vars.yml
           tags:
             - always

         - name: Webserver assignment
           hosts: webservers
             - import_playbook: ../static-assignments/webservers.yml

### Community Roles

1. Inside roles directory create new MySQL role with ansible-galaxy install geerlingguy.mysql. This should download a ready to use ansible role    for MySQL database and install MySQL package, create a database and configure users.

         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ ansible-galaxy install geerlingguy.mysql
         
         - downloading role 'mysql', owned by geerlingguy
         - downloading role from https://github.com/geerlingguy/ansible-role-mysql/archive/3.3.1.tar.gz
         - extracting geerlingguy.mysql to /home/ubuntu/ansible/ansible-config-mgt/roles/geerlingguy.mysql
         - geerlingguy.mysql (3.3.1) was installed successfully

2. Rename geerlingguy.mysql folder to mysql.
               
           ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ mv geerlingguy.mysql/ mysql


3. Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.
   
   Edit the file below
   
         /home/ubuntu/ansible/ansible-config-mgt/roles/mysql/defaults/main.yml
        


4. Upload the changes into GitHub:

         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git status
         On branch dynamic-assignments
         Your branch is up to date with 'origin/dynamic-assignments'.

         Changes not staged for commit:
           (use "git add <file>..." to update what will be committed)
           (use "git restore <file>..." to discard changes in working directory)
                 modified:   dynamic-assignments/env-vars.yml
                 modified:   playbooks/site.yml

         Untracked files:
           (use "git add <file>..." to include in what will be committed)
                 roles/mysql/

         no changes added to commit (use "git add" and/or "git commit -a")
         
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git add .
         
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git commit -m "update mysql setup roles files"
         
         [dynamic-assignments 1468e61] update mysql setup roles files
          35 files changed, 1221 insertions(+), 5 deletions(-)
          create mode 100644 roles/mysql/.ansible-lint
          .
          .
          .
          create mode 100644 roles/mysql/templates/user-my.cnf.j2
          create mode 100644 roles/mysql/vars/Archlinux.yml
          create mode 100644 roles/mysql/vars/Debian-10.yml
          create mode 100644 roles/mysql/vars/Debian.yml
          create mode 100644 roles/mysql/vars/RedHat-7.yml
          create mode 100644 roles/mysql/vars/RedHat-8.yml
        
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git push

         Enumerating objects: 57, done.
         Counting objects: 100% (57/57), done.
         Compressing objects: 100% (44/44), done.
         Writing objects: 100% (51/51), 17.16 KiB | 605.00 KiB/s, done.
         Total 51 (delta 3), reused 0 (delta 0)
         remote: Resolving deltas: 100% (3/3), completed with 1 local object.
         To https://github.com/eoumenwa/ansible-config-mgt.git
            92c5f74..1468e61  dynamic-assignments -> dynamic-assignments

Now, if you are satisfied with the codes, create a Pull Request and merge it to main branch on GitHub.

### Load Balancer roles

We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively using available ones from the community

1. Install geerlinguy Nginx role and rename as appropriate

      ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ ansible-galaxy install geerlingguy.nginx
      
      - downloading role 'nginx', owned by geerlingguy
      - downloading role from https://github.com/geerlingguy/ansible-role-nginx/archive/3.0.0.tar.gz
      - extracting geerlingguy.nginx to /home/ubuntu/ansible/ansible-config-mgt/roles/geerlingguy.nginx
      - geerlingguy.nginx (3.0.0) was installed successfully
     
      ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ mv geerlingguy.nginx/ nginx
      
      ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$

2. Install geerlinguy Apache role and rename as appropriate

         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ ansible-galaxy install geerlingguy.apache
         
         - downloading role 'apache', owned by geerlingguy
         - downloading role from https://github.com/geerlingguy/ansible-role-apache/archive/3.1.4.tar.gz
         - extracting geerlingguy.apache to /home/ubuntu/ansible/ansible-config-mgt/roles/geerlingguy.apache
         - geerlingguy.apache (3.1.4) was installed successfully
       
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ mv geerlingguy.apache/ apache
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt/roles$ 



![image](https://user-images.githubusercontent.com/78841364/121516830-a6899080-c9bc-11eb-82b0-971bc65bd352.png)


3. Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
   
   Note: Since we cannot use both Nginx and Apache load balancer, we need to add a condition to enable either one using variables.

4. Set both values in 3 above to false as in enable_nginx_lb: false and enable_apache_lb: false.

5. Declare another variable in both roles load_balancer_is_required and set its value to false as well

   ![image](https://user-images.githubusercontent.com/78841364/121675506-7ce66d00-ca81-11eb-9c95-4c36349f928c.png)

   ![image](https://user-images.githubusercontent.com/78841364/121675741-cc2c9d80-ca81-11eb-8a05-9f9b1f40651d.png)


6. Update both static-assignment and site.yml files as shown below

   loadbalancers.yml file

      ---
      - hosts: lb
        become: yes
        roles:
          - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
          - { role: apache, when: enable_apache_lb and load_balancer_is_required }
      
      
      site.yml file

     - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 

7. Use env-vars/uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

8. Activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

         enable_nginx_lb: true
         load_balancer_is_required: true

9. Use the same setting for apache LB, so we can switch it by setting respective environmental variable to true and other to false.

10. Update inventory for each environment  
     
         edit /home/ubuntu/ansible/ansible-config-mgt/inventory/uat.yml 
         
  
11. Update git

         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git status
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git add .
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git commit -m "configured load balancer roles"
         ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ git push

12. Create ansible.cfg in root directory using the configuration below

            [defaults]
            inventory = /home/ubuntu/ansible/ansible-config-mgt/inventory
            roles_path = /home/ubuntu/ansible/ansible-config-mgt/roles
            timeout = 160
            callback_whitelist = profile_tasks
            log_path=~/ansible.log
            host_key_checking = False
            gathering = smart
            ansible_python_interpreter=/usr/bin/python3

13. Export ansible.cfg file as below
            
           ubuntu@jenkins-ansible:~/ansible/ansible-config-mgt$ export ANSIBLE_CONFIG=/home/ubuntu/ansible/ansible-config-mgt/ansible.cfg

14. Run Ansible against each environment

    ansible-playbook -i /home/ubuntu/ansible/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible/ansible-config-mgt/playbooks/site.yml
    
   ![image](https://user-images.githubusercontent.com/78841364/121728626-87bdf380-cabb-11eb-9fd0-e995dfc30a7a.png)


### Challenges

1. Playbook had some errors as shown in step 14. This was resolved by applying steps 12 and 13 (creating and exporting ansible.cfg file)

2. Ran the playbook with the following errors

  ![image](https://user-images.githubusercontent.com/78841364/123524376-cc3eb700-d697-11eb-9b1f-ab1f1d55b048.png)

  ![image](https://user-images.githubusercontent.com/78841364/123571257-d647df00-d797-11eb-8cfc-e819c5dfee6b.png)

  After several attempts at troubleshooting, I realized that I had to stop manually stop apache or nginx from running in order to successfully install nginx or apache

### Summary

In this project, I was able to learn and practice how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.

### References

https://github.community/t/add-a-folder/2304/2

https://koukia.ca/how-to-remove-local-untracked-files-from-the-current-git-branch-571c6ce9b6b1

https://professional-pbl.darey.io/en/latest/project13.html#community-roles

https://galaxy.ansible.com/geerlingguy/nginx

https://galaxy.ansible.com/geerlingguy/apache

https://severalnines.com/database-blog/introduction-mysql-deployment-using-ansible-role
