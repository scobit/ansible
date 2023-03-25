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
