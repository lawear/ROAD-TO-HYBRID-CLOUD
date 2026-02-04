
## Project: Secure AD DS Lab with NAT Routing
**The Goal:**
I built this lab to simulate a real-world enterprise network.My main focus was providing internet access to my virtual machines without exposing them directly to the outside world for security. By configuring the Windows Server as a NAT Gateway, I created a "middleman" that keeps the lab isolated while still allowing for updates and web traffic.

**Core Components**
**Windows Server (DC & Gateway):**
RRAS & NAT: Acts as the router. It bridges the private lab network to my physical internet connection.

**DHCP & DNS:** Handles all the internal networking logic so clients can find the domain and get online automatically.

**Active Directory Management:**
Designed a custom OU structure to organize users and service accounts and  Set up users and groups to test permissions and access control.

**Windows Client:** A domain-joined workstation used to verify that the networking and policies actually work in practice.

**Custom Policies (GPOs):**
To make the lab behave like a production environment, I pushed out a couple Group Policy Objects:

-Drive Mapping: Automatically maps a network share (like a "Public" or "Home" drive) when a user logs in. This is way more efficient than doing it manually on every machine.

-Screensaver Lock: Enforced a mandatory screensaver and timeout lock. This is a standard security move to ensure workstations aren't left wide open when someone walks away.

<p align="Left">
  <img src="screenshots/larry.png" alt="Network Topology Diagram" width="450">
  <br>
   <b>Figure 1:</b> <i>Dual-Homed Edge Gateway Topology with RRAS/NAT</i>
</p>

**How it Works:**

Isolation: The client machine sits on a private virtual switch. It can't "see" my home router at all.

The Handshake: The client asks the Server for an IP (DHCP) and the Server tells it, "I'm your gateway."

The Routing: When the client goes to a website, the Server uses NAT to mask the client's internal IP. The server fetches the data and hands it back, keeping the client's identity hidden from the internet.

Policy Enforcement: As soon as a user logs in, the DC pushes the drive mappings and screensaver settings, ensuring the workstation is instantly configured.

**Infrastructure Pre-requisite:**
| Component | Description |
|-----------|-------------|
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 Pro |
| **Deployment** | 2 Virtual Machines (Domain Controller & Client) |
| **Resource Per VM** | 2 CPUs, 4 GB RAM, 30 GB HDD |
| **Networking** | AD DS, DNS, DHCP (Option 003/006), RRAS (NAT) | |





**Phase 1:Active Directory Provisioning(IAM):**
Started by spinning up the core server roles and building a fresh Active Directory forest from scratch on my win 22 server vm.

**1.1 Role Installation**
Hit 'Add Roles and Features' in Server Manager and checked off the boxes for AD DS, DHCP,DNS and RRAS. This got my identity management and core network services up and running for the lab.

<img width="700" height="1000" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/25106061-fa42-49f5-a821-bea24fbec25d" />

**1.2 Forest Creation & AD DS Promotion**:
Setup a new forest with pawplicity.com as the root domain. This created the main security and identity boundary for everything in the lab.

<img width="700" height="1000" alt="Naming my Domain" src="https://github.com/user-attachments/assets/cf12dbbf-0bbe-4fd8-8007-adc48cae31ac" />
<img width="700" height="1000" alt="After Restart" src="https://github.com/user-attachments/assets/e1a578b1-409e-4b0a-a7ef-ee619c657b25" />


**Phase 2: DHCP setup**
Configured the DC to handle the private LAN by setting up DHCP. This automates IP assignments and gives the clients the routing info they need to talk to each other.

**2.1 Service Authorization**
Authorized the DHCP server in AD so only trusted servers can hand out IP addresses on the network

<img width="700" height="1000" alt="Auth Successful" src="https://github.com/user-attachments/assets/0f1acbbc-e049-4c18-be5b-8b4a2b3b3e33" />

**2.2 Scope Configuration**
Address Pool: 10.5.10.20 – 10.5.10.250
Subnet Mask: 255.255.255.0
DNS (Option 006): Pointed to 10.5.10.5 for domain resolution.
Gateway (Option 003): Pointed to 10.5.10.5 to facilitate external routing.

<img width="700" height="1000" alt="NAT Gateway for Client Machines to reach internet" src="https://github.com/user-attachments/assets/23bf95ae-d727-4018-a45f-92f94421f028" />

**Phase 3: Routing & Remote Access (NAT)**
Setup NAT on the DC to bridge the private LAN to the internet. Since the server has two network cards, this lets it act as the gateway for the lab.

**3.1 Interface Identification**
Identified the internal (DC_NY) and external adapters to define the boundary between the private lab and the WAN.
<img width="700" height="1000" alt="Network Adapters" src="https://github.com/user-attachments/assets/7b9bd6ca-5c29-4d46-b724-953e1e746d87" 
  
**3.2 NAT Implementation**
Utilized the Routing and Remote Access (RRAS) console to transform the server into a functional gateway, allowing internal clients to communicate with external web services while remaining hidden behind the server's external IP.
<img width="700" height="1000" alt="Looking for green(Up and Running)" src="https://github.com/user-attachments/assets/ea306751-306d-4269-a341-986178890c59" />

**Phase 4: Verification & Integration**
The final phase validates the network flow and ensures domain connectivity.

**4.1 Client Network Verification**
The Windows 11 client successfully leased an IP (10.5.10.22) from the DHCP server and confirmed internet connectivity via the NAT gateway.
<img width="700" height="1000" alt="ClientPCs have internet Access" src="https://github.com/user-attachments/a ssets/8de8b5a5-8fcb-48d0-9adb-910adb2d6ef0" />

**4.2 Active Directory Validation**
Verified successful domain joins by confirming the client machine is correctly indexed within the Active Directory Users and Computers (ADUC) container.

## Lessons Learned
I learned that DNS is the backbone of AD—if it isn't perfect, everything from domain joins to services will fail. I kept the private NIC on a static IP to keep the routing and DHCP stable, and I made sure the DHCP options (003 and 006) were spot on so the isolated clients could actually find the domain and the internet.

















