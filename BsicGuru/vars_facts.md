
### Using variables in a playbook

Variables can be defined in different ways.

You can set them in playbooks directly, but that is bad practice as the site-specific information in that case is not separated from the code.

While referring to a variable, you need to use double braces:

 - the variable is {{ myvariable }}

If a value starts with a variable, the variable needs to be between double quotes and double curly braces:

 - msg: "{{ myvariable }} is set"
 
If used in a when statement the curly braces are omitted.

Variables are integrated in play header.

Header is the highest level in the playbook where you can set your variables.

It also means that if you have a multiple play playbook you need a different approach to set your variables accordingly.

ansible_hostname is also noted as a variable - this is an ansible fact.

Facts - are discovered variables.

When value is starting with the variable or the entire value is just the variable. The entire value must be between double quotes.


### Another way of defining variable

This way we overwriting the variable on the command line.

That goes for any option that you use in ansible.

If we define the option on the command line, it has higher precedence and it'll always win.

The golden rule in ansible - the most specific settings will always win.

#### Exmaple var via command line
```
ansible-playbook playbook.yaml -e user=bob
```

### Defining Variables

There are different ways to define variables:
 - in the play header using vars:
 - in the play header using an include with vars_files:
 - Using the set_fact module in a play
 - On the command line, using the -e key=value option
 - as inventory variables
 - as host or host_group variables
 - Using vars_prompt: to request values from the user while running the playbook
 
Ansible facts are disccovered variables containing system properties.

Ansible Vault can be used to encrypt sensitive values like variables.

So with all these different ways of running variables we need  to have a clear idea of when to do what.

### Defining variables in play

To define variables in plays, include a vars: section in the header of the play where the variables are needed

Variables defined in plays are valid only within that specific play, and don't have a playbook scope.

Use vars_files: to specify a list of files that include variables you want to include.

To override variables specified in this way, use the -e key=value command line option to specify variables.

gather_facts: no


Variable are defined in a key : value style
Also we can use any style in which you use key=value

### Using set_fact

set_fact is a module that can be used anywhere in a play to set a variable.

Variables set this way apply as a fact to the hosts on which it is set.

Set_fact relates the variable to the host that you are using in a play. 

The benefit of set_fact is that it allows you to dynamically set variables that are based on the result of any task in the playbook.

Also, variables set with set_fact have a playbook scope.

Variables that are defined using set_fact apply to the host on which it is set. That means that they do have a playbook scope but if in the next play you are using the same variable on a different host, it is not going to work.


set_fact is actually is a module and we can check in doc it's usage.

Do mind the hosts on which you are running it.


### Using vars_prompt

Use vars_prompt in a play header to prompt users for a variable value that will have a scope within that play.



#### By default, the variable is flagged as private, which means the user doesn't see anything while entering the variable.
#### Use below construction to change this behavior of vars_prompt 
```
private: no 
```

You should be aware of the scope of variable. That is really something important to realize when working with variables.


### Using inventory and Host variables

Inventory and host variables are used to set a variable as a property for a specific host.

To use host variables, create a file in a directory host_vars that has the name of the host


At some point in time it was considered deprecated to set variables in inventory, but with the rise of Ansible Tower it has become common again. 

Because in ansible Tower, in some cases, it's just the best way to define your variables in inventory.

To define host group variables, create a file in a directory group_vars that has the name of a host groups.

In these files, define the host or host group variables.

So if you use group_vars/all in the file all, you define your variables and these variables are automacally picked up.

#### 
```
mkdir group_vars

vim group_vars/rocky

user: bob


vim create_user.yaml
---
- name: create users
  hosts: rocky
  tasks:
  - debug:
      msg: creating user {{ user }}
```
	  

### Understanding ansible facts

ansible facts are system variables that contain information about the managed system.

Each playbook starts with an implicit task to gather facts. Unless you specifically disable that.

To use fact gathering from an ad-hoc command, use the setup module.

Additional modules for fact gathering are available, like package_facts. 

These additional modules allow you to gather facts for no matter which assets you are managing with ansible.

To disable fact gathering, use this construction in in the play header
```
gather_facts: no 
```


ansible hostname -m setup > facts.txt

Top level variable, one big variable - ansible_facts.

Within that variable we have the second tier variables and these variables have their own value

Facts are categorized in different variables.


Nice thing about all this facts is that these facts are variables. And we can use these facts in conditionals


### Difference between setup module and gathering facts:

setup module - all tier two variables have ansible underscore in front of them.

In fact setup module is still compatible with the old approach, where the fact gathering is showing you the new approach.

Difference between old and new approach - before ansible 2.5 facts were not stored in one big variable ansbile_facts. 

They were hust stored in the variable ansible_all_ipv4_addresses.

Then in ansible 2.5 they decided to put everyting in one variable ansible_facts and to drop the name, the prefix ansible_

The setup module is still printing this prefix and if we use fact gathering we don't see that prefix.

And that prefix actually makes sense in the different ways, how you can refer to your variables and facts.


### Understanding facts and variables notations

Facts are stored in a multi-valued variable with the name ansible_facts, which is organized as a dictionary.

To address specific values in this dictionary, two formats can be used:

ansible_facts['default_ipv4']['address']
ansible_facts.default_ipv4.address

Of these two, the first one is preferred.

On old versions of ansible, facts were stored as individual variables (injected variables): 

ansible_default_ipv4 instead of ansible_facts['default_ipv4']

