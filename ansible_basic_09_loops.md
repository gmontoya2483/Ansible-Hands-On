# Ansible basics - Loops

[BACK TO README](README.md)

## Ansible Loops

### WITH_ITEMS

```yaml
-
  name: Install Packaged
  hosts: localhost
  tasks:
    - yum: name= httpd state=present
    - yum: name= binutils state=present
    - yum: name= glibc state=present
    - yum: name= mongodb state=present
    - yum: name= mc state=present

```

This is a better way to perform the same action but using with_items Loops.


```yaml
-
  name: Install Packaged
  hosts: localhost
  tasks:
    - yum: name= '{{ item }}' state=present
      with_items:
        - httpd
        - binutils
        - glibc
        - mongodb
        - mc
```

[Ansible documentation for loops](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

### Excercises

* **Loops 1**  
  the playbook currently runs an echo command to print a fruit name. Apply a loop directive (**with_items**) to the tsask to print all fruits defined in the **fruits** variable

  ```yaml
  -
  hosts: localhost
  vars:
      fruits:
        - Apple
        - Banana
        - Grapes
        - Orange
  tasks:
    -
      command: echo "{{ item }}"
      with_items: '{{ fruits }}'
  ```


* **Loops 2**
  We are attempting to install multiple packages using **yum** module.

    ```yaml
    -
    name: Install required packages
    hosts: localhost
    vars:
        packages:
        - httpd
        - binutils
        - glibc
        - ksh
        - libaio
        - libXext
        - gcc
        - make
        - sysstat
        - unixODBC
        - mongodb
        - nodejs
        - grunt

    tasks:
        -
        yum: name='{{ item }}' state=present
        with_items: '{{ packages }}'
    ``` 

