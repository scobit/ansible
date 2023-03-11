ansible.builtin.shell is like command but uses a bash shell which allows you to run shell features like pipes and redirects.



#### Set up password for the user in ubuntu
```
ansible -i inventory ManagedHostName -m shell -a "echo 'ansible:password' | chpasswd" -u UserName -b -k -K
```

#### Set up password for the user in RHEL
```
ansible -i inventory ManagedHostName -m shell -a "echo password | passwd --stdin ansible" -u root -k
```
