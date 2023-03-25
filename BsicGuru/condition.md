### Conditionals overview

Handlers are tasks that only run if triggered by a task that has changed something.

Items can be used to iterate over a list.

When is used for conditional task execution.

Blocks can be used to implement if-then-else like statements

Register is useful aid to record the result of a task in a variable.

Fail is a module that is used to perform a specific task on failure.



### Understanding handlers

A handler is a task that is only executed when triggered by a task that has changed something.

Handlers are executed after all tasks in a play.

If any task fails after the task that has called the handler, handlers are not executed.

To change that behavior
```
force_handlers
```

Default behavior is ansible happens to be if execution of a specific task, on a specific host is failing then further play execution on that host is aborted.

Handlers  are going to be operational if something has changed. So if tasks did't change anything, handler will not work.

Force handlers allows you to force the handlers to be started even if any subsequent tasks are failing.

### Using conditions 

When statements are used to run a task conditionally.

Playbook variables, registered variables, and facts can be used in conditions and make sure that tasks only run if specific conditions are true.

For instance, check if a task has run successfully, a certain amount of memory is available, a file exists, etc

Using facts is very common in when statements.

Examples:

ansible_machine == "x86_64"

ansible_distribution_major_version == "8"

ansible_memfree_mb == 1024

The fact that the value is between double quotes makes it ansible will interpret it as a  string.

If we don't see double quotes, so this is interpreted as an integer.

With integers we can use grater than and smaller than:


ansible_memfree_mb < 256

ansible_memfree_mb > 256

ansible_memfree_mb <= 256

ansible_memfree_mb >= 256

ansible_memfree_mb != 256

my_variable is defined

my_variables is not defined

my_variable - is checking for a boolean value

ansible_distribution in supported_distros

ansible distribution os the injected fact style fact and supported distros is another variable.

vim distro.yml
---
- name: when demo
  hosts: all
  vars:
    supported_distros:
      - Rocky
      - CentOS
      - Fedora
    rhpackage: httpd
    ubupackage: apache2
  tasks:
    - name: install RH family specific packages
      yum:
        name: "{{ rhpackage }}"
        state: present
      when: ansible_distribution in supported_distros 
    - name: install Ubuntu package
      apt:
        name: "{{ ubupackage }}"
        state: present
      when: ansible_distribution == "Ubuntu"
	  


vim quicktest.yml

---
- name: test command result
  hosts: all
  tasks:
    - name: check a fact
      debug: 
        msg: echo today is 25
      when: '"25" in ansible_date_time.day'
     

vim handlers.yml
---
- name: set up web server
  hosts: rocky
  force_nadlers: yes | true
  tasks:
   - name: install httpd
     yum:
	  name: httpd
	  state: latest
   - name: copy index.html
     copy:
	  src: /tmp/index.html
	  dest: /var/www/html/index.html
   - name: copy nothing - intended to fail
     copy:
	  src: /tmp/nothing
	  /var/www/html/nothing.html
  handlers:
    - name: restart_web
	  service:
	   name: httpd
	   state: restarted
     
     

### Understanding register

Register is particularly useful in combination with when statements.

Register is used to store the output of a command, and adddress it as a variable.

And next we can use the result of the command in a conditional or in a loop.

vim command-output-test.yml

---
- name: test command output
  hosts: ansible1.example.com
  tasks:
    - name: test for VG existence
      command: vgs centos
      register: vgout
      ignore_errors: True
    - name: show variable value
      debug:
        var: vgout
    - name: print message
      debug:
        msg: vg does not exist
      when: "'not found' in vgout.stderr"
      
      
      
	  
Ignore_errors can be set in a play header, can also be set in a task header. In this example it has a task scope. So only for this task, if it has an error, we continue. If all the tasks have an error, then the playbook execution will stop and produce a normal error behavior.

In a when statement, we do not have to use the curly braces to refer to the variables.

ansible hostname -a "vgs"

vim register_command.yml
---
- name: test register
  hosts: all
  vars_prompt:
    - name: username
      prompt: which user are you looking for
	  private: no
  tasks:
    - shell: cat /etc/passwd
      register: passwd_contents
    - debug:
        var: passwd_contents
    - debug: 
        msg: echo "passwd contains user {{ username }}"
      when: passwd_contents.stdout.find(username) != -1


private: no 
By default vars_prompt will not show the results of what the user is typing

find - this is python command that is used on the standard out on the register variable

() - python way to refer to a variable

-1 if this python find function will find the username it will give a byte offset like 386 if the user was find starting at byte 386. And if user was not found, then it will give a minus one. 


### Understanding ansible blocks

A block is a logical group of tasks.

Blocks are used to control how tasks are executed.

One block can, for instance, be enabled using a single when.

Blocks can also be used in error condition handling

 use block to define the main tasks to run
 use rescue to define tasks that run if tasks defined in the block fail
 use always to define tasks that will run, regardless the success or failure of the block and resccue tasks
 
blocks can be used lije if then else statement

Items can not be used on blocks

vim blocks.yml
- name: simple block example
  hosts: all
  tasks:
    - name: setting up http
      block:
      - name: installing http
        yum:
          name: httpd
          state: present
      - name: restart httpd
        service:
          name: httpd
          state: started
      when: ansible_distribution == "Rocky"


It is good practice when working with ansible, test conditions in different way (talking about variables, conditions etc)


vim blocks2.yml
---
- name: using blocks
  hosts: all
  tasks:
    - name: intended to be successful
      block:
        - name: remove a file
          command:
            cmd: /usr/bin/rm /var/www/html/index.html
		  or
          file:
            path: /var/www/html/index.html
            state: absent			
      rescue:
        - name: create a file
          command:
            cmd: /usr/bin/touch /tmp/rescuefile
		  or
		  file:
		    path: /tmp/rescuefile
			state: touch
      always:
        - name: always write a message to logs
          command:
            cmd: /usr/bin/logger hello
			
Rescue will earn tasks if tasks that are defined in the block are failing



Using when to make the command module idempotent

Idempotency ensures that running a playbook always gives the same result, no matter the current state of the managed asset. Command, shell and raw module are not idempotent by nature. However if you're using register in a smart way, you can use even these modules in an idempotent way.

Create a playbook that creates a user using the command module in such a way that the playbook is idempotent.


If number is not between double quotes means that this is integer

- name: creates user {{ user }}
  vars:
    user: linda
  hosts: ansible2
  tasks:
  - name: checkuser
    command: id {{ user }}
  register: iduser
  - name: 
    debug:
    var: iduser
  - name: beforecheck
    fail:
    msg: user already exists
  when: iduser.rc == 0
  - name: create user {{ user }}
    command: userdd {{ user }}
  
- name: continuing
  hosts: ansible2
  tasks:
   - debug:
       msg: we made it =)
