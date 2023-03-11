ansible.builtin.package is a generic module for managing packages.

Works a little bit slow rather than specific package modules.

Package module doesn't allow you to use some of the specifics, that may be available in specific modules like apt and dnf, but it will work against different Linux targets.

#### Install latest npam package
```
ansible all -m package -a "name=nmap state=latest"
```
