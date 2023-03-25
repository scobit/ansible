Understanding tags

example in kubernetes playbook

A tag is a label that can be used in a playbook to identify specific playbook elements

Using tags allows you to run specific parts of the playbook only

Tags are useful in playbook development to skip parts that have proved to be working all right

ansible-playbook --help

ansible-playbook PlayBookName.yml --list-tags

ansible-playbook PlayBookName.yml --skip-tags

ansible-playbook PlayBookName.yml -t TagsName

ansible-playbook PlayBookName.yml --tags TagsName -e kubehost=localhost

ansible-playbook PlayBookName.yml --start-at-task TaskName
