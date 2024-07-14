# Extreme Beginner Friendly Ansible Lab

## Descriptions:

From here, we can learn how to build the ansible lab with the clients. I build the ansibleMaster and I have cloned that as 5 clients such as Po84, Ra88, Pu94, Fr87, and Mc115 and modified the necessary settings in the `/etc/hosts` and 
the `ansible.cfg`.

## Host Hardware Requirements:

1. **RAM:** Minimum 6GB of RAM
2. **HDD:** Minimum 80GB HDD
3. **Processor:** Latest processors released after 2018, including the latest Pentium or Athlon. I recommend Intel i3 or AMD's Ryzen 3.

## Host OS Requirements:

1. Any OS (Linux, macOS, or even Windows) is fine. However, if you have low resources, stick with lightweight XFCE/MATE/LXDE or Cinnamon-based distros such as Linux Mint or Peppermint. 
   - **Recommendation:** Linux Mint (XFCE)

## Virtualization Software & OS used here:

**MAKE SURE THAT VIRTUALIZATION TECHNOLOGY IS ENABLED IN BIOS**

- **OS:** AlmaLinux 9.4 Minimal ISO x86_64/amd64
- **Ansible:** 2.14.14
- **Python:** 3.9.18
- **VirtualBox:** 7
- **Virtual VM RAM:** 768MB (if the ISO is minimal ISO)
- **Virtual VM CPU:** 1 CPU (if the ISO is minimal ISO)
- **Virtual VM DISK:** 8GB (if the ISO is minimal ISO)

## Building Master:

Download the minimal ISO from the AlmaLinux official website and see this YouTube link on how to install the AlmaLinux minimal ISO.

`Link=`

1. Note the root user password and uncheck the lock option.
2. Check the SSH option to allow root user.
3. Create an Administrator account (Here, `marieCurie`) and set the password. If that user is an Administrator, it will be added to the `wheel` group automatically.
   - **Note:** This user will be the ansible automation user.
4. Uncomment the line `%wheel ALL=(ALL) NOPASSWD: ALL` in the `/etc/sudoers` file so that users in the `wheel` group will not be prompted for a password when they perform sudo commands.
5. Check the syntax by the command `sudo visudo -c`. Make sure you get the parsed OK. If there is any syntax error, correct it before the session expires. If it expires, you will not be able to log in as root and will have to boot in single user mode to troubleshoot.
7. Make sure the Ansible user's account and password expiry is set to never, which is the best practice for the Ansible Admin User.

```
[marieCurie@ansibleMaster ~]$ id marieCurie
uid=1000(marieCurie) gid=1000(marieCurie) groups=1000(marieCurie),10(wheel)
[marieCurie@ansibleMaster ~]$ sudo grep wheel /etc/sudoers
## Allows people in group wheel to run all commands
%wheel	ALL=(ALL)	ALL
%wheel	ALL=(ALL)	NOPASSWD: ALL
[marieCurie@ansibleMaster ~]$ sudo visudo -c
/etc/sudoers: parsed OK
[marieCurie@ansibleMaster ~]$ chage -l marieCurie
Last password change					: never
Password expires					: never
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
[marieCurie@ansibleMaster ~]$ date
Sat Jul 13 20:49:44 IST 2024
[marieCurie@ansibleMaster ~]$ 
```

## 7. Installing Ansible & Setting up:

Once you have logged in with the Admin account, run the below command to install Ansible, vim, and nano:

```bash
sudo yum install platform-python* ansible* vim* nano*
```
## Creating the Ansible Directory:

