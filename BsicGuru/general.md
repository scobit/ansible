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



### Ad hoc commands versus playbooks

When to use Ad-hoc commands:

In an ad-hoc command, ansible modules are used to perform tasks on managed hosts

ad-hoc command are perfect for running one task on a manageed hosts 
 
 - initial hosts setup
 - checking configuration on hosts
 
Playbooks are the way to implement more complex taks where dependency relations have to be managed.


### Understanding Ansible Modules

An ansible module is a python script that will be executed on the managed host.

Modules can be offered as such (pre Ansible 2.10) or as a part of a collection (Ansible 2.10 and later).


### Running ad-hoc commands

Use the ansible command to run ad-hoc commands

While running the ansible command, an inventory must be present, and you must specify which host to address.

We will also use the -m option to address a specific module, and -a for module arguments.


FQCN - fully qualified collection names


### Understanding Module Names

Before ansible 2.10, modules were referred to just by their name.

Since ansible 2.10, modules are a part of ansible collections.

Collections allow for better management of large amounts of modules.

For compatibility reasons, the old module names can still be used.


### Best Practice

Always use the most specific module you can find.

Using generic modules often leads to problems as these don't do well in situations where the desired state already exists.

This is referred to as idempotency: meaning that if the current state already matches the desire state, the module will still work as expected and won't fail.


### Understanding Idempotency

Idempotence is a property of operations in mathematics and computer science whereby they can be applied multiple times without changing the result.

Ansbile is about state management and desired result  should be.

In ansible it means that a module should run successfully, where it makes no difference if the current state already meets the desired state or not.

Many ansible modules are idempotent by nature, but some are not.

Avoid using command, shell or raw as they are not idempotent by nature.


#### Demo: Expoloring Idempotency
```
ansible all -m ping

ansible all -m user -a "name=lisa"

ansible all -m command -a "useradd lisa"

ansible all -m user -a "name=lisa state=absent"
```



### Understanding playbooks

Playbooks are used to run multiple tasks in order on multiple hosts.

Playbooks are the way to go to define dependency relations between tasks.

Playbooks can also be used to implement conditional structures, where tasks are only executed if specific conditions are true.

### Understanding playbook Elements

In a playbook, one or more plays are defined.

Each play has a header thet defines different properties, including the hosts on which it has to run.

In a play, one or more tasks are defined.

Playbooks are written in YAML which makes it easy to define relations between parent and child elements.

### Understanding YAML

In YAML, indentation is used to identify the relation between parent and child objects, which are specified as key: value pairs

We may also find old notations, which is key equals value.

With key common value is the prefered notation nowadays

Data elements at the same level in the hierarchy must have the same indentation and that makes it easy to find relation between different resources.

Items that are children (properties_ are indented more than the parents)

If a specific module argument can have multiple values, these values typically start with hyphen - recall those arrays.

Indentation happens using spaces. Using tabs is not allowed.

#### Configure vim for indentation, this is making sure that my indentation is happening using two spaces.
```
vim ~/.vimrc

autocmd FileType yaml setlocal ai ts=2 sw=2 et
```


### Understanding playbook elements

The relations are defined using indentation.

Yask properties are defined using key:value pairs.

Most key can have one string value.

Some keys have a list (array) value.

When a list value is defined, each item is identified using a hyphen.

In a playbook you will always start with a play and as a playbook can contain multiple plays - every play is starting with hyphen.

If we want we can create multiple plays. 

That makes sense in particular, if you want to use a couple of different settings.

In a play header you identify the target host.

### Example, first 3 lines - minimum play header
```
vim sample.yaml
---
- name: myplay
  hosts: rocky
  tasks:
  - name: task1
    debug:
	     msg: hello world
  - name: task2
    debug:
	     msg: hello task2  

- name: ubuntusettings
  hosts: ubuntu
  tasks:
  - name: hello ubuntu
    debug:
	     msg: hello ubuntu
```


Facts gathering - this is where ansible is going to discover properties of the managed hosts.

We can speed it up by using fact cash.

Fact gathering can be useless in some playbooks.



### Running playbooks

To run a playbook, the ansible-playbook command is used.

Syntax check is not needed you'll be alerted if anything is wrong with your playbook.

Use -v up to four times for increased verbosity messages.

minus v 2 times will tell you which files is used.

minus v 4 times - complete debugging information about everything. So output messages are printed on the screen.

Be default playbooks do not write any output log only standard output.

Command line version of Ansible also known as Ansible engine.


Playbook extensions can be yml and yaml



--- 
YAML identifier that is telling your YAML interpreter thet the code is staring here
















