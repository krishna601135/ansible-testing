ansible

in ansible on manage nodes python should be installed


password less authentication:

ansible archeticture:

1.controlnode
control node(master) is where you install the ansible

As the devops engineer we provide the instructions to the ansible(where the ansible is installed) through yaml format(which are called yaml playbooks) or you can provide the instructions through the adhoc commands(which is command line)

what type of instructions just installing nginx on the manage nodes

- when i try to install nginx on the target machine they block because they ask for authentication
they are two ways to do authentication
1. password 2.providing ssh keys(private key and public key)

- since we are doing automation when our control node need to some installations on the manage nodes it should provide ssh keys and passwords for authentication where ansible need to wait for some time

- Instead we set up a prerequesite where we set up a passwordless authentication between your control node and manage nodes
so whenever you run the ansible it can directly talks to the manage nodes and execute the instructions





2. manage nodes(it can be instances or servers where you want to manages this servers through ansible)
where we want to do configurations on the machines are  called manage nodes

There is no need to install nodes on this manage nodes we just need see that python installed on all machines that we want to manage.





what is meant by password less authentication:

there are two virtual virtual machine A and virtual machine B

so whenever virtual machine A want to talks with virtual machine B

so you can tell the virtual machine B do not ask for password(authentication) whenever you get request from virtual machine A.

--> things we need to do for passwordless authentication:
1. intially whenever we are talking from virtual A to virtual B for the first time 
you need to provide the password or you can provide the pem file. This is only one time activity.

two ways
1. providing password  2.providing ssh

--> by default ec2 blocks the password authentication
the only way you can connect to the aws ec2 instance is ssh file or pem file

1.command to set passwordless authentication:

-> ssh-copy-id -f "-o IdentityFile /home/krishna/Downloads/demo_login.pem" ubuntu@13.126.138.221

and press Enter

And when second time logging into the ec2 machine you no need to provide pem file again simply you can enter following command

-> ssh ubuntu@13.126.138.221



2.password way authentication
Go to the file /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
Update PasswordAuthentication yes
Restart SSH -> sudo systemctl restart ssh


other info
sudo vim /etc/ssh
go to sudo vim /etc/ssh/sshd_config




-->how the ansible control node understands the what are the manage nodes?
Ans) using the process called inventory

its just a file we provide to the ansible, with in the file we will just put the names of tbe manage nodes

inventory can be written in two formats
initially we do it using inventory.ini file    --> before ansible was introduced this inventory.ini process is used(this is popular method)
but we can provide using inventory using yaml


inventory is heart of your ansible why because it will to your ansible control node to which servers need to talk(what are your manage nodes)


inventory.ini file default path --> /etc/ansible/hosts


adhoc commands for inventory.ini

--> ansible -i inventory.ini -m ping all
-m module called ping above we are using
- all -> means all the servers in inventory.ini


--> ansible -i inventory.ini -m shell -a "sudo apt update" all


##########Grouping the servers in the inventory file:###################33

inventory file

[webservers]
ubuntu@13.233.85.151

[appservers]
ubuntu@3.110.130.61

command to execute to specific group:  ansible -i inventory.ini -m ping webservers




##########

In ansible there are two ways to provide instructions

->first way is writing ansible playbooks[in the yaml format]  -> its like complete terraform file
your task is like to install oracle which involves 50 steps
your task is like to setup complete packaging for your database 
then we will go with playbooks
playbooks are resuable, you can version putting in git, and also tag them, and collabarte with other developers in our organisation



-> second way is adhoc commands   -> we will go for this when our task is simple(ex: installing apche on target servers, installing nodejs on 
targets servers,  creating a files on target servers, restarting your target servers)
this are like one step activites

 -




adhoc command to list etc folder on the target machines:
ansible -i inventory.ini -m shell -a "ls /etc/" --become all


##########################################DAY-03#####################################################3
Day-03 writing ansible playbook
- understanding yaml basics and ansible playbook structure
- introduction to ansible structure: playbook, play, modules, task and collections.
- hands on: writing a playbook to install apache2 and deploy a static app on aws.


