# Ansible basics - Conditionals

[BACK TO README](README.md)

## Ansible Conditionals

Example

```txt
#Inventory file - inventory.txt
web1 ansible_host=web1.company.com ansible_connection=ssh ansible_ssh_pass=P@ssw
db   ansible_host=db.company.com ansible_connection=winrm ansible_password=P@ssword
web2 ansible_host=web2.company.com ansible_connection=ssh ansible_ssh_pass=P@ssw

[all_servers]
web1
db
web2
```

```yaml
# Playbook.yml
- name: Start services
  hosts: all_servers
  tasks:
    - name: Start the database service
      service:
        name: mysql
        state: started
      when: ansible_host == "db.company.com"

    - name: Start httpd service
      service:
        name: httpd
        state: started
      when: ansible_host == "web1.company.com" or
        ansible_host == "web2.company.com"
```

We can have an "or" stsatement like above oryou can also have an "and" statment in a similar way.

Use "==" operator for comparison.

The example above is a bad example. The correct way to perform this task would be create a group for web_servers and another for db_servers and perform two different plays within the playbook.

```txt
#Inventory file - inventory.txt
web1 ansible_host=web1.company.com ansible_connection=ssh ansible_ssh_pass=P@ssw
db   ansible_host=db.company.com ansible_connection=winrm ansible_password=P@ssword
web2 ansible_host=web2.company.com ansible_connection=ssh ansible_ssh_pass=P@ssw

[web_servers]
web1
web2

[db_servers]
db

[all_servers]
web1
db
web2
```

```yaml
# Playbook.yml
- name: Start db services Play
  hosts: db_servers
  tasks:
    - name: Start the database service
      service:
        name: mysql
        state: started

- name: Start web services Play
  hosts: web_servers
  tasks:
    - name: Start httpd service
      service:
        name: httpd
        state: started
```

## REGISTER & WHEN

Example: In this case if the service is down, I would like to send an e-mail to Sys Admins reporting the issue.

```yaml
# Playbook.yml
- name: Check status of a service and email if its down
  hosts: localhost
  tasks:
    - command: service httpd status
      register: command_output

    - mail:
        to: Admins <system.admin@company.com>
        subject: Service Alert
        body: "Httpd Service on {{ ansible_hostname }} is down."
      when: command_output.stdout.find('down') != -1
```

The `register` return the result of the task, in this case the command tasks. Register can be use to register or store the output of a module. In this case the output of the command will be stored in the variable `command_output`.  
Next we use the `command_output` variable to check if the result contains the word _down_. If it is **true**, the email will be sent out.

Remember the output of a command is stored in a key stdout, inside the variable we defined.

## Exercises

### Conditional 1

The given playbook attemps to start mysql service on all_servers. Use the when condition to run this task if the host (ansible_host) is the database server.

```txt
#Inventory File - inventory.txt

# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_ssh_pass=Password123!

[web_servers]
web1
web2
web3

[db_servers]
db1
```

```yaml
- name: Execute a script on all web server nodes
  hosts: all_servers
  tasks:
    - service: name=mysql state=started
      when: ansible_host=='server4.company.com'
```

### Conditional 2

The playbook has a variable defined _age_. The two tasks attempt to print if I am a child or ad Adult. Use the when condition to Print if I am a child or an Adult based on wether my age is <18 or >=

```yaml
- name: Am in an Adult or a Child
  hosts: localhost
  vars:
    age: 25
  tasks:
    - command: echo "I am a Child"
      when: age < 18

    - command: echo "I am an Adult"
      when: age >= 18
```

### Conditional 3

The given playbook attempts to add a new entry into **reslv.conf** file foe nameserver.  
First, we run a command using the **shell** module to get the contents of **resolv.conf** file then we add a new line containing the name server data into the file.  
However, when this playbook is run multiple times, it keeps adding new entries of sa,e line into the **resolv.conf** file.

1. Add a register directive to store the output of the first command to command_output.
2. Then add a conditional to the second command to check if the output contains the server name (10.0.250.10) already. Use: command_output.stdout.find(<IP>) == -1

**NOTE:** We already know that we can do this easly using the **lineinfile** module, this is just for practice.

**NOTE:** **shell** and **command** modules are similar in that they are used to execute a command on the system. However **shell** executes the command inside the shell giving us access to the environment variables and redirection using '>', '>>', etc.

```yaml
- name: Add name server entry if not already entered
  hosts: localhost
  tasks:
    - shell: cat /etc/resolv.conf
      register: command_output

    - shell: echo "nameserver 10.0.250.10" >> /etc/resolv.conf
      when: command_output.stdout.find('10.0.250.10') == -1
```
