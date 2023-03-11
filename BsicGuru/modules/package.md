ansible.builtin.package is a generic module for managing packages.

Works a little bit slow rather than specific package modules.

#### Install latest npam package
```
ansible all -m package -a "name=nmap state=latest"
```