```bash
mkdir -p ansible/roles
cd ansible
touch zinventory
touch ansible.cfg
```
8. Power off the VM & clone this VM as ansibleMaster and 5 clients.
9. Rename the clients with respective names like Po84, Ra88, Pu94, Fr87, and Mc115. Power on the clients and rename the hostnames accordingly. Note the IPs of the clients.
10. Power on the ansibleMaster, change the hostname to ansibleMaster, and add the clients' IP details in the Master's ```/etc/hosts``` as shown below:
```bash
[marieCurie@ansibleMaster ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

######################### AnsibleScientificLab ####################

192.168.1.20 Po84 P84
192.168.1.21 Ra88 R88
192.168.1.22 Pu94 P94
192.168.1.23 Fr87 F87
192.168.1.24 Mc115 M115

######################### AnsibleScientificLab ####################
[marieCurie@ansibleMaster ~]$ date
Sat Jul 13 13:15:04 IST 2024
[marieCurie@ansibleMaster ~]$
```
11. Set the ansible.cfg in the ansible directory. ```7th step``` we could see we have created one empty ansible.cfg file (on the ansibleMaster):
```bash
cd ansible
```
Add the below entries to the ansible.cfg:
```
[defaults]
inventory=./zinventory
role_path=./roles
become=true
remote_user=marieCurie

[privilege_escalation]
become=true
become_method=sudo
become_user=root
become_ask_pass=false
```
Example:
```
[marieCurie@ansibleMaster ansible]$ cat ansible.cfg
[defaults]
inventory=./zinventory
role_path=./roles
become=true
remote_user=marieCurie

[privilege_escalation]
become=true
become_method=sudo
become_user=root
become_ask_pass=false
[marieCurie@ansibleMaster ansible]$ date
Sat Jul 13 13:16:34 IST 2024
[marieCurie@ansibleMaster ansible]$
```
12. Inventory file example (on the ansibleMaster):
```
[Dev]
Po84

[Test]
Ra88

[Prod]
Pu94
Fr87

[Dr]
Mc115

[Web]
Pu94
Fr87
```
Example:
```
[marieCurie@ansibleMaster ansible]$ cat zinventory
[Dev]
Po84

[Test]
Ra88

[Prod]
Pu94
Fr87

[Dr]
Mc115

[Web]
Pu94
Fr87
[marieCurie@ansibleMaster ansible]$ date
Sat Jul 13 13:18:17 IST 2024
[marieCurie@ansibleMaster ansible]$
```
13. `.vimrc` file contents:
Add below entries in .vimrc file, so the code will be clear
```
set ai
set et
set ts=2
set cursorcolumn
```
Example:
```[marieCurie@ansibleMaster ansible]$ cat ~/.vimrc
set ai
set et
set ts=2
set cursorcolumn
[marieCurie@ansibleMaster ansible]$ date
Sat Jul 13 13:19:27 IST 2024
[marieCurie@ansibleMaster ansible]$
```
14. Create an SSH key for the ansible user "marieCurie" and map it to the clients under the ansible user "marieCurie". Refer to this link for detailed instructions.

To generate the ssh-key by the command `ssh-keygen`

To copy the ssh oub key to the rargets by the below commands
```
   ssh-copy-id marieCurie@P84
   ssh-copy-id marieCurie@R88
   ssh-copy-id P94
   ssh-copy-id F87
   ssh-copy-id M115

```
Note: Above commands, for the last two nodes I haven't used the username. System will identify automatically by the source user i.e `marieCurie`. We know that ansible user 
already present in the all nodes, right.

To check the ansible setup, run the following command from the ansible directory:

```bash
cd ~/ansible/
ansible all -m ping
```
Example:

```bash
[marieCurie@ansibleMaster ansible]$ ansible all -m ping
Pu94 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
Ra88 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
Po84 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
Fr87 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
Mc115 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[marieCurie@ansibleMaster ansible]$ date
Sat Jul 13 15:08:21 IST 2024
[marieCurie@ansibleMaster ansible]$
```

Please find the Telegram link to download the cloned ansibleMaster virtualbox's ova file. This archived zip file also contains the credentials.

```Telegram: https://t.me/linuxium88/54 ```

Please find the sha256sum hash of the archived file 

```sha256sum: a5a60d77110dd04caeab8aa9bb8878cb694866208c4dce633c1a9d98b9abca3f  ansibleMaster.zip ```

100! times thank you very much 🙏.

Have a fun with this ☢️ `radioActive` lab with `marieCurie` 🤍 💙 🖤

