ansible.builtin.command is a generic module that allows you to run any command using ansible.

#### Command examples via ad-hoc
```
ansible -i inventory all -m ansible.builtin.command -a reboot

ansible -i inventory all -m command -a reboot

ansible -i inventory all -m ansible.builin.command -a id

ansible -i inventory all -m ansible.builin.command -a uptime
```
