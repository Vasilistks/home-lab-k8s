# My Home Lab Infrastructure

## Overview
This repository documents the setup, configuration, and management of my personal home lab environment. The goal is to build a robust, scalable, and observable infrastructure for hosting various services and learning DevOps practices.

## Core Technologies
* **Hypervisor:** Proxmox VE
* **Operating Systems:** Debian (VMs), Ubuntu Server (Raspberry Pi)
* **Container Orchestration:** Kubernetes (K3s)
* **Monitoring:** Checkmk
* **Visualization:** Grafana
* **Configuration Management (Planned):** Ansible
* **Version Control:** Git & GitHub

## Network Topology
My home lab operates on the `10.0.1.0/24` subnet. Detailed IP address assignments and network configuration notes can be found in [docs/network_plan.md](docs/network_plan.md).

## Infrastructure Components

### 1. Physical Hardware
* **Proxmox VE Host:** Intel i5-5200U Laptop (8GB DDR3, 256GB SSD)
* **Raspberry Pi 5:** Ubuntu Server (Jellyfin, Nextcloud, Pi-hole)

### 2. Virtual Machines (Proxmox)
* **`mgmt-server` (`10.0.1.208`):** Debian 12 (Minimal) - Dedicated for infrastructure management (Git, `kubectl`, Ansible control plane).
* **`checkmk-server` (`10.0.1.207`):** Debian 12 (Minimal) - Centralized monitoring with Checkmk.
* **`k3s-master` (`10.0.1.210`):** (Planned) - Kubernetes master node.
* **`k3s-node-1` (`10.0.1.211`):** (Planned) - Kubernetes worker node.

### 3. Linux Containers (LXC - Proxmox) / Migrated Services
*(Initially VMs, planned migration to LXC / Kubernetes)*
* **`nginx-proxy` (`10.0.1.204`):** Nginx Reverse Proxy (also hosts Pi-hole currently).
* **`stremio-wsg` (`10.0.1.205`):** Stremio & WebSockets Gateway.
* **`web-host` (`10.0.1.206`):** Static web page hosting.

## Setup & Configuration Guides
Detailed step-by-step guides for setting up each component can be found in the `docs/` directory:

* [Proxmox VE Host Setup](docs/proxmox_setup.md)
* [Raspberry Pi 5 Setup](docs/raspberry_pi_setup.md)
* [Management VM Setup](docs/management_vm_setup.md)
* [Checkmk Server Installation & Initial Setup](docs/checkmk_setup.md)
* [Kubernetes (K3s) Cluster Deployment](docs/k3s_setup.md) (Planned)
* [Grafana Visualization Setup](docs/grafana_setup.md) (Planned)

## How to Use This Repository
This repository serves as my "Infrastructure as Code" (IaC) documentation. All configurations, scripts, and deployment manifests are version-controlled here.

To get started or contribute:
1.  Clone this repository to your management workstation/VM:
    ```bash
    git clone git@github.com:Vasilistks/home-lab-k8s.git
    cd home-lab-k8s
    ```
2.  Follow the setup guides in the `docs/` directory.

## Current Status
* **Proxmox VE Host:** Running.
* **Raspberry Pi 5:** Running with Jellyfin, Nextcloud, Pi-hole.
* **`mgmt-server` VM:** Installed, static IP configured, Git initialized, repository cloned. (Current working point!)
* **`checkmk-server` VM:** Installed, basic monitoring for Pi and Proxmox host configured.
* *Other VMs:* Running, but slated for migration to LXC/K3s.
* *Kubernetes (K3s):* Not yet deployed.
* *Checkmk/Grafana Integration:* Not yet configured.

## Future Plans
* Full Checkmk integration with all VMs/LXCs.
* Deploy K3s cluster on new Proxmox VMs.
* Migrate Nginx, Stremio, and web page to Kubernetes (or LXCs as intermediate).
* Integrate Checkmk with Kubernetes monitoring.
* Set up Grafana dashboards pulling data from Checkmk and Kubernetes (Prometheus).
* Implement Ansible for automated configuration management.
* Explore centralized logging (Loki/Grafana).

---
