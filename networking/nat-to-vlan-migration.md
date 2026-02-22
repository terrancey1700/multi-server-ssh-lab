# Network Mode Migration – NAT to Emulated VLAN

## Objective

Reconfigure multiple Ubuntu virtual machines from NAT mode
to a shared Emulated VLAN network in UTM
to enable internal VM-to-VM communication.

---

## Initial State

- All VMs configured in NAT mode
- Internet access functional
- No internal communication between VMs
- SSH inaccessible between nodes
- No shared broadcast domain

---

## Migration Strategy

1. Power off all virtual machines:

   sudo poweroff

2. In UTM:

   - Select VM → Edit → Network
   - Change Network Mode from NAT to Emulated VLAN
   - Confirm device is virtio-net-pci
   - Save configuration

3. Start all VMs.

4. Validate IP reassignment:

   ip a

5. Confirm shared subnet (example: 10.0.2.0/24).

6. Validate connectivity:

   ping -c 2 <target-ip>

---

## Issues Encountered

- IP addresses changed after network reconfiguration
- One VM booted with stale DHCP lease
- Incorrect assumption that NAT allows internal routing
- Initial confusion between Layer 2 and Layer 3 networking

---

## Solutions

- Revalidated IP addresses using ip a
- Restarted networking service when necessary
- Confirmed subnet alignment across all nodes
- Re-tested connectivity using ICMP
- Documented Layer 2 broadcast domain behavior

---

## Result

- All VMs now share the same private subnet
- ICMP communication successful between nodes
- SSH connectivity possible internally
- Environment now mimics internal LAN behavior
- Foundation established for multi-node automation
