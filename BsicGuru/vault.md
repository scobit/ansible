Vault is the asnible way to store sensitive information.

Some modules require sensitive data to be processed - hhis may include webkeys, passwords, and more.

To process sensitive data in a secure way, ansible vault  can be used.

ansible vault is used to encrypt and decrypt files.

To manage this process, the ansible-vault command is used.

And one the file is encrypted with ansible vault, we need to tell ansible playbook to decrypt it.


#### Help 
ansible-vault --help

rekey - is what you can use to change the current password on a vault encrypted file

ansible-vault create secret.yml
username: bob
pwhash: password

vim create-user.yml
---
- name: create a user
  hosts: all
  vars_files:
   - secret.yml
  tasks:
   - name: creating user
     user:
	  name: "{{ username }}"
	  password: "{{ pshash }}"
	  

ansible-paybook --ask-vault-pass create-users.yml

ansible all -a "tail /etc/shadow"

echo password > vault-pass

ansible-playbook --vault-password-file=vault-pass create-users.yml
