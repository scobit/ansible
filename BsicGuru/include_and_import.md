Using includes and imports

First we need to understand why

If playbooks grow larger, it is common to use modularity by using includes and imports

Includes and imports can happen for roles, plays as well as tasks

An include is a dynamic process; Ansible processes the contents of the included files at the moment that this include is reached

An import is a static process; Ansible preprocesses the imported file contents before the actual play is started

The use case for an import is modularity in development

The use case for an include in conditionality. We only want to run it if a specific condition is true

vim includes.yml
---
- name: setup apache
  hosts: all
  tasks:
    - name: setup apache task
      import_tasks: install-and-setup.yml
      vars:
        package: httpd
        service: httpd


vim install-and-setup.yml
---
  - name: install the {{ package }} package
    yum:
      name: "{{ package }}"
      state: latest
  - name: start the {{ service }} service
    service:
      name: "{{ service }}"
      enabled: true
      state: started
	  
import - porpuse is in modularity

vim include.yml
---
 - name: setup a service
   hosts: ansible2
   tasks:
   - name: include the services task file
     include_tasks: tasks/service.yml
	 vars:
	   package: httpd
	   service: httpd
	 when: ansible_facts['os_family'] == 'RedHat'
   - name: include the firewall file
     import_tasks: tasks/firewall.yml
	 vars:
	   firewall_package: firewalld
	   firewall_service: firewalld
	   firewall_rules:
	    - http
		- https
