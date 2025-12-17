# multi-server-ssh-lab
# Multi-Server Networking and SSH Setup in UTM

## Project overview

This project builds a small “mini network” on your computer using UTM virtual machines (VMs). The goal is to make one main VM (the controller) talk to three other VMs (targets) over the same private network, and then log into them securely using SSH.

### What you will end up with

* A controller VM named `terrance-devops-machine`
* Three target VMs named `vm1`, `vm2`, and `vm3`
* All four VMs on the same virtual LAN so they can **ping** each other
* SSH installed and running so the controller can **SSH** into each target
* Optional hostname shortcuts so you can type `ssh vm1` instead of `ssh 10.0.2.16`

---

## Step-by-step setup (why, how, what)

### 1) Create or clone the VMs

**Why this matters:**
You need multiple separate machines to practice real networking. Each VM behaves like its own computer.

**How it’s done:**
In UTM, duplicate (clone) an existing Linux VM.

**What to do to complete the step:**

1. Open UTM.
2. Right-click your Linux VM → **Duplicate**.
3. Rename the copies to `vm1`, `vm2`, and `vm3`.
4. You should now see these VMs listed:

   * `terrance-devops-machine`
   * `vm1`
   * `vm2`
   * `vm3`

---

### 2) Put all VMs on the same UTM network

**Why this matters:**
UTM often uses NAT by default. NAT lets a VM reach the internet, but it can block VM-to-VM communication. To make the VMs talk to each other, they must share the same virtual LAN.

**How it’s done:**
Switch each VM’s network mode to **Emulated VLAN** so they all live on the same internal network.

**What to do to complete the step:**

1. Shut down every VM (inside the VM):

   * `sudo poweroff`
2. In UTM, for **each** VM:

   * Select the VM → **Edit** → **Network**
   * Set **Network Mode** to `Emulated VLAN`
   * Confirm the network device is `virtio-net-pci`
3. Save and start all VMs.

**What success looks like:**
Each VM gets an IP address in the same subnet (for example `10.0.2.x`).

---

### 3) Check each VM’s IP address

**Why this matters:**
You need the IP addresses to test networking and to connect with SSH.

**How it’s done:**
Use `ip a` to list your network interfaces and addresses.

**What to do to complete the step:**

1. On each VM, run:

   * `ip a`
2. Find a line that looks like this:

   * `inet 10.0.2.16/24 ...`
3. Write down each VM’s address.

**Example mapping:**

* Controller: `terrance-devops-machine` → `10.0.2.15`
* Target 1: `vm1` → `10.0.2.16`
* Target 2: `vm2` → `10.0.2.17`
* Target 3: `vm3` → `10.0.2.18`

---

### 4) Test basic connectivity with ping

**Why this matters:**
SSH won’t work if the machines can’t even see each other. Ping is a fast way to check if the network path is working.

**How it’s done:**
Ping from the controller to each target VM.

**What to do to complete the step:**

1. From `terrance-devops-machine`, ping each VM:

   * `ping -c 2 10.0.2.16`
   * `ping -c 2 10.0.2.17`
   * `ping -c 2 10.0.2.18`

**What success looks like:**
You see replies like:

* `64 bytes from 10.0.2.16: icmp_seq=1 ttl=64 time=...`

**If it fails:**

* “Destination Host Unreachable” usually means wrong network settings, the VM is off, or a firewall rule is blocking traffic.

---

### 5) Install and start SSH on the target VMs

**Why this matters:**
Ping only proves the network works. SSH is what lets you log in to another machine securely and run commands remotely.

**How it’s done:**
Install `openssh-server`, then start and enable the service.

**What to do to complete the step (run on vm1, vm2, vm3):**

1. Update packages:

   * `sudo apt update`
2. Install the SSH server:

   * `sudo apt install openssh-server -y`
3. Enable and start SSH now and on reboot:

   * `sudo systemctl enable --now ssh`
4. Confirm it’s running:

   * `sudo systemctl status ssh`

**What success looks like:**
The status shows `active (running)`.

---

### 6) Allow SSH through the firewall (if needed)

**Why this matters:**
Even if SSH is installed, a firewall can block port 22 (the default SSH port).

**How it’s done:**
Use `ufw` to allow SSH connections.

**What to do to complete the step (run on vm1, vm2, vm3 if ufw is enabled):**

1. Allow SSH:

   * `sudo ufw allow ssh`
2. Reload firewall rules:

   * `sudo ufw reload`

---

### 7) SSH from the controller into each target

**Why this matters:**
This proves your lab works end-to-end: network is good, SSH is running, and logins work.

**How it’s done:**
From the controller VM, SSH into each target using username + IP address.

**What to do to complete the step:**
From `terrance-devops-machine`, run:

* `ssh terrancey1@10.0.2.16 uptime`
* `ssh terrancey1@10.0.2.17 uptime`
* `ssh terrancey1@10.0.2.18 uptime`

**What success looks like:**
You either get an uptime result, or it asks for a password (both mean SSH is reachable).

**Common failure messages:**

* `No route to host` → network mode mismatch, wrong IP, VM off
* `Connection refused` → SSH server not running
* `Permission denied (publickey)` → key-based login expected but key not set up

---

### 8) Optional: Add hostnames for easier access

**Why this matters:**
IP addresses are hard to remember. Hostnames let you type `ssh vm1` instead of `ssh 10.0.2.16`.

**How it’s done:**
Add name-to-IP entries in `/etc/hosts`.

**What to do to complete the step:**

1. On the controller VM, edit the hosts file:

   * `sudo nano /etc/hosts`
2. Add lines like these:

   * `10.0.2.15 terrance-devops-machine`
   * `10.0.2.16 vm1`
   * `10.0.2.17 vm2`
   * `10.0.2.18 vm3`
3. Save and exit.

**What success looks like:**

* `ping vm1`
* `ssh terrancey1@vm1`

---

## Pain points and what I struggled with

Here are the parts that were hardest during this build, plus what I learned while fixing them.

### 1) NAT vs “same network” confusion

* What went wrong: I expected VMs to talk to each other automatically, but NAT mode mostly focuses on outbound internet access.
* What fixed it: Switching every VM to **Emulated VLAN** so they share one virtual LAN.

### 2) “No route to host” errors

* What went wrong: SSH failed even though I thought the IP was right.
* What fixed it: Making sure all VMs were actually running, all were on the same UTM network mode, and the IP addresses were re-checked with `ip a`.

### 3) “Connection refused” when trying SSH

* What went wrong: The network was fine, but the SSH service wasn’t installed or wasn’t running.
* What fixed it: Installing `openssh-server` and enabling the SSH service with `systemctl enable --now ssh`.

### 4) Firewall surprises

* What went wrong: SSH was running, but the connection still failed.
* What fixed it: Allowing SSH through `ufw` using `sudo ufw allow ssh` (and reloading rules).

### 5) Remembering IPs slowed me down

* What went wrong: I kept re-checking IP addresses and mixing them up.
* What fixed it: Adding entries to `/etc/hosts` so I could use simple names like `vm1`.

---

## Results

After completing these steps:

* All VMs share the same network and can ping each other.
* The controller VM can SSH into `vm1`, `vm2`, and `vm3` reliably.
* The lab is ready for automation projects like a multi-server health collector.

---

## Next steps

* Set up passwordless SSH using `ssh-keygen` and `ssh-copy-id`.
* Write a Bash script to collect metrics like `uptime`, disk usage (`df -h`), and memory/CPU snapshots.
* Combine output into a simple HTML report or dashboard.
