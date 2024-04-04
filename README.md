# ansible
1.Ansible is the opensource
2.Agentless
3.Works on push and pull mechanism
4.Ansible should be able to ssh into other nodes
5.This is the Automated way


#2 ways to use ansible

1.Manual ways
2.Automated way(YAML:playbooks)

How to install ansible curl https://gitlab.com/thecloudcareers/opensource/-/raw/master/ansible/install.sh?ref_type=headsle |sudo bash

To ansible we need to list the servers that needs to be managed by ANSIBLE and we call that file the inventory file.

Inventory file 
common set of username and password

Production and the non prodution password will be seperated

Ansible is all about module
if we need to do anythingsuch as install,start,stop we need to use the module



Ansible Interview Questions
1.	Why ansible is famous and list me 3 points on why do I need to adopt ?
Opensource,
Agentless
Purely works on SSH
Ansible server should be able to SSH with config management
Other tools such as chef or puppet needs agent to run on it
2.	What is the default ansible mechanism ?
By default, Ansible represents which machines it manages using a very simple INI file that puts all of your managed machines in groups of your own choosing.
3.	If you're asked to decided on the usage of Pull vs Push, what would you select and would be the factors that determine the usage ?
Pull mechanism-Use push mechanism when you can auto scale also when we are able to have 10 catalogues at the same time. Install ansible in 1 server
Push mechanism-No concept of autoscaling use push mechanism in which you install ansible on a server and create and work on components on the same.
Install ansible on all the servers
4.	Assume your infra is dynamic that scales out & in dynamically and in this case, would you prefer to use push or pull ?
Pull mechanism.
5.	What is the need of ROLES in ansible ?
Organization. Ansible Roles provide a structured way to organize tasks, templates, files, and variables. This structure makes it easier to manage complex automation setups, as everything related to a specific role is contained within its directory.
6.	If the values of the variables are declared both in vars/main.yml and default/main.yml, which among these will have priority ?
Priority will be given to the variable declared in the vars/main.yml
7.	What is the earliest way to organize the variables in files based on the environment ? Can we supply the variables in files ?
Yes we can supply variables in files by structuring the main.yml file below the vars tree structure.
8.	How to call a role from another role using the task name ?
- name: Run tasks/other.yaml instead of 'main'
  ansible.builtin.include_role:
    name: myrole
    tasks_from: other
- name: Creating {{APPUSER}} user account
  ansible.builtin.include_role:
    name: common
    tasks_from: create_user 
9.	What all modules have you used in Ansible and which version of ansible are you using ?
Ansible version used is 9
Modules used are as below:
Configuring template or file:
Ansible.buildin.template
Restart the service:
Ansible.buildin.service/systemd_service
User Creation:
Ansible.buildin.user
Download and extract a file:
Ansible.buildin.unarchieve
Copy file:
Ansible.buildin.copy
Dnf installs:
Ansible.buildin.dnf
To refer to the task:
Ansible.buildin.include_role
Shell command execution:
Ansible.buildin.shell
Npm installs:
Communit.general.npm
Python pip installation:
Ansible.buildin.pip
Package installation:
Ansible.buildin.package
Fetch information from the log:
Ansible.buildin.slurp
Module for mysql community:
Community.mysql.mysql_info
To register the output of the particular execution:
Ansible.buildin.set_fact
Mysql:
Community.mysql.mysql_db:
Rabbit mq:
Community.rabbitmq.rabbitmq_user:
Replace a IP:
Ansible.buildin.replace:
10.	What is the notify keyword in Ansible? Scenario, I want to make sure that service should only be restarted if there is a change in the file, how can you accomplish that ?
 
11.	What is ROLE DEPENDENCY and what's the purpose of it and when to use that ?
roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""

12.What is the purpose of the become directive in Ansible? Can we run a playbook with 5 tasks as a root user and x-task in that 5 tasks as centos user
- name: Run a command as the apache user
  command: somecommand
  become: true
  become_user: apache

For example, to manage a system service (which requires root privileges) when connected as a non-root user, you can use the default value of become_user (root):
- name: Ensure the httpd service is running
  service:
    name: httpd
    state: started
  become: true

