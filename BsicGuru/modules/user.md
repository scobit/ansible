ansible.builtin.user is a specific module for managing user accounts.

#### Create ansible user on ubuntu with ad-hoc
#### By default in ubuntu user created without home directory
```
ansible -i inventory ManagedHostName -m user -a "name=ansible create_home=yes" -u UserName -b -k -K
```

#### Create ansible user on RHEL with ad-hoc
#### By default in RHEL user created with home directory
#### as we use root user we don't need become privileges, so we don't nedd -b and -K
```
ansible -i inventory ManagedHostName -m user -a "name=ansible" -u root -k

ansible -i inventory ManagedHostName -m ansible.builtin.user -a "name=anna state=present"

ansible ManagedHostName -m user -a "name=anna state=present"

```

