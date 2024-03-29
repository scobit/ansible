### Manipulating Files

ansible.builtin.copy is used to copy files from the control host to a managed host

ansible.posix.synchronize synchronizes files content and is more efficient

The synchronized module has one requirement - it needs rsync to be installed on managed host

ansible.builtin.fetch will fetch a file from a managed host to the control host


Using copy:
Use ansible.builtin.copy as an ansible alternative to linux copy

It can copy over individual files, as well as directories

use contents to copy a line of text into a new destination file

Using synchronize

ansible.posix.synchronize is using the rsync command to synchronize files from the control host to managed hosts

ansible.posix.synchronize is more efficient for copying large amounts of files than ansible.builtin.copy, because it is capable of doing a DeltaSync. So we will only synchronize things that really needs to be changed

It does require the rsync software to be installed on all hosts involved

Use delegate_to to change the source host to a host different than localhost

Using fetch

ansible.builtin.fetch can be used to fetch a file from a managed machine and store it on the control host

It will store files by appending hostname /path/to/file to the fetched file

Use the flat option to override this behavior and store just the filename



Changing file contents

ansible.builtin.copy can be used to create files with simple contents
ansible.builtin.replace is used for simple replacements
ansible.builtin.lineinfile can be used to manipulate single lines in  a text file
ansible.builtin.blockinfile is useful for manipulating complete blocks of text

Using copy and replace
ansible.builtin.copy and ansible.builtin.replace can be used for simple operations

Use the content argument to the copy module to copy a string into a destination file
  ansible all -m copy "content=hello dest=/tmp/hellofile"
  
The replace module allows you to replace file contents based on regular expression

 use regexp to identify the regular expression you want to replace
 use replace to specify the replacement text
 Omit the replace argument if you just want to remove text
 

Using lineinfile and blockinfile

Use lineinfile to add a single line to a file

Use blockinfile to add multiple lines to a file

vim copy.yml

---
- name: file copy modules
  hosts: all
  tasks:
  - name: copy file demo
    copy:
      src: /etc/hosts
      dest: /tmp/
  - name: add some lines to /tmp/hosts
    blockinfile:
      path: /tmp/hosts
      block: | 
        192.168.4.110 host1.example.com
        192.168.4.120 host2.example.com
      state: present
  - name: verify file checksum
    stat:
      path: /tmp/hosts
      checksum_algorithm: md5
    register: result
  - debug:
      msg: "The checksum of /tmp/hosts is {{ result.stat.checksum }}"
  - name: fetch a file
    fetch:
      src: /tmp/hosts
      dest: /tmp/
	  
Pipe behind the name of the module, which is making sure that the next couple of lines iw written to the configuration file with this specific layout. Text is wrapped over multiple lines.

The stat module is mostly used in combination with the register



Lab working with files

write a playbook that is doing the following

Installs vsftpd on all managed hosts

copies the file /etc/vsftpd/vsftpd.conf from one of the managed hosts to the control host

converts that file into a template, adding one line to the end of the file
# written on {{ ansible_hostname }}

uses the template to create a new version of the configuration file on the managed hosts

ensures that vsftpd is started, enabled and accessible through the firewall

dash - list
no dash - properties

When do we need a dash to indicate a list and when don't we need it? - we don't need to put arguments to a module

ansible-doc -s ??? check meaning

vim lab7.yml
---
 - name: copy conf to local
   hosts: all
   tasks:
   - name: install vsftpd
	 dnf:
	   name: vfsftpd
	   state: latest
   - name: fetch the file
	 fetch:
	   src: /etc/vsftpd/vsftpd.conf
	   flat: test
	   dest: /tmp/vsftpd.j2
		 
 - name: modify file on control
   hosts: localhost
   become: yes
   tasks:
    - name: add line to file
	  lineinfile:
	    name: /tmp/vsftpd.j2
		line: '# write on {{ ansible_hostname }}'
		
 - name: finish it
   hosts: all
   tasks:
    - name: use template
	  template:
	    src: /tmp/vsftpd.j2
		dest: /etc/vsftpd/vsftpd.conf
 - name: start it
   service:
     name: vsftpd
	 state: started
	 enabled: yes
 - name: open firewall
   firewalld:
     service: ftp
	 immediate: yes
	 permanent: yes
	 state: enabled
	 


ansible ansible1 -a "systemctl status vsftpd"

I like to improvise =)
Let's move on
