# Infrastructure as Code – Terraform Multi-Node Provisioning

## Objective

Provision the multi-node lab environment using Terraform
instead of manually cloning and configuring virtual machines.

This ensures reproducibility, version control, and environment consistency.

---

## Initial State

- VMs manually cloned in UTM
- Network configuration performed manually
- No infrastructure reproducibility
- Environment rebuild requires manual repetition

Manual provisioning does not scale.

---

## Target State

Terraform-defined infrastructure:

- Bastion host
- 3 target nodes
- Shared network configuration
- Repeatable environment deployment

Infrastructure defined as code.

---

## Strategy

Terraform will define:

- Compute instances
- Network configuration
- SSH key injection
- Base provisioning

---

## Example Structure

terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── provider.tf

---

## Example provider.tf

```hcl
provider "libvirt" {
  uri = "qemu:///system"
}
