Processing variables with filters

Filters allow variables to be processed before using them in ansible

Filters do not change the value of a variable, they just change how the variable is being used

Use {{ varname | filter }} to identify the filter to be used

vim int-filter.yaml
---
- hosts: localhost
  vars_prompt:
  - name: filesize
    prompt: "specify a file size in megabytes"
  tasks:
  - name: check if file size is valid
    assert:
      that:
      - "{{ (filesize | int) <= 100 }}"
      - "{{ (filesize | int) >= 1 }}"
      fail_msg: "file size must be between 0 and 100"
      success_msg: "file size is good, let\'s continue"
  - name: create a file
    command: dd if=/dev/zero of=/bigfile bs=1 count={{ filesize }} 
	
vim storage-filter.yaml
---
- name: set up hosts that have an sdb device
  hosts: all
  tasks:
  - name: getting out with a nice failure message if there is no second disk
    fail:
      msg: there is no second disk
    when: ansible_facts['devices']['sdb'] is not defined
  - name: create a partition
    parted:
      device: /dev/sdb
      number: 1
      state: present
  - name: create a volume group
    lvg:
      pvs: /dev/sdb1
      vg: vgfiles
  - name: run the setup module so that we can use updated facts
    setup:
  - name: get vg size and convert to integer in new variable
    set_fact:
      vgsize: "{{ ansible_facts['lvm']['vgs']['vgfiles']['size_g'] | int }}"
  - name: show vgsize value
    debug:
      var: "{{ vgsize }}"
  - name: create an LVM on big volume groups
    lvol:
      vg: vgfiles
      lv: lvfiles
      size: 6g
    when: vgsize | int > 5
  - name: create an LVM on small volume groups
    lvol:
      vg: vgfiles
      lv: lvfiles
      size: 3g
    when: vgsize | int <= 5
  - name: formatting the XFS filesystem
    filesystem:
      dev: /dev/vgfiles/lvfiles
      fstype: xfs
  - name: mounting /dev/vgfile/lvfiles
    mount:
      path: /files
      state: mounted
      src: /dev/vgfiles/lvfiles
      fstype: xfs
	  
Default interpretation of prompt variable is string

  
Exploring Common filters

mandatory fails a play if a variable doen not have a value 
{{ my_var | mandatory }}

default will set a variable without value to a default value. Add the optional True to also set the variable if it has an empty string or a boolean False as its value 
{{ my_var | default(myvalue,True) }}

capitalize will capitalize a string

int will convert a variable to an integer

float converts a variable to a float

+ - / * perform basic calculations {{ ( vgsize | int) -1 }}

union creates a single list based on multiple input lists

random extracts a random element from a list 
{{ [a,b,c] | random }}

sort will sort a list 
{{ [4,8,2,5] | sort }}

password_hash generates a hashed password

quote used to put command output between quotes so that it is sanitized

vim assert_filter.yaml
---
- name: demonstrate filters
  hosts: localhost
  tasks:
  - name: manipulate a list and check
    assert:
      that:
      - "{{ [ 2, 3, 1, 2, 4, 2 ] | sort | unique | list }} is eq( [ 1, 2, 3, 4 ] )"
	  

vim password_hash.yaml
---
- name: create user with encrypted pass
  hosts: ansible2
  vars_prompt:
  - name: passw
    prompt: which password do you want to use
  vars:
    user: sharon
  tasks:
  - debug: 
      msg: "{{ '{{ passw }}' | password_hash('sha512','myrandomsalt') }}"
    register: mypass
  - debug:
      var: mypass
  - name: create the user
    user: 
      name: "{{ user }}"
      password: "{{ mypass.msg }}"


########################

Understanding the ipaddr filter

The ipaddr filter works on IP addresses to show specific IP address-related output

Before using, install the python3-netaddr as well as python3-dns package libraries

Arguments:
address: validates that input contains valid IP addresses
net: validates that input values are network ranges
host: ensures that IP addresses conform to CIDR prefix format
prefix: returns the network address prefix

vim network.yaml
---
- name: exploring network plugins and filters
  hosts: all
  vars:
    addresses:
    - 1.2.3.4/24
    - 10.0.0.0/8
    net_mask: "{{ ansible_default_ipv4.network }}/{{ ansible_default_ipv4.netmask }}"
  tasks:
  - name: print IP address
    debug:
      msg: the IP address is {{ ansible_facts['default_ipv4'] }}
  - name: show if this is a valid IP address
    debug:
      msg: "{{ ansible_facts['default_ipv4']['address'] | ipaddr }}"
  - name: ths happens if the variable does not contain a valid IP address
    debug:
      msg: "{{ ansible_facts['default_ipv4'] | ipaddr }}"
  - name: now lets check out the netmask
    debug:
      msg: "{{ net_mask | ipaddr('netmask') }}"
  - name: net is used to convert netmask to CIDR notation
    debug:
      msg: "{{ net_mask | ipaddr('net') }}"
  - name: host/prefix will filter out only valid host IP addresses and skip network addresses from a list in 1.1.1.1/24 format
    debug:
      msg: "{{ addresses | ipaddr('host/prefix') }}"
  - name: or size to show available size in this specific network
    debug:
      msg: "{{ ansible_facts['default_ipv4']['network'] | ipaddr('size') }}"
  - name: rewriting an IPv4 address to IPv6
    debug:
      msg: "{{ addresses | ipv4('ipv6') }}"
  - name: and how about converting IP addresses to hex format
    debug:
      msg: "{{ ansible_facts['default_ipv4']['address'] | ip4_hex }}"
  - name: or int format
    debug:
      msg: "{{ ansible_facts['default_ipv4']['address'] | ipaddr('int') }}"
  - name: an how about finding available IP addresses in a range
    debug:
      msg: "{{ '193.179.99.8/27' | ipaddr('range_usable') }}"


