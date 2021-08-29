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

# Modifying host inventry file

```
localhost
ansible-master.localdomain.local
node2.localdomain.local
node3.localdomain.local
node4.localdomain.local

master ansible_host=ansible-master.localdomain.local ansible_connection=ssh ansible_user=root
node2 ansible_host=node2.localdomain.local ansible_connection=ssh ansible_user=root
node3 ansible_host=node3.localdomain.local ansible_connection=ssh ansible_user=root
node4 ansible_host=node4.localdomain.local ansible_connection=ssh ansible_user=root

[servers]
master
node2
node3
node4

[nodes]
node2
node3
node4

[all:children]
servers
nodes


```

# New Modifed host inventry file adding playbook variables to host files and calling it to playbook

```
localhost
ansible-master.localdomain.local
node2.localdomain.local
node3.localdomain.local
node4.localdomain.local

master ansible_host=ansible-master.localdomain.local 
node2 ansible_host=node2.localdomain.local 
node3 ansible_host=node3.localdomain.local 
node4 ansible_host=node4.localdomain.local 

[servers]
master
node2
node3
node4

[servers:vars]
ansible_connection=ssh
ansible_user=root
status=disabled             # or you can write status=enforcing

[nodes]
node2
node3
node4

[all:children]
servers
nodes


<azureuser@azure playbooks>$ sudo nano selinux.yml

---
- name: selinux enable or disable
  hosts: servers
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

# Ansible Conditional Variables & Loops 

## Conditional Variable

```
<azureuser@azure playbooks>$ sudo nano startandstop.yml

---
- name: Start and Stop Services
  hosts: servers
  tasks:
   - service: name=postgresql state=started
     when: ansible_host == "node5.localdomain.local"  # this node is not present

   - service: name=sshd state=restarted
     when: ansible_host == "node2.localdomain.local" # this command is only applicable to node2
...

<azureuser@azure playbooks>$ ansible-playbook servers startandstop.yml

```
## Loop Variable
```
<azureuser@azure playbooks>$ sudo nano loops.yml

---
- name: Install multiple packages
  hosts: node2
  tasks:
   - name: Install packages
     yum: name={{item}} state=latest # here item is loop variable
     with_items:
     - make
     - gcc
     - httpd
     - wget

...

---
- name: Install multiple packages
  hosts: node2
  tasks:
   - name: Install packages
     yum: 
      name: "{{ item }}"                        # here item is loop variable
      state: present
     loop: 
      - {name: 'make', name: 'gcc', name: 'httpd', name: 'wget'}
     

...

<azureuser@azure playbooks>$ ansible-playbook loops.yml

```

# Deploy LAMP Stack Ansible Playbook
* L = Linux
* A = Apache (Web Server)
* M = MariaDB/MySQL
* P = PHP
We are going to deploy an application which is written in PHP language.

* Using Linux local account to install and configure LAMP
* Inatalling Apache (Web Server)
* Start and Enable services
* Install PHP and MariaDB Server
* Create Database called inventory
* Create table called servers with Six columns (sn, servername, ip, env, location remarks)
* Deploy php application which will fetch records from MariaDB in web page

# Lab
```
<azureuser@azure playbooks>$ sudo nano deploylamp.yml
---
- name: Deploying LAMP Stack
  hosts: node2
  remote_user: ansibul1
  become: yes

  tasks:
   - name: Installing Apache server
     yum: name=httpd state=latest

   - name: Start Apache Server service
     service: name=httpd state=started

   - name: Installing MariaDB Server
     yum:
      name:
       - mariadb-server
       - mariadb-devel
       - mariadb-connector-odbc
       - mariadb-server-utils
       - python3-pyMySQL
       - php
       - php-xml
       - php-mbstring
       - php-devel
       - php-soap
       - php-dba
       state: latest

   - name: Start MariaDB Server Service
     service: name=mariadb state=started

   - name: Update MariaDB Server root password
     mysql_user: 
      name: root
      host: node3
      password: mysql
      login_user: root
      check_implicit_admin: yes
      priv: "*.*:ALL,GRANT"

   - name: Create a new database called Inventory
     mysql_db: name=inventory state=present login_user=root login_password=mysql

   - name: Copy SQL file
     copy: src=/source/servers.sql dest=/tmp/servers.sql

   - name: Create Table called servers with data
     shell: mysql -u root -pmysql inventory < /tmp/servers.sql

   - name: Copy PHP files
     copy: src=/source/connection.php dest=/var/www/html/

   - name: Copy Index.php file
     copy: src=/source/index.php dest=/var/www/html/

   - name: Restart web service
     service: name=httpd state=restarted

