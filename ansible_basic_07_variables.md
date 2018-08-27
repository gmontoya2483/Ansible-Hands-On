# Ansible basics - Variables

[BACK TO README](README.md)

## Ansible Varible

- Stores information that varies with each host

- Within the Inventory file, `ansible_host`, `ansible_connection`, `ansible_shh_pass` are examples of variables. We can define as many variables as required.

  ```txt
  target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
  target2 ansible_host=192.168.0.40 ansible_ssh_pass=osboxes.org
  ```

- We can create variables within a Playbook

  ```yaml
  #Sample Ansible Playbook.yml

  - name: Add DNS server to resolv.conf
  hosts: localhost
  vars:
      dns_server: 10.1.250.10
  tasks:
      - lineinfile:
          path: /etc/resolv.conf
          line: 'nameserver {{ dns_server }}'
  ```

- We can have variables in a separate file dedicated for Variables.

  ```yaml
  #Sample Variable_file.yml
  variable1: value1
  variable2: value2
  ```

- In order to define host variables the best approach is to create a variable file for each host

  ```txt
  #Inventory file
  target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
  target2 ansible_host=192.168.0.40 ansible_ssh_pass=osboxes.org
  ```

  ```yaml
  #Variable file - target1.yml

  http_port: 8081
  snmp_port: 161-162
  inter_ip_range: 192.0.2.0
  ```

  ```yaml
  #Variable file - target2.yml

  http_port: 8083
  snmp_port: 161-162
  inter_ip_range: 192.0.3.0
  ```

  ```yaml
      #Playbook.yml

      - name: Set Firewall Configurations
        hosts: all
        tasks:
          - firewalld:
              service: https
              permanent: true
              state: enabled

          - firewalld:
              port: '{{ http_port }}'/tcp
              permanent: true
              state: enabled

          - firewalld:
              port: '{{ snmp_port }}'/udp
              permanent: true
              state: disabled

          - firewalld:
              source: '{{ inter_ip_range }}'/24
              Zone: internal
              state: enabled
  ```
