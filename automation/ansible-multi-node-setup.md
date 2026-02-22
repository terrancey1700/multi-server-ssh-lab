# Multi-Node Automation – Ansible Over Bastion

## Objective

Automate management of multiple Linux nodes using Ansible,
leveraging the Bastion host as the control node.

This removes manual SSH operations
and introduces Infrastructure as Code principles.

---

## Initial State

- SSH key-based authentication configured
- Bastion host controlling access
- Manual command execution across nodes
- No centralized configuration management

While secure, administration was still manual.

---

## Automation Strategy

Bastion host becomes:

Ansible Control Node

Targets:

vm1  
vm2  
vm3  

All communication uses SSH key authentication.

---

## 1. Install Ansible on Bastion

sudo apt update
sudo apt install ansible -y

Verify:

ansible --version

---

## 2. Create Inventory File

nano inventory.ini

Add:

[webservers]
vm1
vm2
vm3

---

## 3. Test Connectivity

ansible -i inventory.ini webservers -m ping

Expected:

vm1 | SUCCESS
vm2 | SUCCESS
vm3 | SUCCESS

This confirms:
- SSH connectivity
- Python installed on targets
- Proper authentication

---

## 4. Execute Remote Commands

Example:

ansible -i inventory.ini webservers -a "uptime"

This executes across all nodes simultaneously.

---

## 5. Create Simple Playbook

nano update.yml

Add:

---
- name: Update all servers
  hosts: webservers
  become: yes

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Upgrade packages
      apt:
        upgrade: dist

Run:

ansible-playbook -i inventory.ini update.yml

---

## Issues Encountered

- Python missing on minimal installs
- Incorrect SSH user configuration
- Inventory hostname resolution issues
- Bastion SSH agent not forwarding properly

---

## Mitigation

Installed Python if missing:

sudo apt install python3 -y

Verified SSH user matches target system user.

Ensured /etc/hosts resolution aligned with inventory names.

Validated SSH key access before running playbooks.

---

## Result

- Multi-node updates executed simultaneously
- Manual SSH eliminated
- Infrastructure now automation-ready
- Configuration management introduced
- Foundation laid for CI/CD integration

The environment now transitions from
manual system administration
to automated infrastructure management.
