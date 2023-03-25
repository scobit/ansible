Understading collections

Collections since ansible 2.10 and in preview 2.9 are a new way to bundle ansible contanets to make it more manageable

Collections can have different sources, including ansible community and Red Hat partners

So by using ansible collections we get better quality control

A role is basically a collection of a couple of tasks that have configured to do something common

Collections may contain following items:
 - modules that provide the ansible core functionality
 - roles that can be included in playbooks for common task execution
 - plugins that extend the pyhthon code on the ansible control host
 




Understanding collection naming

Collections have a Fully Qualified Collection Name (FQCN), such as ansible.netcommon

Within this FQCN, plugins, modules etc are addressed with their own name, such as ansible.netcommon.cli_command

Before collections, you would address just a module name such as user, now we address this module as ansible.builtin.user

Start with a collections setting in the play header and list all collections you-re going to use


vim enforce-selinux.yml
---
- name: set SELinux to enforcing
  hosts: localhost
  gather_facts: no
  become: yes
  tasks:
  - name: set SELinux to enforcing
    ansible.posix.selinux:
      policy: targeted
      state: enforcing
	  

vim enforce-selinux-simplified.yml
---
- name: set SELinux to enforcing
  hosts: localhost
  gather_facts: no
  become: yes
  collections:
  - ansible.posix
  tasks:
  - name: set SELinux to enforcing
    selinux:
      policy: targeted
      state: enforcing



Using collections in playbooks

In the play header, the collections keyword can be used

It takes a list of collections as its argument

After using the collection keyword, the collection itself can be addressed the old way: selinux instead of ansible.posix.selinux

Using collections

In ansible 2.9, collections are not a default part of ansible

To use them, they need to be installed from ansible galaxy

To install multiple collections. consider using a requirements file


(ansible 2.9 and below)
ansible-galaxy collection install -r requirements.yml

In ansible .20 adn later, collections are installed by default


Getting collection information

Ansiblw 2.10 and later contain better collection integration
Collection names can easily be found using ansible-doc: ansible-doc -l | grep aws

Ansible 2.9 shows opnly collections that have been installed

Collections are not listed with ansible-doc -l
Collection documentation is available while addressing collection components directly
Finding collection names only goes through the installed files

Listing collections

Use ansible-galaxy collection list (2.10 and later) to show a complete list of collections

To verify the contents of the collection, access it's physical location and investigate the directory structure

Demo: using ansible collections

ansible-galaxy collection install ansible.posix

tree ~/.ansible/collections/ansible_collections/ansible/posix

ansible-doc ansible.posix.selinux;
ansible-doc ansible.posix.acl


download tarball with ansible.posix


 
ls -l ~/.ansible/collections/ansible_collections

cp tarball there 

tar -xvf 

mkdir -p ansible/posix

mv * ansible/posix

use tree to view (this is all about 2.9 and below)

Modules happen to be plugins as well

ansible-doc ansible.posix

