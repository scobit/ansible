Security in ansible is all about securing connections

Commonly used bur insecure
 -  passphrase-less SSH keys to connect to remote hosts
 -  become = true as a standard setting
 - passwordless privilege escalation
 
More secure but not convenient
 - b -K when needed
 -k instead of SSH keys
 
Making Secure Connections

Do not configure passwordless solutions as a standard!

Securing SSH connections
 - use ssh-keygen to generate as ssh key that is protected with a password
 - use eval ssh-agent $SHELL to start the SSH agent after login
 - use ssh-add ~/.ssh/id_rsa to add your identity to the agent
 - verify using ssh-add -l
 
Consider automating by putting all in ~/bash_profile

You will be prompted for the key password at first use

Next, the password is cached for the remaining duration of your session

if [ -z "$SSH_AUTH_SOCK"]; then
	eval `ssh-agent -s`
	ssh-add
fi


Securing sudo access

Do not use the NOPASSWD option in /etc/sudoers

Instead, increase the timestamp option in /etc/sudoers to decrease the frequence that you will have to enter a password

 - Defaults timestamp_type=global,timestamp_timeout=60
 - timestamp_global keeps an authentification token for passwordless privilege escalation, no matter which terminal a user is coming from
 - timestamp_timeout=60 keeps the token for 60 minutes
 
These options are needed on ALL managed servers
 - consider using line_in_file to get them there
 
On first contact, use -K to prompt for privilege escalation password

In all subsequent commands, omit the -K option until you see an error

After token expiration, use -K again to refresh the token

Demo: securing ansible management

On control, use ssh-keygen and set a password for the ansible user
eval ssh-agent /bin/bash

ssh-add ~/.ssh/id_rsa

Use ssh-copy-id to copy the new public key to managed hosts

Create a playbook that uses lineinfile to replace the default section in /etc/sudoers on all managed hosts. Hints:
regexp: '^Defaults'
line: 'Defaults timestamp_type=global,timestamp_timeout=60'
validate: /usr/bin/visudo -cf %s

Run the playbook with the -k -K options

Use ansible all -a 'ls /root' -b -K and enter the sudo password when prompted

Run again: ansible all -a 'ls /root' -b without any futher options

vim makemesecure.yml
---
- name: make me secure
  hosts: all
  tasks: 
   - lineinfile:
       name: /etc/sudoers
	   regexp: '^Defaults'
	   line: 'Defaults timestamp_type=global,timestamp_timeout=60'
	   validate: /usr/bin/visudo -cf %s
	   
	   



ansible-playbook makemesecure.yml -K -b

ansible all -a 'ls /root' -b -K

ansible all -a 'ls /root' -b

Nice mix between convenient and secure
