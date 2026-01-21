### Project: Virtualized AD-DS LAB 


## Summary
**Building a Secure Active Directory Lab: A Step-by-Step Virtualization Guide**


### VMware Settings
| Component | Description |
|------------|-------------|
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Virtual Machines** | 2 (Domain Controller & Client) |
| **VM Resources** | 2 CPUs, 4 GB RAM, 30 GB HDD each |

---




**Network Topology**
| VM | Adapter 1 | Adapter 2 | Configuration|
|----|------------|-----------|----------|
| **Domain Controller (DC)** | NAT(Routing) | LAN Segment|Static IP|
| **Windows 11 Client** | -- | LAN Segment| InternetAccess & DHCP via DC |







---
CREATE FOLDERS STRUCTURE.


mkdir -p AD-Lab/screenshots
mkdir -p AD-Lab/scripts
cd AD-Lab
touch README.md
touch screenshots/.gitkeep
touch scripts/.gitkeep

##Roles and Server Installations##
Access the Server Manager Dashboard to begin the service provisioning.

1.Navigate to Manage > Add Roles and Features.

2.Select Role-based or feature-based installation.

3.Target Server: DC_NY.

4.Select the following roles:
  *Active Directory Domain Services (AD-DS)
  *DNS Server
  *DHCP Server
  *Remote Access (Required for NAT/Routing)







### Configuring a DHCP Scope
The DHCP service automates the assignment of IP addresses, subnet masks, and other network parameters to client machines. To initialize this service, an administrator must define a Scope—a consecutive range of possible IP addresses for a network.

Initialization: Within the Server Manager, navigate to Tools > DHCP. In the management console, right-click IPv4 and initiate the New Scope Wizard.

Identification: Assign a logical Name and Description to the scope to ensure administrative clarity within the directory.

Address Range and Subnetting: Define the pool of available IP addresses by entering the Start and End IP addresses. The CIDR (Classless Inter-Domain Routing) notation and Subnet Mask must be configured here to define the boundaries of the local broadcast domain.

Lease Duration: By default, the system sets a Lease Duration (the lifespan of an assigned IP address before it must be renewed) of 8 days.

Scope Options (Gateway and DNS): * Default Gateway: Enter the IP address of the Domain Controller (DC). In this specific architecture, the DC serves as the gateway due to its NAT routing configuration.

DNS Server: Specify the DC’s IP address. This is a critical step; without pointing to the local DNS, client machines will fail to authenticate or resolve the domain name.

Activation: Skip the WINS Server configuration, confirm the activation of the scope, and finalize the wizard.

### Establishing a NAT Gateway
Network Address Translation (NAT) allows multiple devices on a private local network to access the internet using a single public IP address. This is managed through the Routing and Remote Access (RRAS) role.

Accessing RRAS: From the Server Manager, select Tools > Routing and Remote Access.

Server Configuration: Right-click the server node and select Configure and Enable Routing and Remote Access.

Role Selection: Choose Network Address Translation (NAT) from the list of deployment options.

Interface Mapping: Select the External/Public Network Interface Card (NIC). This is the interface that connects to the internet service provider or the external network.

Completion: Finalize the setup to enable the routing of traffic from the internal private network to the external public network.                                            


---


Title Idea: Building a Secure Active Directory Lab: A Step-by-Step Virtualization Guide
Subtitle: Master Windows Server 2022, NAT Routing, and Client Integration using VMware.

The Vision
In the world of System Administration, understanding the backbone of enterprise identity management—Active Directory Domain Services (AD-DS)—is essential. I decided to build a fully functional, isolated laboratory environment to simulate a real-world corporate network.

My goal was simple: Create a secure, multihomed environment where a Windows Server 2022 Domain Controller acts as the gateway for internal clients, providing DNS, DHCP, and NAT Routing.
The Blueprint: Network Topology
Before touching any ISO files, I mapped out the architecture. The lab relies on a dual-homed Domain Controller that bridges the gap between the internet and a private LAN segment.

Component,Role,Network Configuration
Domain Controller,Gateway / DNS / DHCP,Adapter 1 (NAT) & Adapter 2 (LAN Segment)
Windows 11 Client,End-User Workstation,LAN Segment (Isolated)



