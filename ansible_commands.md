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




