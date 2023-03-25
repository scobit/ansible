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
