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
- host: all
  tasks:
    - name: Copy File
    - copy:
        src: /home/azureuser/.ssh/id_rsa.pub
        dest:/home/azureuser/.ssh/authorized_keys
            owner:azureuser
...

A few rules you need to follow is. when you want include: (colon) in statement you have to enable quotations ("") from start to end shown example is below

-name: Incorrect: Method: to Write
- name: "Testing: Production and UAT"

you can still write paths as like

windows_path: c:\window

to tell you further on writing ansible variables and there examples

content: "{{ correct }}"
variable: "{{ correct }} Testing"
 contnt: "{{ Wrong }}" Testing

```

# Lab - Writing simple playbook for ping to all servers
```
<azureuser@azure>$ mkdir /playbooks 
<azureuser@azure>$ ls
<azureuser@azure>$ cd /playbooks/
<azureuser@azure playbooks>$ sudo nano pingtest.yml or sudo nano pingtest.yaml #any extension is valid.

---
- name: First playbook ping test
  hosts: servers
  tasks:
    - name: Ping test
      ping:
...

<azureuser@azure playbooks>$ ansible-playbook pingtest.yml

```

# Ansible Variables

1. Variable names should be letters, numbers and underscores_.
2. Should always starts with a letter
3. Not valid variables examples
    * Azure-user
    * Tech Azure
    * 21

# Ansible Host Variable
* You can also use variables in invenory files
* Example:
    [webservers]
    Node1
    Node2
    Node3

    [webservers:vars]
    ansible_connection=ssh
    ansible_user=root

# Lab - Simple playbook for disabling SELinux in CentOS
```
<azureuser@azure playbooks>$ sudo nano selinux.yml

---
- name: selinux enable or disable
  hosts: servers
  vars:
    status: disabled
  tasks:
    - name: changing SELinux from config file
      lineinfile:
        path: /etc/selinux/config
        regexp: '^SELINUX='
        line: 'SELINUX={{ status }}' # status variable called from above vars section status: disabled
...

<azureuser@azure playbooks>$ cat /etc/selinux/config

<azureuser@azure playbooks>$ ansible-playbook servers selinux.yml

```