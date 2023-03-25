
Understanding delegation

Delegation is used to run tasks on another host

Use  delegate_to as a task property to run that task on a different host

It allows you to run an individual task on a different host, withoyt any need to start a new play that addresses these hosts specifically


vim delegation.yaml
---
- name: install start and enable httpd
  gather_facts: false
  hosts: rocky
  tasks:
  - name: install package
    package:
      name: httpd
      state: latest
  - name: start and enable service
    service:
      name: httpd
      state: started
      enabled: yes
  - name: test httpd access
    uri:
      url: http://rocky
    delegate_to: localhost
	
Understanding delegation requirements

Delegetion host need to meet the same requirements as regular control hosts

 - pythin is installed
 - ssh access to managed hosts in enabled
 - host name to ip address resolviing works

The delegate_to host must exist in inventory

 
	
Using delegation to copy  files between hosts - 

vim copy-files.yaml
- name: copy files between hosts
  hosts: rocky
  tasks:
  - name: copy file from ubuntu to rocky
    synchronize:
#   copy:	
      src: /etc/hosts
      dest: /tmp/hosts
      mode: push  # comment if copy
    delegate_to: ubuntu
  - name: check file
    stat:
      path: /tmp/hosts
    register: stafile
  - debug:
      var: stafile

ansible-plaubook -vvv copy-files.yaml

synchronioze requires rsync to be present