ansible playbook structure:
-> ansible playbook structure is basically a yaml file

-> ansible playbook starts with ---

-> a playbook is list of plays
ansible playbook consists of

hosts:
remote-user:
tasks:
  - module
  - module
  - module

state: present(install)
state: absent(uninstall)

here module is a basically execution of a task

executing firstplaybook installing apache server on target machines and copy file from local machine to the target machine in path
/var/www/html

executing the playbook command:
ansible-playbook -i inventory.ini first-playbook.yaml





##########################################DAY-04#####################################################3

- An ansible playbook is a collection of palys where each play consists of fileds 
 - hosts -> on which hosts this ansible playbook should execute
   vars  -> variables
   remote-user -> using which user this playbook should be executed on the manage nodes
   tasks -> a collection of module should be executed

   using adhoc command "ansible-playbook -i inventory first-playbook" we submit to the control node where ansible installed 
   and this control node takes this playbook and exectues on set of manage node using remote-user we mentioned  


######
reason why to use ansible roles:

Assume there is ansible playbook yaml file which consits of multiple to be exectued nearly that file includes 
2000 lines

if someone open this file and start to understand they feel difficult due to the number of lines it had.

we loose in interest in reading that file

using ansible roles we can split this to better readibility and modularity

where we have sections like files, defaults, template, tasks, handlers, metadata 
in each section we have yaml files.

creating a role:
ansible-galaxy role init httpd

roles can used in complicated tasks
you given a task in your organisation
like installing kuberenetes on worker(data plane) and master nodes(control plane)

##########Ansible Roles#####################

An Ansible role is a reusable, self-contained unit of automation that is used to organize and manage tasks, variables, files, templates, and handlers in a structured way.

Roles help to encapsulate and modularize the logic and configuration needed to manage a particular system or application component.

This modular approach promotes reusability, maintainability, and consistency across different playbooks and environments.
Key Components of an Ansible Role
Tasks

The main list of actions that the role performs.
Handlers

Tasks that are triggered by changes in other tasks, typically used for actions like restarting services.
Files

Static files that need to be transferred to managed hosts.
Templates

Jinja2 templates that can be rendered and transferred to managed hosts.
Vars

Variables that are used within the role.
Defaults

Default variables for the role, which can be overridden.
Meta

Metadata about the role, including dependencies on other roles.
Library

Custom modules or plugins used within the role.
Module_defaults

Default module parameters for the role.
Lookup_plugins

Custom lookup plugins for the role.
Directory Structure of an Ansible Role

An Ansible role follows a specific directory structure:
link: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html


############################################
Why Use Ansible Roles?
Modularity

Roles allow you to break down complex playbooks into smaller, reusable components. Each role handles a specific part of the configuration or setup.
Reusability

Once created, roles can be reused across different playbooks and projects. This saves time and effort in writing redundant code.
Maintainability

By organizing related tasks into roles, it becomes easier to manage and maintain the code. Changes can be made in one place and applied consistently wherever the role is used.
Readability

Roles make playbooks cleaner and easier to read by abstracting away the details into logically named roles.
Collaboration

Roles facilitate collaboration among team members by allowing them to work on different parts of the infrastructure independently.
Consistency
Using roles ensures that the same setup and configuration procedures are applied uniformly across multiple environments, reducing the risk of configuration drift.





###############Day-05 | Ansible Galaxy Tutorial | Write less code, Use existing roles from galaxy############################





####################Day-06 | Create Resources on AWS using Ansible | Ansible Variables Demo | Ansible Vault Demo###################

collections: talks to api of aws cloud, azure, gcp etc

on ansible control node module executed and module talks to aws apis

here in this case ansible python boto3 to talk to the aws apis

prerequisites:
install boto3

-> ansible-galaxy collection install amazon.aws


##########ansible vault###########################
for encrypting accesskey and secretkey
-> openssl rand -base64 2048 > vault.pass
-> ansible-value create group_vars/all/pass.yml --vault-password-file vault.pass
now it will open vim editor
in this editor the enter accesskey and secret key it will encrypt them 
