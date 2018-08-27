# Ansible basics - Modules

[BACK TO README](README.md)

## Ansible Modules

Ansible **Modules** are categorized into various groups based on their functionallity:

- System  
   System modules are actions to be performed at a system level: such as modifying the users and groups on the system, modifying IP tables and firewall configurations, working with logical volume groups, mounting operation or workings with services (starting, stopping or restarting).

  - User
  - Group
  - Hostname
  - Iptables
  - Lvg
  - Lvol
  - Make
  - Mount
  - Ping
  - Timezone
  - Systemd
  - Service

- Commands  
   Command modules are used to execute commands or scrpts on a host. These could be simple commands using the command module or an interactive execution using expect by responding to prompts. You could also run a script on the host using the script module.

  - Command
  - Expect
  - Raw
  - Script
  - Shell

- Files  
   File modules help working with files. For example using the Acl module to set a retrive Acl infoemtion on files. Use the Archive and unarchive modules to compress and unpack files. Use Find, Lineinfile and replace modules to modify the content of existing file.

  - Acl
  - Archive
  - Copy
  - File
  - Find
  - Lineinfile
  - Replace
  - Stat
  - Template
  - Unarchive

- Database  
   Database modules helps in working with databases such as mongoDb, MSsql, Mysql or Postgresql, to add or remove databases or modify database configurations, etc.

  - Mongodb
  - Mssql
  - Mysql
  - Postgresql
  - Proxysql
  - vertica

- Cloud  
   The clouds section has a vast collection of modules for various different cloud providers like Amazon, Azure, Docker, Google, Openstack, Linode, Digital Ocean and VMware being just a few of them. There are a number of modules available for each of these that allow you to perform various tasks such as creating and destroying instances, performing configuration changes and networking and security, managing containers, data centers, clusters, social networking, Vsan and a lot more.

* Windows  
   Windows modules helps use Ansible in a Windows environment. Some of them are:

  - Win_copy
  - Win_command
  - Win_domain
  - Win_file
  - Win_iis_website
  - Win_msi
  - Win_shell
  - Win_user
  - Win_service
  - Win_ping
  - And more ...

* More ...

## COMMAND

Executes a command on a remote node

[Ansible Documentation - Command Module](https://docs.ansible.com/ansible/latest/modules/command_module.html#command-module)

```yaml
#Simple Ansible Playbook.yml

- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Display resolv.conf contents
      command: cat /etc/resolv.conf
```

In case you need to change directory before executing a command, the correct way would be:

```yaml
#Simple Ansible Playbook.yml

- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Display resolv.conf contents
      command: cat resolv.conf chdir=/etc
```

This will ensure that ansible changes directory to `/etc` before executing the command.

The creates parameter is used to perform a check before the command is run:

```yaml
#Simple Ansible Playbook.yml

- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Display resolv.conf contents
      command: cat resolv.conf chdir=/etc

    - name: Display resolv.conf contents
      command: mkdir /folder creates=/folder
```

```yaml
#Simple Ansible Playbook.yml

- name: Copy file to target servers Play
  hosts: localhost
  tasks:
    - name: Create a folder
      command: mkdir temp
      args:
        chdir: /home/osboxes
        creates: /home/osboxes/temp
```

The command to creates the folder will only run if the folder does not already exist.

The command module has a requiered parameter `free_form` this parameter in the examples above are `date`, `cat`, `mkdir`

## SCRIPT

Runs a local script on a remote node after transfering it. To run a script on one or more servers, you don't have to copy it all over all the servers first. Ansible takes care of automatically copying the script to the remote node and then executing it on the remote systems.

```yaml
#Simple Ansible Playbook.yml

- name: Play 1
  hosts: localhost
  tasks:
    - name: Run a script on a remote server
      script: /some/local/script.sh -arg1 -arg2
```

[Ansible Documentation - Script Module](https://docs.ansible.com/ansible/latest/modules/script_module.html#script-module)

## SERVICE

The service module is used to manage services on a system such as starting, stopping or restarting a service.

```yaml
#Sample Ansible Playbook.yml

- name: Start services in order
  hosts: localhost
  tasks:
    - name: Start the database service
      service: name=postgresql state=started
```

or

```yaml
#Sample Ansible Playbook.yml

- name: Start services in order
  hosts: localhost
  tasks:
    - name: Start the database service
      service:
        name: postgresql
        state: started
```

The **state** is `started` because we do not trigger ansible to start the service, we trigger ansible to ensure the service is `started`

That means:
if service is not already started => start it  
if service is already started => do nothing

This property is call **Idempotency**. An operation is idempotent if the result of performing it once is exactly the same as the result of performing it repeatedly without any intervening actions.

[Ansible Documentation - Service Module](https://docs.ansible.com/ansible/latest/modules/service_module.html#service-module)

## LINEINFILE

Search for a line in a file and repalce it or add it if it doesn't exist. For example, we are given a task to add a new DNS server into the `/etc/resolv.conf`

```text
#Sample /etc/resolv.conf

nameserver 10.1.250.1
nameserver 10.1.250.2
```

```yaml
#Sample Ansible Playbook.yml

- name: Add DNS server to resolv.conf
  hosts: localhost
  tasks:
    - lineinfile:
        path: /etc/resolv.conf
        line: "nameserver 10.1.250.10"
```

[Ansible Documentation - Lineinfile](https://docs.ansible.com/ansible/latest/modules/lineinfile_module.html#lineinfile-module)
