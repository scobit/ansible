
### Setting up Windows as a Managed Host

Ansible can manage all windows versions from Windows 7 and Windows server 2008.

#### Preparing a windows hosts
 - os
 - user with admin privileges
 
#### powershell configure winrm
```
winrm quickconfig

type yes
```


# WinRM setup part, using the script that is provided, copy script and run in PowerShell. This will set up basic authentification configuration
https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html

#### Verify using winrm enumerate that http and https listeners are available
```
winrm/config/Listener
```

Windows is using windows remote manager, as default remote management solution.


#### Create ansible user, set password and make this user an administrator

#### Server Manager -> Local Server -> IE Enhanced Security Configuration - make Off This conf makes IE unusable

Windows supports different authentification protocols

If authentification has been configured to a strict protocol on Windows, you may have to set up Ansible accordingly

 - basic: only supports local accounts - do not use
 - certificate: only supports local accounts - do not use
 - kerberos: only works for AD accounts
 - NTLM: works for local and AD accounts
 - CredSSP: offers best support for local and AD accounts
 
If you have isues with authentifications use ansible_winrm_transport: credssp (or anything elase) in ansible.cfg

### Preparing the Control Hosts

#### set up /etc/hosts for host name resolution to the windows box - notice that windows firewall disallows ping incoming
```
vim /etc/hosts

IP	FQDN	HOSTNAME
```


#### on ansible control
```
sudo pip3 install pywinrm
```

#### on ansible control
```
ansible win -i inventory -m win_ping
```

#### check facts
```
ansible win -i inventory -m setup
```
