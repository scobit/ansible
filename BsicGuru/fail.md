### Managing failure with the fail module

Understanding failure handling

Ansible looks at the exit status of a task to determine whether it has failed.

This may lead to failures occuring when nothing really is going wrong.

When any taks fails, ansible aborts the rest of the play on that host and continues with the next host.

different solutions can be used to change that behavior

use ignore_errors in a task to ignore failures

It can be used in a task or in a play header

Use force_handlers to force a handler that has been triggered to run, even if (another) task fails

Defining Failure States

As ansible only looks at the exit status of a failed task, it may think a task was successful where that is not the case 

To be more specific, use failed_when to specify what to look for in command output to recognize a failure

Using the fail module

The failed_when keyword can be used in a task to identify when a task has failed

The fail module can be used to print a message that informs why a task has failed

To use failed_when of fail, the result of the command must be registered, and the registered variable output must be analyzed

When using the fail module, the failing task must have ignore_errors set to yes. Because the  essence of the fail module is that you want to do something if a failure occurs. And if you want to do something after the failure, well, it's mandatory to use ignore errors, because otherwise play execution will be aborted on that specific host. 



vim failure.yml

---
- name: demonstrating failed_when
  hosts: all
  tasks: 
    - name: run a script
      command: echo hello world
      ignore_errors: yes
      register: command_result
      failed_when: "'world' in command_result.stdout"
    - name: see if we get here
      debug:
        msg: hello
		
Anytime you use register, register creates a variable name.stdout with the result of the actual command


vim failure2.yml
---
- name: show the fail module
  hosts: all
  tasks:
  - name: do somthing impossible
    lvol:
      lv: lvnothing
      vg: vgnothing
      size: 1G
    register: command_result
    ignore_errors: yes
  - name: show command_result errors
    debug:
      var: command_result.err
  - name: print nice error message after failure
    fail:
      msg: volume group doesnt exist
    when: "'not found' in command_result.err"
