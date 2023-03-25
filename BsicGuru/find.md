
Using the find module

The find module is used to perform operations on multiple files

It is similar to the Linux find command

Use it with regular expressions, or in combination with lineinfile to perform modifications on multiple files that match a certain criterium

When using register together with find, you can easily build a playbook to change files regardless of their exact location

vim changeconfig.yaml

---
- name: find config
  hosts: localhost
  become: true
  tasks:
  - name: find ansible.cfg files
    find:
      paths: /home/ansible/rhce8-live
      patterns: 'ansible.cfg' 
      recurse: yes
    register: output
  - debug:
      var: item.path
    with_items: "{{ output.files }}"
  - blockinfile:
      path: "{{ item.path }}"
      insertafter: "defaults"
      marker: "##<!-- {mark} ANSIBLE MANAGED BLOCK -->##"
      block: |
        fact_caching = redis
        fact_caching_timeout = 3600
        fact_caching_connection = localhost:6379:0
    with_items: "{{ output.files }}"


If we want to set up the fact cache, we need to change ansible.cfg

The thing is that find will generate a list of files. This list of files is written to the files variable within the output variable.