12.	What's the purpose of rescue module and when do you use that ?
# These 3 tasks I would like to run only for the first time and not if the password is already changes
# If any of the task in the block fails, then all the tasks in the rescue will be executed
# It's like IF ELSE Condition

13.	Situation, if any of the 3 tasks in a playbook fails then I would like to run 5 specific tasks on the 3 tasks failure, how can we deal that ?
- name: validating the password 
  block:
    - name: Get MySQL version with non-default credentials
      community.mysql.mysql_info:
        login_user: root
        login_password: "{{MYSQL_PSW}}"
        filter: version
  rescue:
    - name: Capturing the bash exit code 
      ansible.builtin.set_fact:
        DEFAULT_PASSWORD: "{{psw_log.content|b64decode | regex_findall('.*temporary password.*')| join('') | split(' ')|last}}"

    - name: Changing the default password 
      ansible.builtin.shell: echo "ALTER USER 'root'@'localhost' IDENTIFIED BY 'RoboShop@1'" | mysql  --connect-expired-password -uroot -p"{{DEFAULT_PASSWORD}}"

    - name: Uninstalling Password Validate Plugin
      ansible.builtin.shell: echo "uninstall plugin validate_password" | mysql -uroot -pRoboShop@1 

14.	What is a BLOCK in ansible ?
- name: validating the password 
  block:
    - name: Get MySQL version with non-default credentials
      community.mysql.mysql_info:
        login_user: root
        login_password: "{{MYSQL_PSW}}"
        filter: version

15.	How can we extract a specific string consider a password from a file that's available on a remote host

- name: Extracting {{COMPONENT}} password file 
  ansible.builtin.slurp:
    src: /var/log/mysqld.log
  register: psw_log

16.	What's the main purpose of SLURP function in ansible ?
Fetch infor from the log:
Ansible.buildin.slurp
- name: Extracting {{COMPONENT}} password file 
  ansible.builtin.slurp:
    src: /var/log/mysqld.log
  register: psw_log
17.	What is the different between collections vs module ?
Collections are a distribution format for Ansible content that can include playbooks, roles, modules, and plugins. As modules move from the core Ansible repository into collections, the module documentation will move to the collections pages. You can install and use collections through Ansible Galaxy.
18.	How can we control the facts not to be collected by Ansible Controller ?
Ansible facts are gathered using the setup module, which runs in the background every time. But this can be switched off by using mentioning gather_facts: no in the playbook.
19.	What is the port number used by ansible ?
Port SSH
20.	What are the packages that needs to be installed on remote nodes that needs to be controlled by ansible ?
Should install Ansible on the control node if it is Push.
Ensure to install asible on all the servers if it is pull.
21.	Why ansible is referred as AGENT-lESS
Ansible is agentless, which means the nodes it manages do not require any software to be installed on them. Ansible reads information about which machines you want to manage from your inventory. Ansible has a default inventory file, but you can create your own and define which servers you want to be managed

22.	If there are any sensitive pieces of information on your playbook like API Tokens, what's the strategy that you would follow to encrypt them ?
To run a playbook containing an encrypted string, use the ansible-playbook command, adding the --ask-vault-pass option. In this example, you can ignore the warnings about valid hosts, because you're just testing an example playbook: $ ansible-playbook --ask-vault-pass ssh-config.
23.	How can extract all the facts gather by ANSIBLE ?
This module is part of ansible-core and included in all Ansible installations. In most cases, you can use the short module name gather_facts even without specifying the collections keyword. However, we recommend you use the Fully Qualified Collection Name (FQCN) ansible.builtin.gather_facts for easy linking to the module documentation and to avoid conflicting with other collections that may have the same module name.
24.	How do you manage package installations using Ansible?
Package installation:
Ansible.buildin.package
25.	What is the difference between register and set_fact? When to use what ?
set_fact is a task on its own and register captures the result of a task,
26.	How do you loop over items in Ansible?
- name: Demo on using loops  
  hosts: all
  tasks:
      - name: Demo on Loops
        ansible.builtin.debug:
          msg: "value is: {{item}}" #item is the keyword to represent the element in the loop
        loop:
          - 10
          - 20
          - 30


