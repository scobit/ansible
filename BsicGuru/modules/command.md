ansible.builtin.command is a generic module that allows you to run any command using ansible.

ansible.builtin.command is the default module and runs arbitrary commands on the managed nodes, but not through a shell.

The default module means that you don't have to specify -m command and just use -a followed by the command, it'll also work.



#### Command examples via ad-hoc
```
ansible -i inventory all -m ansible.builtin.command -a reboot

ansible -i inventory all -m command -a reboot

ansible -i inventory all -m ansible.builin.command -a id

ansible -i inventory all -m ansible.builin.command -a uptime
```
