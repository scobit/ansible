inventory groups

-b become - run this command with escalated privileges

-k going to prompt for an ssh password

-K is going to prompt for a sudo password as well


#### Отображение версии и конфигурации ansible
```
ansible --version
```

ansible --version
can show which ansible.cfg file we use

dnf install -y git

git clone https://github.com/sandervanvugt/ansiblecvc



at this part ansible don't know where to find inventory???

if we press enter on become password - will be used ssh password

#######

on ansible control, create windows project directory with an ansible.cfg and an inventory 
https://github.com/sandervanvugt/ansiblecvc
cd windows


cp ansiblecvc/ansible.cfg .


add host to inventory

ansible tower -m user -a "name=ansible" -u root -k


ansible tower -m shell -a "echo password | passwd --stdin ansible" -u root -k"

ssh-copy-id tower

ansbile tower -u root -k -m copy -a "src=/tmp/sudoers dest=/etc/sudoers.d/ansible"

ansible tower -m command -a "ls -l /root"



Demo: performing Tasks Using Modules

ansible ansible1 -m command -a "rpm -qa | grep python"

ansible ansible1 -m shell -a "rpm -qa | grep python"

ansible all -m copy -a 'content="hello world" dest=/etc/motd'

ansible all -a "cat /etc/motd"

ansible all -m service -a "name=httpd state=started enabled=yes"



Use add-hoc commands to request ansible facts for your Windows Server

Use ad-hoc commands to create a user anna on your Windows Server

ansible-doc -l | grep ^win

ansible-doc -l | grep ^win | grep facts

ansible windows -m win_disk_facts

ansible windows -m win_product_facts


ansible-doc win_user

ansible win -m win_user -a "name=anna password=Aa123456$$"


ok - means nothing was changed

apache2 in ubuntu, httpd in RHEL.



boolean in ansible can be:
false no F f 
 
firewall-cmd --get-services



#########


create an anventory file that defines a host group webservers ans well as a group dbservers

create the file group_vars/webservers with the following contants
web_package: httpd
web_service: httpd

create a simple playbook that uses the debug module to show the current value of the variables web_package and web_service

vim inventory

[dbservers]
hostname

[webservers]
hostname

mkdir group_vars

vim group_vars/webservers
web_package: httpd
web_service: httpd


It is important  that the name of var file matches tha name that you are using in inventory

vim lab5.yaml
---
- name: lab5
  hosts: webservers
  tasks:
   - debug:
       msg: using {{ web_package }} and {{ web_service }}

	   
