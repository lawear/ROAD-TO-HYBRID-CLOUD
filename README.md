

**Building a Secure Active Directory Lab to master Windows Server 2022, NAT Routing, and Client Integration using VMware.
**

**Prerequisite Checklist:**
**ISO Media**: Server 22 iso & windows 11 iso

**DC-SVR2022**: A clean installation of standard Windows Server 2022 with Desktop Experience.

**PC1 & PC2**: A clean installation of Windows 11 Pro.




**VMware Specificatios**:
| Component | Description |
|------------|-------------|
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Virtual Machines** | 2 (Domain Controller & Client) |
| **VM Resources** | 2 CPUs, 4 GB RAM, 30 GB HDD each |

---



**Network Flow Logic:**
| VM | Adapter 1 | Adapter 2 | Configuration|
|----|------------|-----------|----------|
| **Domain Controller (DC)** | NAT(Routing) | LAN Segment|Static IP|
| **Windows 11 Client** | -- | LAN Segment| InternetAccess & DHCP via DC |

**Phase 1:** Server Provisioning and AD DS Promotion
The foundation of the environment begins with the installation of core server roles and the establishment of a new Active Directory forest.

1.1 Role Installation
Before configuring specific services, Active Directory Domain Services (AD DS), DHCP, and DNS roles must be enabled.

Figure 1.1:
Displays the confirmation screen for the selected roles before the installation process begins.

<img width="3150" height="1758" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/25106061-fa42-49f5-a821-bea24fbec25d" />
 

Figure 1.2: Highlights the post-deployment configuration link in Server Manager required to elevate the server to a Domain Controller.

  <img width="3072" height="1740" alt="Promote Server to DC" src="https://github.com/user-attachments/assets/3e763780-ed8f-4d8f-9f07-e822a5f194f6" />


1.2 Forest Creation
A new forest is established to provide the identity and security boundary for the lab.

Figure 1.3: Shows the "Add a new forest" operation with the Root domain name set to Pawplicity.com

<img width="2871" height="1731" alt="Naming my Domain" src="https://github.com/user-attachments/assets/cf12dbbf-0bbe-4fd8-8007-adc48cae31ac" />

Figure 1.4:Displays the Windows login screen showing the PAWPLICITY\Administrator account, confirming the domain is active.
 
 <img width="3249" height="1983" alt="After Restart" src="https://github.com/user-attachments/assets/e1a578b1-409e-4b0a-a7ef-ee619c657b25" />


Phase 2: Internal Network Management (DHCP)
To manage the LAN Segment, the Domain Controller (DC) is configured to automate IP distribution to client workstations.

2.1 DHCP Authorization
To prevent rogue servers, the DHCP service must be authorized within Active Directory.

Figure 2.1: The summary screen showing successful creation of security groups and server authorization.

<img width="1629" height="1707" alt="DHCP Post-Install" src="https://github.com/user-attachments/assets/9b1beb40-c0ab-46eb-87eb-84e18495e29c" />

Figure 2.2:  The DHCP management console displaying a green checkmark, indicating the server is authorized and active.

<img width="1968" height="1626" alt="Auth Successful" src="https://github.com/user-attachments/assets/0f1acbbc-e049-4c18-be5b-8b4a2b3b3e33" />

2.2 Scope Configuration
The DHCP scope defines the addressing logic for all internal assets.

Figure 2.3: Defining the scope name: Range: 10 to 10.5.10.20 .5.10.250.

<img width="1680" height="1560" alt="DHCP Scope_Name and Description" src="https://github.com/user-attachments/assets/0fc63fb4-f106-42b5-87fd-e45dcfa2858b" />


Figure 2.4: Configuring the IP range from 10.5.10.20 to 10.5.10.250 with a /24 subnet mask.

<img width="1146" height="1140" alt="DHCP Scope Options- Setting the instructions" src="https://github.com/user-attachments/assets/524514fc-83e6-41cc-bbf5-883d89c286e9" />

Figure 2.5: Setting the DNS server to 10.5.10.5 to ensure clients can resolve the domain.