27.	How do you handle errors in Ansible playbooks?  Use ignore_errors or failed_when to manage task failures.
# In ansible, if any of the task in the play fails, the subsequent tasks will fail
- name: Demo on errors
  hosts: all
  tasks:
    - name: Check if the tomcat is Running or nothing
      ansible.builtin.shell: ps -ef | grep java | grep -v grep
      register: OUT
      ignore_errors: true
 
    - name: Check if the tomcat is Running or nothing
      ansible.builtin.debug:
        msg: shipping is up

28.	What is an inventory file in ansible ?
The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. The file can be in one of many formats depending on your Ansible environment and plugins.
29.	When to use ansible vs ansible-playbook vs ansible-pull ?
Used to pull a remote copy of ansible on each managed node, each set to run via cron and update playbook source via a source repository.
Ansible Playbooks offer a repeatable, reusable, simple configuration management and multi-machine deployment system, one that is well suited to deploying complex applications. If you need to execute a task with Ansible more than once, write a playbook and put it under source control. Then you can use the playbook to push out new configuration or confirm the configuration of remote systems. The playbooks in the ansible-examples repository illustrate many useful techniques. You may want to look at these in another tab as you read the documentation.
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
The ansible-pull is a small script that will checkout a repo of configuration instructions from git, and then run ansible-playbook against that content.
Used to pull a remote copy of ansible on each managed node, each set to run via cron and update playbook source via a source repository. This inverts the default push architecture of ansible into a pull architecture, which has near-limitless scaling potential.
30.	If your playbook is on DRIVE, can you run it using ansible-pull ?
31.	What are the pre-requisites for ansible to operate using ansible-pull
Push mechanism-No concept of autoscaling use push mechanism in which you install ansible on a server and create and work on components on the same.
Install ansible on all the servers

32.	How does Ansible differ from other configuration management tools like Puppet or Chef?
o	Ansible is agentless, meaning it doesn’t require any agents on managed nodes.
o	It uses YAML-based playbooks for defining tasks.
o	Ansible is easy to learn and doesn’t require a master-agent architecture.
33.	What is the difference between list vs dictionary vs map object in YAML ?
Dictionary-a key value pair
List-a key with multiple values
Map-a key with multiple keys
34.	How can you leverage Ansible Vault to manage secrets for different environments (e.g., dev, test, prod)?
35.	Can we use ANSIBLE to create infrastructure on cloud ? If yes, why it's not at all preferred
environments
├── dev
│   └── group_vars
│       └── all
│           └── secrets
└── prod
    └── group_vars
        └── all
            └── secrets

36.	How can you achieve variable precedence and inheritance in Ansible?
Role Defaults-Lowest precedence
2. Inventory Variables
These variables are defined in the inventory file. They typically relate to specific hosts and are generally used for setting host-specific values.

 Inventory Group_vars and Host_vars
Group_vars and host_vars are specified for groups and hosts inside the inventory directory. The latter will take precedence if an inventory variable and a group variable conflict.

Use Vars to have the higher precedence.

. Set Facts
Variables set using the set_fact module have higher precedence.

37.	Discuss different ways to manage complex data structures (e.g., loops, conditionals) within Ansible playbooks. What are the conditions that you've used in ansible ?
38.	Explain me how would you handle this situation, If you have 300 vm's ( Combined ) of Redhat Linux,7,8,9 and ubuntu 18,20,22. Due to the security findings, you were asked to patch the package xyz on all the Redhat 9 machines of the inventory that you have, how can you do that ?
39.	Scenario : You have 500+ VM's and you would like to restart all the VM's whose uptime is greater than 100 days
40.	Scenario: You need to deploy a complex application with dependencies between different servers. How would you model this in your Ansible playbooks using handlers ?
41.	How would you configure secure access for your Ansible control node using SSH key management?
42.	Explain best practices for managing secrets and sensitive data within Ansible playbooks using Ansible Vault.

43.	How to run the playbook on debug mode
If you are running legacy playbooks or roles, you may see the debugger enabled as a strategy. You can do this at the play level, in ansible. cfg , or with the environment variable ANSIBLE_STRATEGY=debug .


