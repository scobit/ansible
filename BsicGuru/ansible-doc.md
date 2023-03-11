Ansible-doc has doc not only for modules, it has doc for about everything.

Module documentation is in ansible-doc.

#### to show a list of all modules
```
ansible-doc [-t module] -l 
```

#### Show a detailed desription of the module and its options
```
ansible-doc modulename 
```

Notice the example section that exists at the end of the documentation for each of the modules.

Arguments also knows as options.

= in doc means mandatory options.



#### Linsting modules
```
# Pre-2.10: 
ansible-doc -l

# 2.10 and later: 
ansible-doc -t module -l
```