<img width="1752" height="1347" alt="DNS Server(DC) so I can join Clients to domain" src="https://github.com/user-attachments/assets/3b631fd8-6893-4cfb-97fa-b7a23c7a1dc9" />


Figure 2.6:Defining the Default Gateway as 10.5.10.5 so clients know where to send external traffic.

<img width="1644" height="1368" alt="NAT Gateway for Client Machines to reach internet" src="https://github.com/user-attachments/assets/23bf95ae-d727-4e20-a085-c4dcef802032" />

Phase 3: Routing and Remote Access (NAT)
To bridge the private LAN Segment to the internet, we implement Network Address Translation (NAT) on the dual-homed Domain Controller.

3.1 Interface Identification
The DC utilizes two network adapters to act as a gateway between the internal and external worlds.

Figure 3.1:  A view of the "Network Connections" window showing both the NAT (External) and LAN Segment (Internal) adapters.

<img width="2946" height="822" alt="Network Adapters" src="https://github.com/user-attachments/assets/7b9bd6ca-5c29-4d46-b724-953e1e746d87" />

Figure 3.2: Detailed IP status of the internal adapter, labeled DC_NY.

<img width="2439" height="1323" alt="Netwaork Settings" src="https://github.com/user-attachments/assets/ddd2b960-a920-4d6f-a53f-731cd72bd442" />


Figure 3.3: Verification of the static IP configuration on the internal interface.

<img width="1296" height="1473" alt="D_NY" src="https://github.com/user-attachments/assets/fdf4060e-da84-4d2b-9aa5-789e9ed7b48f" />


3.2 NAT Implementation
Using the Routing and Remote Access (RRAS) console, the server is transformed into a functional router.

Figure 3.4:  Initializing the "Configure and Enable Routing and Remote Access" wizard.

<img width="1419" height="1104" alt="Configuring NAT Routing" src="https://github.com/user-attachments/assets/1adf6c3a-2a63-440c-ab8d-ef7ea22b27fe" />

Figure 3.5: Selecting the "Network address translation (NAT)" radio button.
 
<img width="1377" height="1218" alt="Select NAT Option" src="https://github.com/user-attachments/assets/0a985647-cbb8-47cd-a340-141154ca672e" />

Figure 3.6: The final confirmation screen showing NAT is configured for the external interface.
 
<img width="1368" height="1023" alt="Finish Routing NAT RoutingConfig" src="https://github.com/user-attachments/assets/f84926d2-be2e-4018-a45f-92f94421f028" />

Figure 3.7: The RRAS console showing the service status as active (Green).

<img width="1269" height="981" alt="Looking for green(Up and Running)" src="https://github.com/user-attachments/assets/ea306751-306d-4269-a341-986178890c59" />

Phase 4: Client Integration and Verification
The final phase confirms the Network Flow Logic by verifying client connectivity and internet access.

4.1 Client Network Acquisition
Workstations on the LAN Segment should automatically receive their configuration from the DC.

Figure 4.1: A Windows 11 client terminal running ipconfig /all, showing an assigned IP of 10.5.10.22 and the DHCP server as 10.5.10.5.

<img width="2556" height="1770" alt="ClientPCs have internet Access  Not on Domain Yet but under the same subnet" src="https://github.com/user-attachments/assets/8de8b5a5-8fcb-48d0-9adb-910adb2d6ef0" />

Figure 4.2: A web browser successfully loading Google, confirming the NAT routing is functional.
[Internet Access Verification] — 

4.2 Active Directory Verification
The last step is ensuring the client is recognized within the domain database.

Figure 4.3: A view of "Active Directory Users and Computers" showing the Windows 11 machine listed in the "Computers" container.






---


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







### Authorizing and Configuring a DHCP Scope
The DHCP service automates the assignment of IP addresses, subnet masks, and other network parameters to client machines. To initialize this service, an administrator must define a Scope—a consecutive range of possible IP addresses for a network.DHCP must be authorized before it is operational.

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