Some modules only support the old way of referring to facts.

		 	 
greater that sign - allows to type a message that's longer than one line 

That's the injected variable cell, ansible 2.5 and before this was common


### Troubleshooting slow fact collection

In some conditions fact collection may be very slow.

If fact collection is slow because you're working against lots of hosts, you can use a fact cache.

If you're experiencing slow fact gathering, ensure that host name resolution is set up on all hosts.

Ensure that each managed hosts has an /etc/hosts file that allows for name resolution between all managed hosts.


#### time command - allows you to time how long specific action is taking 
```
time ansible-playbook.yaml new_facts.yaml

where:
real - real (all) time
user - user time 
sys - system time (kernel time)
```


Important thing is that a remote host need to be able to resolve the name with the IP address of the control machine.

ansible all -m copy -a "src=/etc/hosts dest=/etc/hosts"


### Using Multi-valued Variables

The are 2 ways to store variables with multiple values, the dictionary versus array.

Dictionaries and arrays in ansible are based on python dictionaries and arrays.

An array (aka list) is an ordered list of values, where each value can be addressed individually

List = ["one","two","three"]

print(List[0])

A dictionary (hash) is an unordered collection of values, which is stored as a key-value pair
 
Dict = {1:'one',2:'two',3:'three'}
 
print(Dict)
 
Notice that a dictionary can be presented in a list.


Array is the same as a list.
Dictionary is the same as a hash.


### Understanding Array and Dictionary

Multi-valued variables can be used in playbooks.

When using a multi-valued variable, it can be written as an array (list), or as a dictionaty (hash).

Each of these has their own specific use cases

 - Dictionares are used in ansible facts
 - arrays are common for multi-valued variables and easily support loops
 - loops are not  supported on dictionaries directly, only indirectly by using the dic2items filter or by using with_dict (deprecated)
 
See ansible doc "Iterating over a dictionary"


### Understanding dictionary (hash)

#### Dictionaries can be written in two ways:
```
users:
  linda:
    username: linda
    shell: /bin/bash
  lisa:
    username: lisa
    shell: /bin/sh

Or as:

users:
 linda:{ username: 'linda', shell: '/bin/bash' }
 lisa: { username: 'lisa', shell: '/bin/bash' }
 ```


### Using dictionary

To address items in a dictionary, you can use two notations:
 
variable_name['key'], as in users['linda']['shell']

variable_name.key, as in users.linda.shell
 
Dictionaries are used in facts, use arrays in conditionals.

Notice that dictionaries can be a part of a list, which is used in ansible facts.

Using Array (list).

Arrays provide a list of items, where each item can be addressed separately.
```
users:
 - username: linda
   shell: /bin/bash
 - username: lisa
   shell: /bin/sh
```

You can easily recognize the array because in an array every item starts with a hyphen.

Every item starts with a hyphen and the array has different properties for these users.

Individual items in the array can be adressed, using the {{ var_name[0] }} notation.

where 0 is the array offset and print the first value in the array

Use arrays for looping, not dictionaries.

To access all variables, you can use with_items or loop


### Recognizing lists and dictionaries

In output like facts, a list is always written between []

Dictionaries are written between { }

And if the output is just showing " ", it is a string

Check ansible_mounts in the facts, which actually presents a list of dictionaries.

try to gather facts and check where arrays and where dictionaries and where strings.

ansible_facts itself is a dictionary, a dictionary containing dictionaries which may contain arrays.

ansible_mounts starting with a square bracket which means that he content really is an array and in the array we have dictionaries so it's an array of dictionaries and that is how we can mix the two of them together

vim vars/users-dictionary
users:
 linda:
  username: linda
  homedir: /home/linda
  shell: /bin/bash
  
 lisa:
  username: lisa
  homedir: /home/lisa
  shell: /bin/bash
  
We can see the dictionary - we recognized it because there are no hyphens

If we define an array, every item in the array starts with a hyphen

vim vars/users-list
users:
 - username: linda
   homedir: /home/linda
   shell: /bin/bash
 - username: anna
   homedir: /home/anna
   shell: /bin/bash
   
vim multi-list.yaml
---
- name: show lists also known as arrays
  hosts: ansible1.example.com
  vars_files:
   - vars/users-list
  tasks:
   - name: print array values
     debug:
	   msg: "User {{ item.username }} has home dir {{ item.homedir}} and shell {{item.shell }}
	 loop: "{{ users }}"
   - name: print the second array value
     debug:
	   msg: the second item is {{ users[1] }}


We are including the variable file and in this variable file we are addresiing the items in the array by looping over users

Loop is something that we can use on an array and loop will work on every single item that is encountered on the array 

Item is the thing that staring with a hyphen

One line for every single item in the array

vim multi-dcitionary.yml
---
- name: show dictionary also known as hash
  hosts: ansible1.example.com
  var_files:
   - vars/users-dictionary
  tasks:
   - name: print dictionary values
     debug:
	   msg: "User {{ users.linda.username }} has home dir {{ users.linda.homedir }} and shell {{ users.linda.shell }}"
	   
There is no way to easily loop over the values in a dictionary

You need to be aware of, use the right approach when encountering the reght variable type

Most inportant the ansible facts by nature is a huge collection of dictionaries and arrays is what you can use to loop over


Dictionaries are honestly not as spectacular but in a dictionary we can address the dictionary value specifically.








