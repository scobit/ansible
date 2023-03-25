Understanding galaxy

galaxy.ansible.com is the community website that provides access to roles and collections

while using galaxy,  you can find the required commands to install roles and collections easily

ansible-galaxy in the command line interface that allows you to work with galaxy

use ansible-galaxy collection to manage collections

use ansible-galaxy role to manage roles

geerlingguy - one of the main pepople in the ansible community


ansible-galaxy role -h

ansible-galaxy role list

If we use ansible-galaxy role install, by default it ends in /home/ansible/.ansible/roles

cd /home/ansible/.ansible/roles

tree command

default and vars - these are about variables
Default is our default variables, they can be overwritten in vars in many cases operating system, specific variables.

tasks - every role has tasks, the main playbook in any directory has the name main.
main.yml - containing the main task
