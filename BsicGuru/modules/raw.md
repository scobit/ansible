ansible.builtin.raw runs the command without the need for python being present on the managed nodes.

raw in the past was useful because it allowed you to install python on managed nodes that did not have python yet.

Nowadays python is a part of the base linux installation of all distributions.
