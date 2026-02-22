# SSH Intrusion Protection – Fail2Ban Implementation

## Objective

Protect SSH-enabled infrastructure from brute-force login attempts
by implementing Fail2Ban to monitor authentication logs
and automatically block malicious IP addresses.

---

## Initial State

- SSH key-only authentication enabled
- Bastion host architecture implemented
- Firewall restricting SSH access
- No automated intrusion detection mechanism

While password authentication was disabled,
monitoring and response automation were not yet implemented.

---

## Implementation Strategy

### 1. Install Fail2Ban

On Bastion host:

sudo apt update
sudo apt install fail2ban -y

---

### 2. Verify Service Status

sudo systemctl status fail2ban

Expected:
active (running)

---

### 3. Create Local Jail Configuration

sudo nano /etc/fail2ban/jail.local

Add:

[sshd]
enabled = true
port = 22
logpath = /var/log/auth.log
maxretry = 5
bantime = 3600
findtime = 600

Explanation:

- maxretry = 5 → block after 5 failed attempts
- bantime = 3600 → block for 1 hour
- findtime = 600 → 10-minute window

---

### 4. Restart Service

sudo systemctl restart fail2ban

---

### 5. Validate Jail Status

sudo fail2ban-client status
sudo fail2ban-client status sshd

---

## Testing

Simulated failed login attempts from a test node.

Confirmed:

- IP added to ban list
- SSH blocked for defined bantime

---

## Issues Encountered

- Incorrect log path (auth.log vs journalctl-based systems)
- Service restart forgotten after config change
- Firewall rules conflicting with ban rules

---

## Mitigation

Verified correct log file location:

sudo tail -f /var/log/auth.log

Ensured systemd service restart after config updates.

Confirmed active jail with:

sudo fail2ban-client status sshd

---

## Result

- Automated SSH intrusion protection active
- Brute-force attempts automatically banned
- Security posture strengthened
- Reduced manual monitoring requirements
- Infrastructure hardened against common attack vectors
