# Ansible Ad-Hoc Commands

ansible [host file added Group/Server Name] -m [module] -a [arguments] -u [user name] --become

ansible -i [path-to-inventry-file] -m [module] -a [arguments] -u [user name] --become

    -B 'SECONDS' [Running in Background]

    -k (ask password)

    -T 'TIMEOUT'

    -a 'MODULE_ARGS'

    -b (become)

    -i (inventory file)

    -m Module

    -v or -vvv or -vvvv

## Ad-Hoc Command Examples
    ansible servers -a 'uptime'

    ansible servers -m shell -a 'systemctl status sshd'

    ansible servers -m command -a 'df -h'

    touch testingansible

    ansible servers -m copy -a "src=/root/testingansible dest=/home/azureuser/testingansible"

    ansible servers -m yum -a "name=wget state=present"

    ansible servers -m yum -a "name=wget state=absent"

    absible servers -m user -a "name=azureuser password=redhat"

        cat /etc/passwd |grep azureuser

    ansible servers -m service -a "name=sshd state=started"

    ansible servers -m shell -a "/sbin/service sshd status"

# How to write First Ansible Playbook 
1. Playbook begin with ---
2. Comments begin with #
3. Members of lists begins with -
4. Playbook ends with ...
5. Key value Pairs "<"key">": "<"value">"
6. All the playbooks are written in YAML == "[Y]et [A]nother [M]arkup [L]anguage"

## Playbooks ar combination of plays and tasks
A play can have number of tasks. In playbook there is number of plays. In this example here single play and multiple tasks. [Note: Alignment should be perfect]
### Playbook Alignment
```
---
-host: all
 tasks:
    -name: Copy File
    -copy:
        src: /home/azureuser/.ssh/id_rsa.pub
        dest:/home/azureuser/.ssh/authorized_keys
            owner:azureuser
...
```
