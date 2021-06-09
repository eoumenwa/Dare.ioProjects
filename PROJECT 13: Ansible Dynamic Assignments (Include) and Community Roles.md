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


git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
Now, if you are satisfied with your codes, you can create a Pull Request and merge it to main branch on GitHub.

## Load Balancer roles

We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

Nginx

Apache

With your experience on Ansible so far you can:

Decide if you want to develop your own roles, or find available ones from the community
Update both static-assignment and site.yml files to refer the roles
Important Hints:

Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one - this is where you can make use of variables.
Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.
Declare another variable in both roles load_balancer_is_required and set its value to false as well
Update both assignment and site.yml files respectively
loadbalancers.yml file

- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
site.yml file

     - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 
Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

enable_nginx_lb: true
load_balancer_is_required: true
The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.

To test this, you can update inventory for each environment and run Ansible against each environment.

### Summary

In this project, I learnt how to deploy and configure UAT Web Servers using Ansible imports and roles!

### References

https://github.community/t/add-a-folder/2304/2

https://koukia.ca/how-to-remove-local-untracked-files-from-the-current-git-branch-571c6ce9b6b1

Congratulations!
You have learned and practiced how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.
