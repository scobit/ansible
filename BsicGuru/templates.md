Using templates

A meplate is a sample configuration file that is combined with environment variables to produce sire specific configuration files

Templates are based on the Jinja2 templating language

In advance templates, conditional structures as well as loop structures can be used

By defaults templates should be in template directory

ansible likes dedicated directories


template - some layout which we should combine with specific values set in variables

vim templates/motd.j2

Welcome to {{ ansible_facts['fqdn'] }}
This system is running {{ ansible_facts['distribution'] }} version {{ ansible_facts['distribution_version'] }}

vim motd.yaml
---
- name: install motd
  hosts: all
  tasks:
  - name: install motd
    template:
      src: motd.j2
      dest: /etc/motd
      owner: root
      group: root
      mode: 0644
	  
When we are using ansible mode, there are two options.
Either we put our permission mode between single quotes and then we can use three digits for user group and other permissions.

Four digit mode in which we need a leading zero/
If we don't use single quotes, neither use 4 digits and use three digits, then the six is interpreted as special permissions and we have wrong permissions.

This applies to anywhere where we are going to use a mode parameter in ansible.

ansible all -a "cat /etc/motd"

Check in documentation information about conditional structures as well as loop structures.
Find frequently asked questions
and there how do i loop over a list of hosts inside a template

ansible hostname -m setup | less

vim templates/hosts.j2
{% for host in groups['all'] %}
  {{ hostvars[host]['ansible_default_ipv4']['address'] }}   {{ hostvars[host]['ansible_fqdn'] }} {{ hostvars[host]['ansible_hostname'] }} 
{% endfor %}



Notece that hostvars[host] is not something that needs to be interpreted as a real variable. It is a hostvars variable and that is why the host does not get single quotes. And the single quotes are aroung the fact.


vim hosts.yaml
---
- name: install hosts
  hosts: all
  tasks:
  - name: install hosts
    template:
      src: hosts.j2
      dest: /tmp/newhosts
      owner: root
      group: root
      mode: 0644
	  
ansible all -a "cat /tmp/newhosts"
