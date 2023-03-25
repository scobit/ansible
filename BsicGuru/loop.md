### Understanding loop

Ansible offers different solutions to implement iterations

Items are easy to use
 - Define a list of items
 - In the task, use loop (previously: with_items) to process the items

 
Example:
- name: remove files
  file:
    name: "{{ item }}"
	state: absent
  loop:
   - file1
   - file2


Using variables to define a loop
 
Do not forget about separating site specific data from code

The list that loop is using can be defined by a variable

vars:
  my_services:
   - httpd
   - vsftpd
tasks:
 - name: start some services
   service:
     name: "{{ item }}"
	 state: started
   loop: "{{ my_services }}"
   
Using Arrays in Loops

Each item in a loop can be array with multiple keys in each array item

- name: create users using a loop\
  hosts: all
  tasks:
  - name: create users
    user:
	  name: "{{ item.name }}"
	  state: present
	  groups: "{{ item.groups }}"
  loop:
   - name: anna
     groups: wheel
   - name: linda
     groups: users
     
	 
### Understanding loops vs items

The loop keyword is the current keyword.

In previous versions of ansible, the with_* keyword was used for the same purpose.

The loop syntax is preferred, but with_X is still supported as well.

From ansible 2.5 developers introduced loop, and with_* was deprected. 

But it happens to be that some things are better done using with. 

And that is why the current statement has changed. Loop is preferred but in some cases it's okay to use with whatever.

with_items: equivalent to the loop keyword
with_file: the item contains a file, which contents is used to loop through
with_sequence: generates a list of values based on a numeric sequence

Most of the with_X statements can now be replaced with filters

vim loopservices.yml

- name: start some services
  hosts: ansible2.example.com
  vars:
    my_services:
      - crond
      - sshd
  tasks:
    - name: start some services
      service:
        name: "{{ item }}"
        state: started
      loop: "{{ my_services }}"
	  
vim loopusers.yml
---
- name: create users using a loop
  hosts: all
  tasks: 
  - name: create users 
    user: 
      name: "{{ item.name }}"
      state: present
      groups: "{{ item.groups }}"
    loop:
      - name: anna
        groups: wheel
      - name: linda
        groups: users
      - name: bob
        groups: users
		
vim install _and_run.yml
---
- name: install and run software
  hosts: ansible2
  tasks:
  - name: install packages
    package: 
	  name:
	   - vsftpd
	   - nmap
	   - httpd
	  state: latest
  - name: run them
    service:
      name: "{{ item }}"	
	  state: started
	loop:
	 - vsftpd
	 - httpd
	 
The point is, that for every single item that we are specifying, it is running the task again. 

If we have 100 items then we run your task a hungred times.

Limited number of modules is ansible allow us to specify lists in the name of the module, 

which means that you run the module once and while running it once it is installing all of them.

we can check it by:

ansible-doc yum

find - name and find type, it will be list

ansible-doc package

This doc don't provide info about list support.

If you have a module that supports a list in its name, always use it.

if we cannot find it in a documentation use common sense, common sense as in