...

<azureuser@azure playbooks>$ sudo mkdir /source
<azureuser@azure playbooks>$ cd source
<azureuser@azure playbooks>$ sudo nano servers.sql

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
SET time_zone = "+00:00";

CREATE TABLE IF NOT EXISTS 'servers' (
    'sn' init(10) NOT NULL AUTO_INCREMENT,
    'servname' varchar(255) NOT NULL,
    'ip' varchar(255) NOT NULL,
    'location' varchar(255) NOT NULL,
    'env' varchar(255) NOT NULL,
    'remarks' varchar(255) NOT NULL,
    PRIMARY KEY ('sn')
) ENGINE=MyISAM DEFAULT CHARSET=latin1 AUTO_INCREMENT=1;

--
-- Dumping data for table 'servers'
--

INSERT INTO 'servers' ('sn', 'servname', 'ip', 'location', 'env', 'remarks') VALUES (1,'ansible-master.localdomain.local', '192.168.1.18','India', 'Development', 'Ansible Development Master'), (2,'node2.localdomain.local', '192.168.1.19','India', 'Development', 'Ansible Node'), (3,'node3.localdomain.local', '192.168.1.20','India', 'Development', 'Ansible Node');

<azureuser@azure playbooks>$ sudo nano connections.php

<?php
//server info
$server = 'localhost';
$user = 'root';
$pass = 'mysql';
$db = 'inventory';

//connect to the database

$mysqli = new mysqli($server, $user, $pass, $db);

//show errors (remove this line if on a live site)

mysqli_report(MYSQLI_REPORT_ERROR);

?>

<azureuser@azure playbooks>$ sudo nano index.php

<html>
<head>
<title>Fetch Data from Databse</title>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
</head>
<body>
<?php
        // connect to the database
        include('connection.php');

        // get the records from the database
            if ($result = $mysqli->query("SELECT * FROM servers ORDER BY sn DESC"))
            {
                // display records if there are records to display
                    if ($result->num_rows > 0)
                    {
                     // display records in a table
                     echo "<table border='1' cellpadding='10'>";
                     // set table headers
                     echo "<tr><th>S.No</th><th>Server Name</th><th>IP Address</th><th>Location</th><th>Environment</th><th>Remarks</th></tr>";
                     while ($row = $result->fetch_object())
                        {
                          // set up a row for each record
                          echo "<tr>";
                          echo "<td>" . $row->sn . "</td>";
                          echo "<td>" . $row->servname . "</td>";
                          echo "<td>" . $row->ip . "</td>";
                          echo "<td>" . $row->location . "</td>";
                          echo "<td>" . $row->env . "</td>";
                          echo "<td>" . $row->remarks . "</td>";
                          echo "</tr>";
                        }

                     echo "</table>";
                    }
                    // if there are no records in the database, display an alert message
                    else
                    {
                     echo "No results to display!";
                    }
            }
            // show an error if there is an issue with the database query
            else
            {
               echo "Error: " . $mysqli->error;
            }

            // close database connection
            $mysqli->close();

?>

</body>
</html>

<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml --syntax-check

# status check is installed
<azureuser@azure playbooks>$ systemctl status httpd
<azureuser@azure playbooks>$ cd /var/www/html/
<azureuser@azure html>$ ls
<azureuser@azure html>$ cd
<azureuser@azure ~>$ systemctl status mariadb

<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml

<azureuser@azure playbooks>$ firewall-cmd --list-all
<azureuser@azure playbooks>$ systemctl stop firewalld
<azureuser@azure playbooks>$ sestatus
<azureuser@azure playbooks>$ setenforce 0

```


# Ansible Tags 

```
<azureuser@azure playbooks>$ sudo nano deploylamp.yml
---
- name: Deploying LAMP Stack
  hosts: node2
  remote_user: ansibul1
  become: yes

  tasks:
   - name: Installing Apache server
     yum: name=httpd state=latest
     tags:
     - installapache

   - name: Start Apache Server service
     service: name=httpd state=started
     tags:
     - startapache

   - name: Installing MariaDB Server
     yum:
      name:
       - mariadb-server
       - mariadb-devel
       - mariadb-connector-odbc
       - mariadb-server-utils
       - python3-pyMySQL
       - php
       - php-xml
       - php-mbstring
       - php-devel
       - php-soap
       - php-dba
       state: latest
       tags:
       - installmariadb

   - name: Start MariaDB Server Service
     service: name=mariadb state=started
     tags:
     - startmariadb

   - name: Update MariaDB Server root password
     mysql_user: 
      name: root
      host: node3
      password: mysql
      login_user: root
      check_implicit_admin: yes
      priv: "*.*:ALL,GRANT"
     tags:
     - configuredb

   - name: Create a new database called Inventory
     mysql_db: name=inventory state=present login_user=root login_password=mysql
     tags:
     - createdb

   - name: Copy SQL file
     copy: src=/source/servers.sql dest=/tmp/servers.sql
     tags:
     - copyfile

   - name: Create Table called servers with data
     shell: mysql -u root -pmysql inventory < /tmp/servers.sql
     tags:
     - dumpdata

   - name: Copy PHP files
     copy: src=/source/connection.php dest=/var/www/html/
     tags:
     - copyfile

   - name: Copy Index.php file
     copy: src=/source/index.php dest=/var/www/html/
     tags:
     - copyfile

   - name: Restart web service
     service: name=httpd state=restarted
     tags:
     - restartapache

