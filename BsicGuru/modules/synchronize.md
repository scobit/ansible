Copying files

Th copy module can take a long time when dealing with large amounts of files

Consider using the synchronize module, is uses rsync in the background which is more efficient

For synchronize to work, ensure thar rsync is installed on the managed nodes

vim synchronize.yaml
---
- name: copy many files
  hosts: all
  become: true
  gather_facts: false
  tasks:
  - name: update web document root
    synchronize:
      src: webfiles/
      dest: /var/www/html
