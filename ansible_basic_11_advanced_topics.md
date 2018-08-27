# Ansible basics - Advanced Topics

[BACK TO README](README.md)

## Ansible Advanced Topics

### Preparing Windows Servers

- Ansible Control Machine can only be a Linux host and not Windows.
- Windows machines can be targets of Ansible and thus be part of Automation.
- Ansible connects to a Windows machine using **winrm**
- Requirements:
  - `pywinrm` module installed on the Ansible Control Machine: `pip install pywinrm` - _The version must be 0.2.2 or greater_
  - It is necessary to enable the WinRM in windows target servers. In order to perform that download and execute the following PowerShell script `examples/scripts/ConfigureRemotingForAnsible.ps1` - [https://github.com/ansible/ansible/tree/devel/examples/scripts](https://github.com/ansible/ansible/tree/devel/examples/scripts)
  - Different modes of authentication
    Basic/ Certificate/ Kerberos/ NTLM/ CredSSP

[Ansible Documentation - Windows Guides](https://docs.ansible.com/ansible/latest/user_guide/windows.html)

### Ansible-Galaxy

Ansible Galaxy is a free site for downloading, sharing and writing all kinds of community develop ansible roles.

This is a great way to get jumpstart on your automation projects.

### Patterns

You can use patters like wild cards.

Host*, *.compay.com

[Ansible Documentation - Patterns](https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html)

### Dynamic Inventory

instead of passing a static inventory files (which is a text file), you can pass an script which get the host list dynamically. For example by querying a Database.

Static Inventory:  
`ansible-playbook -i inventory.txt playbook.yml`

Dynamic Inventory:  
`ansible-playbook -i inventory.py playbook.yml`

### Developing Custom Modules

Modules are Python programs, therefore if you don't find any module that covers your expectation you can create a customs module by writing python programs using a particular template.

[Ansible Documentsation - Developing Modules](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules.html)
