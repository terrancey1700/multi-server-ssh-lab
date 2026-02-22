# Multi-Server SSH Lab
## Virtual Network & Secure Remote Access in UTM

End-to-end multi-VM networking and SSH deployment using UTM virtualization. This project simulates a small internal network where one controller node securely manages multiple Linux servers over a shared virtual LAN.

---

# Executive Summary (STAR Method)

## Situation

I needed a controlled environment to practice real-world networking and remote server administration without relying on cloud infrastructure. The goal was to simulate multiple machines communicating over a private subnet and establish secure remote access between them.

Initial state:

- Single standalone VM
- Default NAT networking
- No inter-VM communication
- No remote management architecture

This limited the ability to practice infrastructure-level troubleshooting and multi-host administration.

---

## Task

Design and deploy a mini internal network that:

- Connects four Linux VMs on the same virtual subnet
- Enables inter-machine communication (ICMP validation)
- Implements secure SSH-based remote management
- Survives reboot and configuration changes
- Mimics real internal LAN architecture

---

## Action

### Phase 1 — Network Architecture Design

Created:

- Controller VM: `terrance-devops-machine`
- Target VMs:
  - `vm1`
  - `vm2`
  - `vm3`

Reconfigured all VMs from **NAT** to **Emulated VLAN** mode in UTM to ensure they shared the same Layer 2 broadcast domain.

## Validated IP assignments:

ip a
- Explanation:
  - `ip — network configuration utility`
  - `a — show all interfaces and assigned addresses`

All VMs were confirmed to be on the same subnet (example: 10.0.2.0/24).

--- 

### Phase 2 — Network Validation

Tested connectivity from controller to each target:

ping -c 2 10.0.2.16

- Explanation:

  - `ping — sends ICMP echo requests`

  - `-c 2 — send exactly 2 packets1`

Successful replies confirmed:

- `Shared subnet routing`
- `Proper network mode configuration`
- `No host-level firewall blocking ICMP`

---

### Phase 3 — SSH Service Deployment

Installed and enabled SSH server on all target nodes:

- `sudo apt update`
- `sudo apt install openssh-server -y`
- `sudo systemctl enable --now ssh`

Explanation:

- `apt update — refresh package index`

- `apt install openssh-server — install SSH daemon`

- `systemctl enable --now ssh — start service immediately and enable on boot`

Verified service state:

- `sudo systemctl status ssh`

Expected output:

- `active (running)`

---
  
### Phase 4 — Firewall Validation

If UFW was enabled:

- `sudo ufw allow ssh`
- `sudo ufw reload`

Explanation:

- `Allows inbound TCP port 22`
- `Reloads firewall rules`

---

### Phase 5 — Remote Command Execution

From the controller:

- `ssh terrancey1@10.0.2.16 uptime`

Explanation:

- `ssh user@host command:`

  - `Executes command remotely without entering interactive shell`
  - `Validates network, authentication, and service availability`
  - `Successful execution confirmed end-to-end functionality`

---

### Phase 6 — Hostname Resolution Improvement

Edited /etc/hosts on the controller:

- `sudo nano /etc/hosts`

Added:

- `10.0.2.16 vm1`
- `10.0.2.17 vm2`
- `10.0.2.18 vm3`

Now accessible via:

- `ssh terrancey1@vm1:`

  - `This mimics small-office DNS resolution behavior.`

---

## Architecture Overview

### All nodes:

- `Ubuntu Linux`
- `Same private subnet`
- `SSH enabled`
- `ICMP reachable`
- `No external exposure`

---

## Problems Encountered & Resolutions
### NAT vs VLAN Confusion

Issue:
- VMs could access the internet but not communicate with each other.

Root Cause:
- NAT isolates internal traffic and prioritizes outbound access.

Resolution:
- Switched to Emulated VLAN so all VMs share the same broadcast domain.

Lesson:
- Understand the difference between outbound NAT and Layer 2 networking.

### “No Route to Host”

Issue:
 - `SSH failed despite correct IP.`

Root Causes:
- `VM powered off`
- `Network mode mismatch`
- `Incorrect IP assumption`

Resolution:
- `Validated using ip a and confirmed subnet alignment.`

Lesson:
- `Troubleshoot layer-by-layer (network → service → authentication).`

### “Connection Refused”

Issue:
 - `Ping worked but SSH failed.`

Root Cause:
- `SSH daemon not installed or not running.`

Resolution:
- `Installed openssh-server and enabled via systemd.`

Lesson:
- `Network success does not imply service availability.`

### Firewall Blocking

Issue:
- `SSH installed but connection timed out.`

Resolution:
- `Allowed SSH through UFW.`

Lesson:
- `Always consider host-based firewalls in troubleshooting flow.`

### Results

- `After completion:`
- `Four VMs operate on the same subnet`
- `Controller can execute remote commands across all targets`
- `SSH survives reboot`
- `Firewall rules persist`
- `Lab environment ready for automation expansion`

### Skills Demonstrated

- `Linux system administration`
- `Virtual networking architecture`
- `Subnet validation`
- `SSH deployment and troubleshooting`
- `Firewall configuration (UFW)`
- `Service management (systemd)`
- `Layered debugging methodology`
- `Internal DNS simulation via /etc/hosts`

---

## Future Improvements

- `SSH key-based authentication`
- `Disable password authentication`
- `Harden SSH configuration`
- `Implement Fail2Ban`
- `Centralized logging`
- `Ansible-based multi-host automation`
- `Multi-node configuration management lab`

---

## Author
Terrance Young
Aspiring DevOps / Systems Engineer
