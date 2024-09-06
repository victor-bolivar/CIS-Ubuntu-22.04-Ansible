# ChangeLog for Ansible CIS Ubuntu 22.04 LTS Hardening V2.0.0
All noteworthy changes should be documented in this file.

## 1.0 (WIP)
- Bugfix: Fixes tasks in sections 1.1.1,4.1,5.3,5.4 and handlers [[GH-3](https://github.com/alivx/CIS-Ubuntu-22.04-Ansible/pull/3)].
- Bugfix: Fixes procedure for the creation of the AIDE database in task 6.1.1 [[GH-4](https://github.com/alivx/CIS-Ubuntu-22.04-Ansible/pull/4)].
- Feature: Adds use of Ansible tasks for 5.4.2.5 instead of the script provided by the CIS [[GH-5](https://github.com/alivx/CIS-Ubuntu-22.04-Ansible/pull/5)].
- Feature: Allows to individually select which apt packages to purge [[GH-6](https://github.com/alivx/CIS-Ubuntu-22.04-Ansible/pull/6)].
- Feature: Defines a list of users to be added to the group with `su` command access. [[GH-7](https://github.com/alivx/CIS-Ubuntu-22.04-Ansible/pull/7)].