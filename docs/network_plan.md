# Network Plan

## Overview
My home lab network uses the `10.0.1.0/24` subnet. All primary devices and VMs are assigned static IP addresses for stability, easy management, and integration with monitoring and service discovery.

## IP Address Assignments

| Device/VM         | Role                                | IP Address    | Notes                                                              |
| :---------------- | :---------------------------------- | :------------ | :----------------------------------------------------------------- |
| **Physical Hosts**|                                     |               |                                                                    |
| Raspberry Pi 5    | Ubuntu Server, Jellyfin, Nextcloud  | `10.0.1.10`   | Bare-metal apps, also hosts Pi-hole (DNS)                          |
| Laptop            | Proxmox VE Host                     | `10.0.1.200`  | Intel i5-5200U, 8GB DDR3, 256GB SSD                                |
| **Proxmox VMs** |                                     |               |                                                                    |
| `checkmk-server`  | Checkmk Monitoring System           | `10.0.1.207`  | (VM, Debian 12 minimal)                                            |
| `mgmt-server`     | Management VM (Git, Kubectl, Ansible) | `10.0.1.208`  | (VM, Debian 12 minimal) - **Current focus of setup** |
| `nginx-proxy`     | Nginx Reverse Proxy (VM / LXC)      | `10.0.1.204`  | (Your existing VM, planned migration to LXC) - **Also Pi-hole IP** |
| `stremio-wsg`     | Stremio & WSG (VM / LXC)            | `10.0.1.205`  | (Your existing VM, planned migration to LXC)                       |
| `web-host`        | Web Page (VM / LXC)                 | `10.0.1.206`  | (Your existing VM, planned migration to LXC)                       |
| `k3s-master`      | Kubernetes Master (VM)              | `10.0.1.210`  | (To be created)                                                    |
| `k3s-node-1`      | Kubernetes Worker (VM)              | `10.0.1.211`  | (To be created, optional for a single-node cluster initially)      |

## Network Configuration Notes

* **Subnet:** `10.0.1.0/24`
* **Gateway (Router):** `10.0.1.1`
* **DNS Servers:**
    * Primary: `10.0.1.204` (Your Pi-hole instance, running on the Raspberry Pi)
    * Secondary: `1.1.1.1` (Cloudflare DNS - for redundancy)
    * *Note: Ensure your router's DHCP is configured to hand out `10.0.1.204` as the primary DNS for all clients, if you want everything to go through Pi-hole.*
* **Router/DHCP:**
    * It is crucial to configure your main router's DHCP server to either:
        * Exclude the `10.0.1.10` - `10.0.1.211` range (or a broader range like `10.0.1.10` - `10.0.1.254`) from its DHCP pool.
        * Or, create DHCP reservations for these specific MAC addresses if you still want DHCP but with guaranteed IPs.
    * This prevents IP conflicts with the statically assigned addresses.
* **Firewall:**
    * Plan for firewall rules (e.g., UFW on Ubuntu/Debian, Proxmox built-in firewall) to allow necessary traffic:
        * **SSH:** Port 22 (from management devices)
        * **HTTP/HTTPS:** Ports 80/443 (for web services, Nginx)
        * **Checkmk Agent:** Port 6556 (from Checkmk server)
        * **Kubernetes Ports:** (specific ports for API, Kubelet, etc., when K3s is set up)
    * Default to a "deny all incoming" policy and explicitly allow only what is necessary.

---

**Action:**

1.  **On your `mgmt-server` VM**, ensure you are in your `home-lab-k8s` directory:
    ```bash
    cd ~/home-lab-k8s
    ```
2.  **Create the `docs` directory if it doesn't exist:**
    ```bash
    mkdir -p docs
    ```
3.  **Create and edit the `network_plan.md` file:**
    ```bash
    nano docs/network_plan.md
    ```
4.  **Paste the content above** into the file.
5.  **Save and exit Nano** (`Ctrl + O`, Enter, `Ctrl + X`).
6.  **Commit and push these changes to GitHub:**
    ```bash
    git add docs/network_plan.md
    git commit -m "Add initial network plan documentation"
    git push origin main # or 'master'
    ```

This ensures your network plan is now version-controlled and readily accessible within your repository!
