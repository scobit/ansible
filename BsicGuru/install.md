Ansible is dedeloped on top of python, so all dependencies are python3 based packages.

We need python stack in order to work with ansible.

#### Установка epel (Extra packages for Enterprise Linux)
```
cat /etc/redhat-release

yum install epel-release

dnf install epel-release
```

#### Установка Ansible
```
dnf install -y ansible
```

#### Поиск пакета pip и установка
```
dnf provides */pip

yum -y install python3-pip

dnf -y install python3-pip
```

#### Установка ansible через pip
```
pip3 install ansible
```


Недостаточк pip3 - установка в обход встроенного механизма установки ПО (к примеру альтернативное расположение файлов после установки и т.д.).

