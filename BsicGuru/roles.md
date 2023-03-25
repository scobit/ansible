Understanding roles

Roles are community-provided resources that make working with ansible easier

They provide standardized solutions for common tasks

Instead of finding out yourself, we can use community-provided roles in out playbooks

Just add the site-specific configuration using variables in the playbook, and run everything else from the roles

Custom roles can also be provided

Understanding task execution order

Tasks in roles are normally executed before other tasks in the playbook

Use pre_tasks to execute tasks before the role

Handlers that are triggered by pre_tasks are also executed before the roles


vim nginx-role.yml
---
- name: use galaxy nginx role
  hosts: ansible2.example.com
  user: ansible
  become: true

  roles:
    - role: geerlingguy.nginx
	
ansible-galaxy --help

ansible-galaxy role install geerlingguy.nginx

ansible playbook nginx-role.yml



Creating your own roles

ansible-galaxy role init RoleName

everything that's not required in role - should be deleted

The roles are supposed to be in the roles directory

vim pretasks.yml
---
- name: run a task before a role
  hosts: all
  pre_tasks:
    - name: update yum cache
      yum:
        update_cache: yes
  roles:
    - motd



### Nginx role - install nginx, make sure that before using the role, th following is done:
if installed and running, apache is removed
Perform an update of all packages

After running the role, remove the yum package cache

Solution:

Always go for the most specific solution

ansible-galaxy role list

sudo -i

Quoting issue 
Often in ansible can be a quoting issue

ansible-playbook --help

ansible-playbook lab8.yaml --list-tasks

ansible-playbook lab8.yaml --start-at-task file


vim lab8.yml
---
- name: install nginx
  hosts: all
  pre_tasks:
   - block:
     - name: remove apache
	   service:
	     name: httpd
		 state: stopped
		 enabled: no
     - yum:
	     name: httpd:
		 state: absent
	 - name: perform package update
	   yum:
	     name: '*'
		 state: latest
     when: ansible_facts['os_family'] == 'RedHat' or ansible_facts['os_family'] == 'Rocky'
	 
  roles:
   - geerlingguy.nginx
   
  post_tasks:
   - file:
       path: /var/cache/dnf
	   state: absent

