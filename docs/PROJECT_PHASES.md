# Project Phases – Multi-Server SSH Lab

This document outlines the structured phases used to build the lab environment.

---

## Phase 1 – VM Creation

- Cloned base Ubuntu VM
- Created:
  - terrance-devops-machine (controller)
  - vm1
  - vm2
  - vm3

---

## Phase 2 – Network Reconfiguration

- Changed all VMs from NAT to Emulated VLAN
- Verified IP addresses using ip a
- Confirmed shared subnet (10.0.2.0/24)

---

## Phase 3 – Network Validation

- Tested connectivity using ping
- Confirmed ICMP success between nodes

---

## Phase 4 – SSH Deployment

- Installed openssh-server
- Enabled service using systemctl
- Verified active (running) state

---

## Phase 5 – Firewall Validation

- Allowed SSH through UFW
- Reloaded firewall rules
- Confirmed access via ssh user@host

---

## Phase 6 – Hostname Resolution

- Edited /etc/hosts on controller
- Added entries for vm1, vm2, vm3
- Confirmed SSH using hostname instead of IP

---

## Final State

- Four VMs on shared subnet
- SSH functional across nodes
- Firewall properly configured
- Environment stable and reboot-safe
