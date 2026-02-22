# Troubleshooting Guide – Multi-Server SSH Lab

This document outlines real issues encountered during implementation and how they were resolved.

---

## Layered Troubleshooting Model

1. Is the VM running?
2. Is the network mode correct?
3. Are IP addresses correct?
4. Does ping work?
5. Is SSH service running?
6. Is port 22 allowed through firewall?

Always troubleshoot from lowest layer upward.

---

## NAT vs VLAN Confusion

Issue:
VMs could access the internet but not communicate with each other.

Root Cause:
NAT mode isolates internal VM-to-VM communication.

Resolution:
Switched all VMs to Emulated VLAN mode.

Lesson:
Understand the difference between outbound NAT and shared Layer 2 networks.

---

## “No Route to Host”

Issue:
SSH failed even though IP seemed correct.

Root Causes:
- VM powered off
- Network mode mismatch
- Incorrect IP assumption

Resolution:
Validated IPs using:

ip a

Confirmed subnet alignment and VM state.

---

## “Connection Refused”

Issue:
Ping worked but SSH failed.

Root Cause:
SSH daemon not installed or not running.

Resolution:
Installed and enabled OpenSSH:

sudo apt install openssh-server -y
sudo systemctl enable --now ssh

Lesson:
Network connectivity does not guarantee service availability.

---

## Firewall Blocking

Issue:
SSH installed but connection timed out.

Resolution:
Allowed SSH through UFW:

sudo ufw allow ssh
sudo ufw reload

Lesson:
Always include firewall checks in debugging flow.

---

## Result

All major network and SSH failures were identified and resolved using structured, layered troubleshooting.
