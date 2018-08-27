# Ansible basics - Playbooks

[BACK TO README](README.md)

## Ansible Playbooks

Ansibl **playbooks** are ansible orchestation languages. It is defined what we want ansible to do. It is a set of instruction you provide to ansible to works with.

```yml
#Simple Ansible Playbook

- Run command1 on server1
- Run command2 on server2
- Run command3 on server3
- Restarting server1
- Restarting server2
- Restarting server3
```

- **Playbook** is a single **YAML** file
  - **Play** defines a set of activities (tasks) to be run on hosts
    - **Task** is a n action to be performes on the host
      - Execute a command
      - Run a script
      - Install a package
      - Shutdown/ Restart

```yml
#Simple Ansible Playbook1.yml
- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Execute script on server
      script: test_script.sh

    - name: Install httpd service
      yum:
        name: httpd
        state: present

    - name: Start web server
      service:
        name: httpd
        state: started
```

## Playbook format

```yml
#Simple Ansible Playbook1.yml

- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Execute script on server
      script: test_script.sh

- name: Play2
  hosts: localhost
  tasks:
    - name: Install httpd service
      yum:
        name: httpd
        state: present

    - name: Start web server
      service:
        name: httpd
        state: started
```

### host

Within the hosts, you can set any of them (one or serveral), you also con put a gruop. The selected hosts or grups must be defined in the inventory file.

### Module

The different actions run by tasks are called modules. In the example above the tasks are: `command`, `script`, `yum` and `service`

## Run

To execute an ansible playbook execute `ansible-playbook <playbook file name>`

## Demos

there are two ways of running ansible: using the `ansible` command and using the `ansible-playbook` command.

- `ansible` command:  
  it is use to run ansible for just a task for example:

  - `ansible <host> -a <command>`  
    `ansible all -a "/sbin/reboot"`

  - `ansible <hosts> -m <module>`  
    `ansible target1 -m ping`

  ```command
  [osboxes@ansible-controller section_05_playbooks_project]$ ansible all -m ping -i inventory.txt
  target1 | SUCCESS => {
      "changed": false,
      "ping": "pong"
  }
  target2 | SUCCESS => {
      "changed": false,
      "ping": "pong"
  }
  [osboxes@ansible-controller section_05_playbooks_project]$
  ```

- `ansible-playbook` command.  
  The real usage of ansible is with playbooks. The playbooks can be saved on source code repositories like **gitHub** or **gitLab** and manage centrally.

  - `ansible-playbook <playbook name>`  
    `ansible-playbook playbook-webserver.yaml`

### Demo 1

**inventory.txt**

```text
target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
target2 ansible_host=192.168.0.40 ansible_ssh_pass=osboxes.org
```

**playbook-pingtest.yaml**

```yaml
-
  name:  Test connectivity to target servers Play
  hosts:  all
  tasks:
    - name:  Ping test
      ping:
```

```command
[osboxes@ansible-controller section_05_playbooks_project]$ ansible-playbook playbook-pingtest.yaml -i inventory.txt

PLAY [Test connectivity to target servers Play] *********************************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

TASK [Ping test] ****************************************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

PLAY RECAP **********************************************************************************************************************************************************************************************************
target1                    : ok=2    changed=0    unreachable=0    failed=0
target2                    : ok=2    changed=0    unreachable=0    failed=0

[osboxes@ansible-controller section_05_playbooks_project]$
```

### Demo 2

**inventory.txt**

```text
target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
target2 ansible_host=192.168.0.40 ansible_ssh_pass=osboxes.org
```

**playbook-copyfile.yaml**

```yaml
- name: Copy file to target servers Play
  hosts: all
  tasks:
    - name: Copy file
      copy:
        src: test-file.txt
        dest: /home/osboxes/test-file.txt
```

```command
[osboxes@ansible-controller section_05_playbooks_project]$ ansible-playbook playbook-copyfile.yaml -i inventory.txt

PLAY [Copy file to target servers Play] *****************************************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

TASK [Copy file] ****************************************************************************************************************************************************************************************************
changed: [target1]
changed: [target2]

PLAY RECAP **********************************************************************************************************************************************************************************************************
target1                    : ok=2    changed=1    unreachable=0    failed=0
target2                    : ok=2    changed=1    unreachable=0    failed=0

[osboxes@ansible-controller section_05_playbooks_project]$
```
