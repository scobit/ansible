
### Using register

Register is a nice way to automatically define variables based on command result.

Register can be used to store the result of a task in a variable.

It provides an awesome way to perform specific tasks only upon success or failure of another task.

vim register.yaml
---
- name: test register
  hosts: all
  tasks:
   - shell: cat /etc/passwd
     register: passwd_contents
   - debug:
       var: "passwd_contents"
   - debug:
       msg: {{ passwd_contents.rc }}	   
	   msg: {{ passwd_contents['rc'] }}	

Register defines it's variable on every single host, so basically it becomes a property of the host.

We can address all fileds of new variable like

passwd_contents.rc is going to print the return code

rc - result of the command
