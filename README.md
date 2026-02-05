# Secure AD DS Lab with NAT Routing

## 🎯 Goal
I built this lab to simulate a real-world enterprise network. My main focus was providing internet access to my virtual machines without exposing them directly to the outside world for security. By configuring the Windows Server as a **NAT Gateway**, I created a "middleman" that keeps the lab isolated while still allowing for updates and web traffic.

---

## 🏗️ Core Components

* **Windows Server (DC & Gateway):**
    * **RRAS & NAT:** Acts as the router. It bridges the private lab network to my physical internet connection.
    * **DHCP & DNS:** Handles all the internal networking logic so clients can find the domain and get online automatically.
* **Active Directory Management:**
    * Designed a custom OU structure to organize users and service accounts.
    * Set up users and groups to test permissions and access control.
* **Windows Client:** A domain-joined workstation used to verify that the networking and policies actually work in practice.
* **Custom Policies (GPOs):**
    * **Drive Mapping:** Automatically maps a network share (like a "Public" or "Home" drive) when a user logs in.
    * **Screensaver Lock:** Enforced a mandatory screensaver and timeout lock to ensure workstations aren't left open.

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
| **Resource Per VM** | 2 CPUs, 4 GB RAM, 30 GB HDD |
| **Networking** | AD DS, DNS, DHCP (Option 003/006), RRAS (NAT) |

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

### Phase 2: DHCP Setup
Configured the DC to handle the private LAN by setting up DHCP to automate IP assignments.

* **Service Authorization:** Authorized the DHCP server in AD to ensure only trusted servers hand out IPs.
* **Scope Configuration:**
    * **Address Pool:** 10.5.10.20 – 10.5.10.250
    * **DNS (Option 006):** Pointed to 10.5.10.5 for domain resolution.
    * **Gateway (Option 003):** Pointed to 10.5.10.5 to facilitate external routing.

<img width="800" alt="NAT Gateway for Client Machines" src="https://github.com/user-attachments/assets/23bf95ae-d727-4018-a45f-92f94421f028" />

---

### Phase 3: Routing & Remote Access (NAT)
Setup NAT on the DC to bridge the private LAN to the internet using dual network cards.

**3.1 Interface Identification**
Identified the internal (**DC_NY**) and external adapters to define the boundary between the private lab and the WAN.
<img width="800" alt="Network Adapters" src="https://github.com/user-attachments/assets/7b9bd6ca-5c29-4d46-b724-953e1e746d87" />

**3.2 NAT Implementation**
Utilized the **RRAS console** to transform the server into a gateway, allowing internal clients to communicate with external services while remaining hidden behind the server's external IP.
<img width="800" alt="RRAS Up and Running" src="https://github.com/user-attachments/assets/ea306751-306d-4269-a341-986178890c59" />

---

### Phase 4: Verification & Integration
The final phase validates the network flow and ensures domain connectivity.

**4.1 Client Network Verification**
The Windows 11 client successfully leased an IP (**10.5.10.22**) and confirmed internet connectivity via the NAT gateway.
<img width="800" alt="ClientPCs have internet Access" src="https://github.com/user-attachments/assets/8de8b5a5-8fcb-48d0-9adb-910adb2d6ef0" />

**4.2 Active Directory Validation**
Verified successful domain joins by confirming the client machine is correctly indexed within the **Active Directory Users and Computers (ADUC)** container.

---

## 🧠 Lessons Learned
* **DNS is the Backbone:** If DNS isn't perfect, everything from domain joins to services will fail.
* **Static IPs:** I kept the private NIC on a static IP to keep the routing and DHCP stable.
* **DHCP Options:** Ensuring Options **003** and **006** were spot on was the key to allowing isolated clients to find the domain and the internet simultaneously.
