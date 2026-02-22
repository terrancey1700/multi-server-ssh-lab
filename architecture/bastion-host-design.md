# Bastion Host Architecture Design

## Objective

Introduce a Bastion (Jump Host) architecture to restrict direct SSH access
to internal nodes and simulate real-world production network segmentation.

---

## Initial State

- Controller VM directly SSHs into vm1, vm2, vm3
- All nodes on same subnet
- No segmentation between management and internal nodes
- Flat network topology

This model works for labs but does not reflect secure production environments.

---

## Target Architecture

Admin Machine → Bastion Host → Private Target Nodes

Internal nodes no longer accept SSH from all machines.
Only the Bastion host can access them.

---

## Architecture Diagram

Admin (Controller)
        │
        │ SSH (Port 22)
        ▼
Bastion Host
        │
        │ Internal SSH
        ▼
vm1   vm2   vm3

Subnet: 10.0.2.0/24
Access Control: Restricted via firewall rules

---

## Implementation Strategy

### 1. Designate Controller as Bastion Host

terrance-devops-machine becomes the only node allowed
to SSH into vm1, vm2, vm3.

---

### 2. Restrict SSH Access on Target Nodes

On each target:

Edit UFW rules:

sudo ufw delete allow ssh
sudo ufw allow from 10.0.2.15 to any port 22
sudo ufw reload

Explanation:
- Removes open SSH rule
- Allows only Bastion IP (example: 10.0.2.15)
- Blocks lateral movement from other nodes

---

### 3. Validate Access Control

From Bastion:

ssh user@vm1  → Success

From any other node:

ssh user@vm1  → Connection blocked

---

## Security Improvements

- Reduces attack surface
- Prevents unrestricted lateral SSH movement
- Simulates real production DMZ/internal segmentation
- Aligns with Zero Trust principles

---

## Issues Encountered

- Locked out when IP was misconfigured
- Firewall rule order confusion
- Forgot to validate Bastion IP before restricting rules

---

## Mitigation Strategy

- Keep secondary SSH session open during firewall changes
- Verify Bastion IP using ip a before applying rule
- Test before deleting broad SSH rules

---

## Result

- Only Bastion host can SSH into internal nodes
- Internal nodes no longer accept open SSH connections
- Infrastructure now reflects segmented network design
- Security posture significantly improved
- Foundation established for enterprise-style architecture
