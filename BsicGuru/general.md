Ansible is a configuration management tool.

Автоматизирует управление конфигурирацией в инфраструктуре.

Ansible не профилируется на разворачивании инфраструктуры, это лучше делает Terraform.

Ansible нужен для того, чтобы быть уверенным что текущее состояние управляемого объекта соотвествует желаемому состоянию определённому в Ansible.

Управление состоянием (конфигурацией) - это Ansible

Альтернатива:
 - Puppet
 - Chef
 - SaltStack


Ansible управляет конфигурацией как кодом.

В качеcтве гаранта верности кода, плейбуки должны редатироваться через git.

Ansible control node - хост где установлен ansible. Сontrol node - это не сервер, а именно рабочая станция оператора.

Control node требуется python.

Managed nodes упраляется control node.

Понятие оператор ansible - тот, кто работает на control node.

С Control node конфигурация распростроняется на управляемые объекты.

Способы установки:
 - git репозиторий - latest stable
 - python pip3 установка - latest and greatest
 
latest and greatest isn't always stable
 
Рекомендация - установка из репозитория ОС.
 
Ansible 2.10 - возможность работы с коллекциями стала доступна по умолчанию. 

модули и коллекции - это разные вещи.



### Managed node requirements

remote access - ssh default (etc)

dedicated user account

privilege escalation

For convenient  
use ssh keys fow passwordless connection to remote hosts   
use passwordless privilege escalation   

for secure management   
use -k cmd option to prompt for ssh password  
use -K cmd option to prompt for privilege escalation password   

Ansible tower - allows to cache passwords

Inventory

DNS and /etc/hosts are generic solutions that provide hostname to IP address resolving

Inventory needs
to identify managed hosts
to define hosts groups to be used by ansible
to define variables (considered deprecated)

default inventory is in /etc/ansible/hosts

project based inventory is common, and inv files can be referred to using the -i inventoryFileName cmd line option

#### Ad-hoc

Dependencies cannot manage very well using ad-hoc.

Ansible modules can be used in a ad-hoc commands.

Modules - perform specific tasks, modules - key feature of ansible.

Idempotentcy - свойство, при повторном выполнении таска результат выполнения будет одним и тем же и не важно менялось ли среда исполнения или нет.

Ansible ad-hoc command is a command-line-only solution to perform ansible tasks on managed hosts.

Always recomended to use the most pecific module you can find, as generic modules are not idempotent.

While we are wotk with modules we need to address modules by name.

Since ansible 2.10 modules are part of collections.

Есть значительная разница между ansible 2.9 and 2.10.

To refer to module name in 2.10 and later a fq collection name is used like ansible.builtin.command

In ansible 2.9 and earlier only the last part of this name is used.



### Setting up linux managed hosts with ad hoc commands

Hosts requirements:
 - ssh is running and reachable
 - hostname are resolved via dns or etc/hosts
 - create a file with the name inventory
 - create the ansible user on manged nodes with administrative privileges
 - include host names in inventory 
 - set a password for this user
 - configure ssh key-based login
 
 


#### Openssh install on ubuntu
```
apt install openssh-server

systemctl status sshd
```

Changed true - means that it changed something.


By default, every ad-hoc command starts with ansible command.





#### Using the local user ansible, create an ssh key pair and press enter to all questions.
```
ssh-keygen
```

#### Use ssh-copy-id to copy the key pairs to the managed nodes
```
ssh-copy-id HostName; 
```

#### Testing
```
ansible -i inventory all -m command -a "id" -u ansible
```


#### Ansible Failed. "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this. Please add this hosts's fingerprint to your known hosts file to mange this host"

If ansible receives a host key fingerprint from a managed host that it doesn't know yet, ansible is going to refuse connection.

#### We can change this through the ansible.cfg or workaround, just 

```
ssh HostName
```

Fingerprint - is telling us hey i can't verify the authenticity.The thing is that i don't nedd to login, we need these host keys to be stored.

#### They are stored in 
```
.ssh/known_hosts
```

#### Edit /etc/hosts for hostname resolving, then ping ansible host to check
```
vim /etc/hosts

ip	fqdn	hostname
```



### Setting up privilege escalation

To securely escalate privileges, use the -b -K command line options.

To conveniently escalate privileges, consider configuring passwordless sudo.

To do so with ansible, , next use the ansible copy module to 

#### Create a sudo configuration file locally
```
echo 'ansible ALL=(ALL) NOPASSWD: ALL' > /tmp/sudoers
```

#### copy configuration file to hosts
```
cp /tmp/sudoers /etc/sudoers.d/ansible

ansible ManagedHostName -i inventory -u root -k -m copy -a "src=/etc/sudoers dest=/etc/sudoers.d/ansible"

ansible ManagedHostName -i inventory -u UserName -k -b -K -m copy -a "src=/tmp/sudoers dest=/etc/sudoers.d/ansible"

ansible ManagedHostName -i inventory -u UserName -k -b -K -m file -a "path=/etc/sudoers.d state=directory"
```

#### test
```
ansible -i inventory all -m command -a "ls -l /root"

ansible -i inventory all -m command -a "ls -l /root" -b
```

#### Ansible Failed. Permission deniednon-zero return code

Means that we make some sudo command wtihout specifing -b even if we have passwordless sudo rights

we can automate input options like -b - K etc




















