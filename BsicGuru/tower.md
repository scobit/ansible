Ansible Tower - the web-based Ansible management platform.

Ansible Tower  web-based platform that manages Ansible from a web interface.

Ansible Tower is installed by big install.yml playbook

The purpose is to make it easier to work with ansible and to add some features as well.

Ansible Tower adds some features that are not integrated in ansible engine, the command line version of ansible:

 - Role-Based Access Control for instance. You can use users as well as teams and to provide permissions to the users and teams to perform specific tasks
 - Caching of passwords
 - workflow designer - which is the option to create a conditional workflow between different tasks. That is like execute task A, if task succeed, you execute task B, if it doesn't, you execute task C

AWX is the open-source upstream of Ansible Tower.

AWX is container based and you need a container orchestration platform.

Ansible Automation Platform is a Red Hat solution that bundles different ansbile components:
 - ansible engine: the command line version of ansible
 - ansible tower: the web-based ansible management interface
 - content collections: packages that bundle modules, roles and more
 - automation hub: a platform that hosts certified collections for ansible
 - automation analytics: integrated Red Hat insights for advanced ansible management and analysis


galaxy.ansible.com - the community website, but content is not certified.

console.redhat.com
Home website for Ansible Automation Platform
requiere Red Hat credentials
you can get it from
developer.redhat.com

### Tower installation requirements

Free evaluation licenses are available from Red Hat
 - Download the tar-ball
 - Request an evaluation license or use your Red Hat developer subscription (developers.redhat.com)
 
The ansible Tower (AWX) machine
 - 8 (4) GB RAM
 - 4 (2) vCPUs
 - 40 (20) GB  disk space
 - Red Hat Enterprise Linux or CentOS Stream


### Installing Ansible Tower

#### download package
```
wget https://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-latest.tar.gz
```
#### Extract the evaluation version tar-ball
```
tar xvf ansible-tower-setup-latest.tar.gz
```


#### Modify all password fields in the enventory file (4 fields) (type password word)
```
vim inventory

admin_password='password'
pg_password='password'
automationhub_admin_password='password'
automationhub_pg_password='password'
```


#### Installation
```
sudo ./setup.sh
```


#### Log in to the web UI: https://localhost
```
Login: admin
Password: from inventory file
```

#### Log in with your Red Hat credentials or provide the credentials file

### Managing Machines with Tower

To reach out to managed machines with Tower, things are not really different from managing machines with Ansible Engine from the command line.

Identifying the managed machines:
 - On the Tower host, setup /etc/hosts name resolving (or DNS)
 
On the managed machines:
- Ensure sshd is running and accepts incomming connections (firewall)
- Need a user account with sudo privileges
- Need to set up password / SSH keys


### Understanding Tower Main Components

Organization: a collection of managed devices

Users: administrative users that can be granted access to specific tasks

Inventories: managed servers. Can be created statically or dynamically 
 - click Setting -> License and check Host Remaining
 
Credentials: credentials that are used to log in to a managed machine. Think of user with sudo privileges.

Project: a collection of playbooks obtained from a certain location (such as Github).

Template: the job definition with all of its parameters. Must be launched or scheduled.


### Understanding the Tower Workflow

(optional) Define an organization

create an inventory

configure credentials

set up a project

define a job template

run the job



#### configure /etc/hosts on tower server
```
ansible tower -m copy -a "src=/etc/hosts dest=/etc/hosts"

ansible tower -a "cat /etc/hosts"
```

#### create ssh key
```
su - ansible

ssh-keygen

ssh-copy-id HostName1 etc
```


### Managing Windows with Ansible Tower

There are no credentials through that allow you to specify that winrm needs to be used as the remote connection mechanism.

To manage Windows, you create a Windows specific inventory, with variables set on that inventory:
```
ansible_connection: winrm
ansible_port:5986
ansible_winrm_server_cert_validation: ignore
optional:
ansible_winrm_transport: credssp
```

### Understanding Windows Become

To manage Linux, you need privilege escalation.

To manage windows, you need to have a user with administrator privileges.

If tasks are executed using such a user account, privilege escalation does not have to be disabled.


### Managing Windows with Ansible Tower

Make sure that windows is in manageable state.

Create machine credentials to log in as the Windows based ansible user, using a password (not an ssh key).

Next you will be able to run playbooks against Windows.

groups in inventory 

privikege escalation