...

<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml --syntax-check
<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml --tags "restartapache"
<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml --tags "startapache,restartapache"


------------------ Some service has to start always the put tag as always then run command without mention that tag it will run ---------------------------------

<azureuser@azure playbooks>$ sudo nano deploylamp.yml
---
- name: Deploying LAMP Stack
  hosts: node2
  remote_user: ansibul1
  become: yes

  tasks:
   - name: Installing Apache server
     yum: name=httpd state=latest
     tags:
     - installapache

   - name: Start Apache Server service
     service: name=httpd state=started
     tags:
     - startapache

   - name: Installing MariaDB Server
     yum:
      name:
       - mariadb-server
       - mariadb-devel
       - mariadb-connector-odbc
       - mariadb-server-utils
       - python3-pyMySQL
       - php
       - php-xml
       - php-mbstring
       - php-devel
       - php-soap
       - php-dba
       state: latest
       tags:
       - installmariadb

   - name: Start MariaDB Server Service
     service: name=mariadb state=started
     tags:
     - startmariadb

   - name: Update MariaDB Server root password
     mysql_user: 
      name: root
      host: node3
      password: mysql
      login_user: root
      check_implicit_admin: yes
      priv: "*.*:ALL,GRANT"
     tags:
     - configuredb

   - name: Create a new database called Inventory
     mysql_db: name=inventory state=present login_user=root login_password=mysql
     tags:
     - createdb

   - name: Copy SQL file
     copy: src=/source/servers.sql dest=/tmp/servers.sql
     tags:
     - copyfile

   - name: Create Table called servers with data
     shell: mysql -u root -pmysql inventory < /tmp/servers.sql
     tags:
     - dumpdata

   - name: Copy PHP files
     copy: src=/source/connection.php dest=/var/www/html/
     tags:
     - copyfile

   - name: Copy Index.php file
     copy: src=/source/index.php dest=/var/www/html/
     tags:
     - copyfile

   - name: Restart web service
     service: name=httpd state=restarted
     tags:
     - always

...

<azureuser@azure playbooks>$ ansible-playbook deploylamp.yml --tags "startmariadb"

```

# Ansible Roles
### Configuration Management and App deployment tool
* Larger playbooks are split into multiple files
* Roles are a way to group multiple different tasks or complex tasks
* Reuse of playbook code is easy with roles
* Roles are major for Ansible
* roles have a structured layout

### Roles Directory Structure
* Defaults: Default variables for the role
* Files: Contains files to copy to destination
* Handlers: Based on notify do something specified
* Meta: Meta data about current role
* Tasks: List of tasks to be executed by the role
* Templates: Template files to deploy
* Tests: If you want additional verification of you build
* Vars: Other variables for the role

```
.
|___ apache
     |
     |____ defaults
     |     |___ main.yml
     |
     |____ files
     |
     |____ handlers
     |     |___ main.yml
     |
     |____ meta
     |     |___ main.yml
     |
     |____ README.md
     |
     |____ tasks
     |     |___ main.yml
     |
     |____ templates
     |
     |____ tests
     |     |___ main.yml
     |
     |____ vars
           |___ main.yml

```

# Lab

```
<azureuser@azure playbooks>$ ansible-galaxy init webserver --offline
# Above command download directory structure from ansible website repository https://galaxy.ansible.com
Note : Refer webserver folder of this repository here i have changed few files as below
files - created index.html
meta - added purpose details
handlers - added handlers in main.yml
tasks - added tasks in main.yml
webserver.yml - created webserver.yml in webserver root folder

<azureuser@azure playbooks>$ ansible-playbook webserver.yml --syntax-check
<azureuser@azure playbooks>$ ansible-playbook webserver.yml



```