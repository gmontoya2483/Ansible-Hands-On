# Ansible basics - Inventory

[BACK TO README](README.md)

## Ansible Inventory

Ansible stablishes connectivity to the servers via `SSH` when the server is **Linux** or `Powershell Remoting` in case the host is Windows.

Ansible is **agentless**, that means you don't have to install anything on the target machines.

Information of the machines is stored in an inventory file. In case you don't create a new inventory file, ansible uses the default inventory file which is located `/etc/ansible/hosts`.

- Example of an Inventory file:

Invertory file follows the **ini** format:

```ini
#Sample Inventory File

Server1.company.com
Server2.company.com
```

Servers can be grouped:

```ini
#Sample Inventory File

Server1.company.com
Server2.company.com

[mail]
Server3.company.com
Server4.company.com

[db]
Server5.company.com
Server6.company.com

[web]
Server7.company.com
Server8.company.com

[boston_nodes]
Server2.company.com
Server6.company.com
Server8.company.com
```

You can have multiple sub groups defined in a single inventory file.

You can target any group of servers in a **playbook**.

You can add a server to different groups.

Finally you can also have a group of groups. The group of server must have the **:children**

```ini
#Sample Inventory File

Server1.company.com
Server2.company.com

[mail]
Server3.company.com
Server4.company.com

[db]
Server5.company.com
Server6.company.com

[web]
Server7.company.com
Server8.company.com

[all_servers:children]
mail
db
web
```

- Alias

```ini
#Sample Inventory File

web  ansible_host=Server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=P@ssword
db   ansible_host=Server2.company.com ansible_connection=winrm ansible_user=admin
mail ansible_host=Server3.company.com ansible_connection=ssh ansible_port=2222
web2 ansible_host=Server4.company.com ansible_connection=winrm ansible_password=P@ssword

localhost ansible_connection=localhost
```

> **Key Inventory Parameters**
>
> - `ansible_connection` -- ssh/ winrm/ localhost
> - `ansible_port` -- 22/ 5986
> - `ansible_user` -- root/ administrator
> - `ansible_ssh_pzxx` -- Password (SSH)
> - `ansible_password` --Password (Windows)
>
> **Note:** there are other Inventory parameters but these are key ones.  
> `ansible_port` by default is set to 22 for ssh connections.

### Demo

in order to be able to get connected to remote machine by using ansible, it is needed to get connected through the controller server via ssh (to confirm the key fingerprint).

```console
[osboxes@ansible-controller ~]$ ssh 192.168.0.39
The authenticity of host '192.168.0.39 (192.168.0.39)' can't be established.
ECDSA key fingerprint is SHA256:SNfc4a30IViNzKt/h++aMvWJyzj0k7EP8rvuUdSiTVU.
ECDSA key fingerprint is MD5:68:5c:12:2b:88:99:7e:38:bf:bb:09:e3:77:5d:80:b7.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.0.39' (ECDSA) to the list of known hosts.
osboxes@192.168.0.39's password:

Last login: Fri Aug 24 17:05:30 2018 from 192.168.0.19
[osboxes@ansible-target-1 ~]$ exit
logout
Connection to 192.168.0.39 closed.
```

- Example 1:

```console
[osboxes@ansible-controller section_04_test-project]$ cat > inventory.txt
target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
^C

[osboxes@ansible-controller section_04_test-project]$ ls -all
total 4
drwxrwxr-x. 2 osboxes osboxes 27 Aug 24 18:17 .
drwxrwxr-x. 3 osboxes osboxes 37 Aug 24 18:16 ..
-rw-rw-r--. 1 osboxes osboxes 63 Aug 24 18:18 inventory.txt

[osboxes@ansible-controller section_04_test-project]$ cat inventory.txt
target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org

[osboxes@ansible-controller section_04_test-project]$ ansible target1 -m ping -i inventory.txt
target1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

[osboxes@ansible-controller section_04_test-project]$
```

Ejemplo 2:

```console
[osboxes@ansible-controller section_04_test-project]$ vi inventory.txt
[osboxes@ansible-controller section_04_test-project]$ cat inventory.txt
target1 ansible_host=192.168.0.39 ansible_ssh_pass=osboxes.org
target2 ansible_host=192.168.0.40 ansible_ssh_pass=osboxes.org

[osboxes@ansible-controller section_04_test-project]$ ansible target1 -m ping -i inventory.txt
target1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[osboxes@ansible-controller section_04_test-project]$ ansible target2 -m ping -i inventory.txt
target2 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
[osboxes@ansible-controller section_04_test-project]$



[osboxes@ansible-controller section_04_test-project]$
```

> **NOTE:** target2 failed becuse there was not created the fingerprint as we did with the target1.  
> In order to solve this issue you can connect to the target2 via ssh to manually generate the fingerprint or you can disable the **Hostkey checking** (not recommended).  
> In order to disble the **Hotkey checking**, you should edit the `/etc/ansible/ansible.cfg` and set `host_key_cheking` parameter to `False`.
>
> ```console
> [osboxes@ansible-controller section_04_test-project]$ sudo nano /etc/ansible/ansible.cfg
> [osboxes@ansible-controller section_04_test-project]$ ansible target2 -m ping -i inventory.txt
> target2 | SUCCESS => {
>     "changed": false,
>     "ping": "pong"
> }
> [osboxes@ansible-controller section_04_test-project]$
> ```
