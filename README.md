# Secure AD DS Lab with NAT Routing & Centralized Policy Management

## 🎯 Goal
I built this lab to simulate a real-world enterprise network. My main focus was providing internet access to my virtual machines without exposing them directly to the outside world for security. By configuring the Windows Server as a **NAT Gateway**, I created a "middleman" that keeps the lab isolated while still allowing for updates and web traffic. Beyond connectivity, I implemented a professional **Identity and Access Management (IAM)** framework and utilized **Group Policy** to automate security and resource delivery across the `pawplicity.com` domain.

---

## 🏗️ Core Components

* **Windows Server (DC & Gateway):**
    * **RRAS & NAT:** Acts as the router. It bridges the private lab network to my physical internet connection.
    * **DHCP & DNS:** Handles all the internal networking logic so clients can find the domain and get online automatically.
* **Identity & Access Management (IAM):**
    * Designed a custom **OU structure** (Pawplicity) to organize users and service accounts.
    * Provisioned **Security Groups** to test permissions and access control.
* **Windows Client:** A domain-joined workstation used to verify that the networking and policies actually work in practice.
* **Custom Policies (GPOs):**
    * **Drive Mapping:** Automatically maps a network share (`\\NY-DC\Public_Share`) via UNC path using Item-Level Targeting.
    * **Screensaver Lock:** Enforces a mandatory screensaver and timeout lock to ensure workstations are secured when idle.
    * **Local Admin Control:** Centralized workstation management by adding a dedicated `Pawp_Admin` to the local Administrators group of all domain-joined PCs.

<p align="left">
  <img src="screenshots/larry.png" alt="Network Topology Diagram" width="450">
  <br>
  <b>Figure 1:</b> <i>Dual-Homed Edge Gateway Topology with RRAS/NAT</i>
</p>

---

## 🛠️ Infrastructure Prerequisites

| Component | Description |
| :--- | :--- |
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 Pro |
| **Deployment** | 2 Virtual Machines (Domain Controller & Client) |
| **Networking** | AD DS, DNS, DHCP (Option 003/006/015), RRAS (NAT) |

---

## 🚀 Implementation Phases

### Phase 1: Active Directory Provisioning (IAM)
Started by spinning up the core server roles and building a fresh Active Directory forest from scratch.

**1.1 Role Installation**
Installed **AD DS, DHCP, DNS, and RRAS** via Server Manager to establish identity management and core network services.
<img width="800" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/25106061-fa42-49f5-a821-bea24fbec25d" />

**1.2 Forest Creation & AD DS Promotion**
Setup a new forest with `pawplicity.com` as the root domain, creating the main security boundary for the lab.
<img width="400" alt="Naming my Domain" src="https://github.com/user-attachments/assets/cf12dbbf-0bbe-4fd8-8007-adc48cae31ac" />

---

### Phase 2: DHCP & NAT Routing Setup
Configured the DC to handle the private LAN by setting up DHCP to automate IP assignments and RRAS for external access.

* **DHCP Scope Configuration:**
    * **Address Pool:** 10.5.10.20 – 10.5.10.250
    * **DNS (Option 006):** Pointed to 10.5.10.5 for domain resolution.
    * **Gateway (Option 003):** Pointed to 10.5.10.5 to facilitate external routing.

**2.1 NAT Implementation**
Utilized the **RRAS console** to transform the server into a gateway, allowing internal clients to communicate with external services while remaining hidden behind the server's external IP.
<img width="800" alt="RRAS Up and Running" src="https://github.com/user-attachments/assets/ea306751-306d-4269-a341-986178890c59" />

---

### Phase 3: Identity Management & Directory Structure
With the network stable, I established a professional directory hierarchy to manage the organization.

**3.1 Custom OU Hierarchy**
Created the `Pawplicity` parent
