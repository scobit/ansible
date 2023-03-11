ansible.cfg - a convenient configuration file that helps you to set some default values for your current product or for all of your system.


Specific ansible.cfg files can be created in project directories, if we create ansible.cfg in project directory - than file will always be used.

Managing assets with ansible, you will need to provide different settings.

Settings can be provided at different levels:

- on the command line
- in playbooks
- in ansible.cfg
 

A default ansible.cfg is provided in /etc/ansible/ansible.cfg

Settings that are made at a more specific level will always overwrite the generic settings.

Comand line settings always win ansible.cfg setting.

Generic rule in Ansible - the most specific settings will always win.
