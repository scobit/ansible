### Using the assert module

The assert module can be used to show a message on success and on failure

It's a bit like the fail module, but with more advanced options

vim assertsize-wrong.yml

---
- hosts: localhost
  vars_prompt:
  - name: filesize
    prompt: "specify a file size in megabytes"
	private: no
  tasks:
  - name: check if file size is valid
    assert:
      that:
      - filesize <= 100
      - filesize >= 1
      fail_msg: "file size must be between 0 and 100"
      success_msg: "file size is good, let\'s continue"
  - name: create a file
    command: dd if=/dev/zero of=/bigfile bs=1 count={{ filesize }} 
	
If we enter a value using vars prompt, we get a string.
If we want to do greater than smaller than operations on a variable, it needs to be an integer

vim assertsize.yml
---
- hosts: localhost
  vars_prompt:
  - name: filesize
    prompt: "specify a file size in megabytes"
	private: no
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
	
This is example of passing through the integer filter.
This filter does not changing the contents of variable, it's just changing the interpretation

Assert, a little bit like block and rescue and always - is just another way of implementing something that looks like an if the else statement


