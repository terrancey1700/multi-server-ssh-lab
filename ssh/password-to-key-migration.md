# SSH Authentication Migration – Password to Key-Based Access

## Objective

Migrate all target nodes from password-based SSH authentication
to key-based authentication to improve security and automation readiness.

---

## Initial State

- SSH accessible via username + password
- Password authentication enabled in sshd_config
- No SSH key pairs configured
- Environment vulnerable to brute-force attempts
- Not automation-friendly (Ansible requires key-based access)

---

## Migration Strategy

### 1. Generate SSH Key on Controller

ssh-keygen -t ed25519 -C "multi-node-access"

Explanation:
- ed25519 → modern secure algorithm
- Generates private key (~/.ssh/id_ed25519)
- Generates public key (~/.ssh/id_ed25519.pub)

---

### 2. Copy Public Key to Target Nodes

ssh-copy-id user@vm1
ssh-copy-id user@vm2
ssh-copy-id user@vm3

Alternative (manual method):

cat ~/.ssh/id_ed25519.pub | ssh user@vm1 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

---

### 3. Verify Key-Based Access

ssh user@vm1

Expected behavior:
- No password prompt
- Direct login using key

---

### 4. Disable Password Authentication

Edit SSH daemon configuration on each target:

sudo nano /etc/ssh/sshd_config

Set:

PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no

Save and restart SSH:

sudo systemctl restart ssh

---

### 5. Validate Configuration

Attempt login:

ssh user@vm1

Expected:
- Key login works
- Password login rejected

Check service status:

sudo systemctl status ssh

---

## Issues Encountered

- Incorrect file permissions on ~/.ssh directory
- authorized_keys readable by group
- Locked out after disabling password authentication prematurely

---

## Solutions

Corrected permissions:

chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

Tested key login before disabling password authentication.

Maintained secondary session open during configuration changes.

---

## Result

- All nodes now enforce key-only SSH authentication
- Password login disabled
- Brute-force risk significantly reduced
- Environment ready for Ansible automation
- Secure multi-node architecture established
