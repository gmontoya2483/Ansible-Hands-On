# Ansible Advance - Vault

[BACK TO README](README.md)

## Ansible Advanced Vault

```txt
#Inventory File - inventory.txt

db_server ansible_ssh_pass=PassW0rd ansible_host=192.168.1.1
web_server ansible_ssh_pass=PassW0rd ansible_host=192.168.1.2

```

We´ve been storing the credentials to get connected to the target servers in a plain text format.  

Storing password and other confidential information in plain text format is obviouly not a best practice.  

Ansible Vault helps us to store this data in an encrypted format.  

There are multiple ways to use ansible vault. Since we already have an inventory file, we eill simple try to encrypt the current inventory file itself.  

To do that run the following command to encrypt the inventory file.  

``ansible-vault encrypt inventory.txt``  

When this is run, you´re asked for a new vault password and on entering the vault password the file is encrypted

```command
montoya@N900D279:~/MyDocuments/ansible$ ansible-vault encrypt inventory.txt
 [WARNING] Ansible is being run in a world writable directory (/mnt/c/Users/montoya/Documents/ansible), ignoring it as an ansible.cfg source. For more information see https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
New Vault password:
Confirm New Vault password:
Encryption successful
montoya@N900D279:~/MyDocuments/ansible$
```

```command
montoya@N900D279:~/MyDocuments/ansible$ cat inventory.txt
$ANSIBLE_VAULT;1.1;AES256
66353362643238623232313238666633643633363039366437663332613862303966643963386335
6363653932353765363331333265623239363361383838300a356466613964323130623131633938
63633734633339333764643931353662626236646461666135303765323431626666646562663961
3261373964623233630a316630346462343961393162383664646363616438623731623233643933
30396130613533393836646365313866613863653231343366633565663036353635653431633763
35626663353962623766623364313239303636326364393634373237656163333934666635616264
34323232396635366464303032356537613032643663643234643763326531346435363534353565
36306530333561613661323963313961643465366435383136333034393062613237643364353263
64346137346335613964373138613738613737383936616534633035626232336130383563613866
66313735653737393162613533326665353139353332363933333333363237613331656131643036
61643531306163303632366630376631633531363537336336303738666135396465356433616139
62626638376331613633663266313530373234363061306134636139306161626662326261316433
3834
montoya@N900D279:~/MyDocuments/ansible$
```

### ANSIBLE-VAULT

If we try to run the playbook now, we will get an error like this,

``ansible-playbook playbook.yml -i inventory.txt``

``ERROR! Attempted to read "inventory.txt" as ini file: Encrytion failed on inventory.txt``

What it means is that ``ansible-playbook`` was not able to decrypt the file.  
If you are using any encrypted file, then we must append the option ``ask-vault-pass``, so ansible ask us for the vault password. On entering the same password we used while encryting the file, the playbook now runs.

``ansible-playbook playbook.yml -i inventory.txt -ask-vault-pass``

Instead of prompting for the vault password, you could have the vault password stored in a file and have that file passed in as a value to ``-vault-password-file ~``  

``ansible-playbook playbook.yml -i inventory.txt -vault-password-file ~./vault_pass.txt``  


Another way instead of passing a plain text file containing the password is to passing a Python script instead.  

``ansible-playbook playbook.yml -i inventory.txt -vault-password-file ~./vault_pass.py``  

That script is a program which retrieves the password from a secure location.

### Working with encripted files

* To see the content of an encripted file, use the command ``ansible-vault view``:  
  ``ansible-vault view inventory.txt``

* To create an encripted file, use the command ``ansible-vault create``:  
  ``ansible-vault create inventory.txt``

* To Edit an encripted file, use the command ``ansible-vault edit``:  
  ``ansible-vault edit inventory.txt``

* To Encript a file, use the command ``ansible-vault encrypt inventory.txt``:  
  ``ansible-vault encrypt inventory.txt``

* To change the password of encryted files,  use the command ``ansible-vault rekey``:  
  ``ansible-vault rekey inventory.txt``


[Ansible Documentation - Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)



