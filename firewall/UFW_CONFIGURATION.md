# UFW Configuration – SSH Access Control

## Objective

Allow secure SSH access to target nodes while ensuring the firewall does not block port 22.

---

## Initial State

- SSH installed and running
- Connection attempts resulted in timeout
- Firewall suspected of blocking traffic

---

## Configuration Steps

### 1. Allow SSH

sudo ufw allow ssh

Explanation:
- Opens TCP port 22
- Uses predefined SSH profile

---

### 2. Reload Firewall Rules

sudo ufw reload

Explanation:
- Applies rule changes immediately

---

### 3. Verify Rules

sudo ufw status

Expected Output:

Status: active

22/tcp   ALLOW   Anywhere

---

## Common Firewall-Related Failures

Issue:
SSH times out

Possible Causes:
- UFW enabled but SSH not allowed
- Firewall rules not reloaded
- Wrong IP address used

---

## Result

- Port 22 accessible internally
- SSH connections successful
- Firewall rules persist across reboot
- Network and service layers aligned
