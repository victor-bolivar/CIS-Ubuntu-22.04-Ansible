


Ansible CIS Ubuntu 22.04 LTS Hardening V1.1.0 Latest [![Build Status](https://travis-ci.com/alivx/CIS-Ubuntu-22.04-Ansible.svg?branch=master)](https://travis-ci.com/alivx/CIS-Ubuntu-22.04-Ansible)
=========

CIS hardened Ubuntu: cyber attack and malware prevention for mission-critical systems
CIS benchmarks lock down your systems by removing:
1. non-secure programs.
2. disabling unused filesystems.
3. disabling unnecessary ports or services.
4. auditing privileged operations.
5. restricting administrative privileges.


CIS benchmark recommendations are adopted in virtual machines in public and private clouds. They are also used to secure on-premises deployments. For some industries, hardening a system against a publicly known standard is a criteria auditors look for. CIS benchmarks are often a system hardening choice recommended by auditors for industries requiring PCI-DSS and HIPPA compliance, such as banking, telecommunications and healthcare.
If you are attempting to obtain compliance against an industry-accepted security standard, like PCI DSS, APRA or ISO 27001, then you need to demonstrate that you have applied documented hardening standards against all systems within the scope of assessment.


The Ubuntu CIS benchmarks are organised into different profiles, namely **‘Level 1’** and **‘Level 2’** intended for server and workstation environments.


**A Level 1 profile** is intended to be a practical and prudent way to secure a system without too much performance impact.
* Disabling unneeded filesystems,
* Restricting user permissions to files and directories,
* Disabling unneeded services.
* Configuring network firewalls.

**A Level 2 profile** is used where security is considered very important and it may have a negative impact on the performance of the system.

* Creating separate partitions,
* Auditing privileged operations

The Ubuntu CIS hardening tool allows you to select the desired level of hardening against a profile (Level1 or Level 2) and the work environment (server or workstation) for a system.
Exmaple:
```Bash
ansible-playbook -i inventory cis-ubuntu-22.yaml --tags="level_1_server"
```
You can list all tags by running the below command:
```Bash
ansible-playbook -i host run.yaml --list-tags
```


The roles were written based on:
```Text
CIS Ubuntu Linux 22.04 LTS Benchmark
v2.0.0 - 03-28-2024
```


**Check Example dir**
_________________


Requirements
------------

You should carefully read through the tasks to make sure these changes will not break your systems before running this playbook.

You can download Free CIS Benchmark book from this URL
[Free Benchmark](https://learn.cisecurity.org/benchmarks)


To start working in this Role you just need to install Ansible.
[Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)


_________________

Role Variables
--------------

You have to review all default configuration before running this playbook, There are many role variables defined in defaults/main.yml.

* If you are considering applying this role to any servers, you should have a basic familiarity with the CIS Benchmark and an appreciation for the impact that it may have on a system.
* Read and change configurable default values.

```

If you need you to change file templates, you can find it under `files/templates/*`


_________________

Dependencies
------------

* Ansible version > 2.9

_________________

Example Playbook
----------------

Below an example of a playbook

```Yaml
---
- hosts: host1
  become: yes
  remote_user: root
  gather_facts: no
  roles:
    - { role: "CIS-Ubuntu-22.04-Ansible",}
```

### Run all
If you want to run all tags use the below command:
```Bash
ansible-playbook -i [inventoryfile] [playbook].yaml
```
### Run specfic section
```Bash
ansible-playbook -i host run.yaml -t section2
```
### Run multi sections
```Bash
ansible-playbook -i host run.yaml -t section2 -t 6.1.1
```
* Note:
When run an individual task be sure from the dependencies between tasks, for example, if you run tag **4.1.1.2 Ensure auditd service is enabled** before running **4.1.1.1 Ensure auditd is installed** you will get an error at the run time.

* make sure to select one time service, for me I use ntp, but you can use other service such as [`systemd-timesyncd`,`ntp`,`chrony`] under the settings `defaults/main.yaml`
> Testing
> 10/07/2024 Tested on Openstack Ubuntu 22.04 LTS server [Pass]

_________________
## Troubleshooting
* If you want to run the playbook in the same machine, make sure to add this to run task:
```
- hosts: 127.0.0.1
  connection: local
```
* if you faced issue with execut, try to run the playbook in another path, like `/srv/`.
* For error like this `stderr: chage: user 'ubuntu' does not exist in /etc/passwd`, make sure to update config under `CIS-Ubuntu-22.04-Ansible/defaults/main.yml`


```Bash
TASK [CIS-Ubuntu-22.04-Ansible : 1.4.1 Ensure AIDE is installed] ***********************************************************************************************************************************************************************************************************fatal: [192.168.80.129]: FAILED! => {"cache_update_time": 1611229159, "cache_updated": false, "changed": false, "msg": "'/usr/bin/apt-get -y -o \"Dpkg::Options::=--force-confdef\" -o \"Dpkg::Options::=--force-confold\"      install 'nullmailer' 'aide-common' 'aide' -o APT::Install-Recommends=no' failed: E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 5194 (unattended-upgr)\nE: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?\n", "rc": 100, "stderr": "E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 5194 (unattended-upgr)\nE: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?\n", "stderr_lines": ["E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 5194 (unattended-upgr)", "E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?"], "stdout": "", "stdout_lines": []}
```
* For the above error you need to make sure there is not apt process running in the background, or you must wait until apt finish the process.

```Bash
TASK [CIS-Ubuntu-22.04-Ansible : 5.4.1.1 Ensure password expiration is 365 days or less | chage] ***************************************************************************************************************************************************************************failed: [192.168.80.129] (item=ubuntu) => {"ansible_loop_var": "item", "changed": true, "cmd": ["chage", "--maxdays", "300", "ubuntu"], "delta": "0:00:00.005478", "end": "2021-01-21 12:49:45.463615", "item": "ubuntu", "msg": "non-zero return code", "rc": 1, "start": "2021-01-21 12:49:45.458137", "stderr": "chage: user 'ubuntu' does not exist in /etc/passwd", "stderr_lines": ["chage: user 'ubuntu' does not exist in /etc/passwd"], "stdout": "", "stdout_lines": []}
```
* Make sure you set the right user under defaults/main.yaml


```

TASK [CIS-Ubuntu-22.04-Ansible : Creating users without admin access] ***************************************************************************************************************
fatal: [golden]: FAILED! => {"msg": "crypt.crypt not supported on Mac OS X/Darwin, install passlib python module"}
```

Install `pip install passlib`

_________________


License
-------

 GNU GENERAL PUBLIC LICENSE

Author Information
------------------

The role was originally developed by [Ali Saleh Baker](https://www.linkedin.com/in/alivx/) and upgraded to the latest CIS v2.0.0 for Ubuntu 22.04 by [Victor Bolivar](https://www.linkedin.com/in/victor-bolivar-de-la-cruz-644b2814b/)
When contributing to this repository, please first discuss the change you wish to make via a GitHub issue,  email, or via other channels with me :)
