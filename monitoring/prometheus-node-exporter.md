# Infrastructure Monitoring – Prometheus & Node Exporter

## Objective

Introduce observability into the multi-node infrastructure
by deploying Prometheus and Node Exporter
to monitor system health across all servers.

This transforms the lab from "deploy and manage"
to "deploy, manage, and observe."

---

## Initial State

- Multi-node SSH access configured
- Bastion-based segmentation implemented
- Ansible automation active
- No centralized performance visibility
- No CPU, memory, or disk monitoring

Infrastructure was operational but not observable.

---

## Monitoring Architecture

vm1  ─┐
vm2  ─┼── Node Exporter → Prometheus (running on Bastion)
vm3  ─┘

Prometheus scrapes metrics from each node over port 9100.

---

## Phase 1 – Install Node Exporter on Targets

On each target node:

sudo apt install prometheus-node-exporter -y

Verify service:

sudo systemctl status prometheus-node-exporter

Confirm port listening:

ss -tulnp | grep 9100

---

## Phase 2 – Install Prometheus on Bastion

sudo apt install prometheus -y

Verify service:

sudo systemctl status prometheus

---

## Phase 3 – Configure Prometheus Targets

Edit configuration:

sudo nano /etc/prometheus/prometheus.yml

Add under scrape_configs:

- job_name: "linux_nodes"
  static_configs:
    - targets:
        - "vm1:9100"
        - "vm2:9100"
        - "vm3:9100"

Restart Prometheus:

sudo systemctl restart prometheus

---

## Phase 4 – Validate Metrics

Access Prometheus UI:

http://<bastion-ip>:9090

Test query:

node_cpu_seconds_total

Confirm metrics visible for all nodes.

---

## Issues Encountered

- Firewall blocking port 9100
- Hostname resolution issues
- Prometheus config indentation errors
- Service restart required after config change

---

## Mitigation

Allowed port 9100 internally:

sudo ufw allow from 10.0.2.0/24 to any port 9100

Validated scrape targets in Prometheus UI → Status → Targets

Checked logs:

journalctl -u prometheus

---

## Result

- CPU, memory, disk, and load metrics visible for all nodes
- Centralized monitoring operational
- Infrastructure observable in real-time
- Multi-node health validation simplified
- Foundation ready for alerting integration

The lab now reflects full lifecycle infrastructure:

Design → Secure → Automate → Observe
