
 
## **Project: Secure ADDS Lab via NAT Routing**

In this project I built a simulation of real-world enterprise level Active Directory environment. My main focus was providing internet access to my virtual machines without exposing them directly to Home Router aka the outside world for security. By configuring the Windows Server(Domain) as a **NAT Gateway**, I created a "middleman" that keeps the lab isolated while still allowing for updates and web traffic.

For governance, I deployed Active Directory Domain Services and a suite of Group Policy Objects to automate security baselines. 

**Key implementations include:**

* **Windows Server (DC & Gateway):**
    * **RRAS & NAT:** Acts as the router. It bridges the private lab network to my physical internet connection.
    * **DHCP & DNS:** Handles all the internal networking logic so clients can find the domain and get online automatically.
* **Identity & Access Management (IAM):**
    * Designed a custom **OU structure** to organize users and service accounts.
    * Provisioned **Security Groups** to test permissions and access control.
* **Windows Client:** A domain-joined workstation used to verify that the networking and policies actually work in practice.
* **Custom Policies (GPOs):**
    * **Drive Mapping:** Mapped the \\NY-DC\Public_Share via GPO preferences. Added Item-Level Targeting so it only hits the right user groups instead of blasting it to the whole domain.
    * **Screensaver Lock:** Enforces a mandatory screensaver and timeout lock to ensure workstations are secured when idle.
    * **Local Admin Control:** Centralized workstation management by adding a dedicated `Pawp_Admin` to the local Administrators group of all domain-joined PCs.

<p align="left">
  <img src="screenshots/larry.png" alt="Network Topology Diagram" width="500">
  <br>
  <b>Figure 1:</b> <i>Dual-Homed Edge Gateway Topology with RRAS/NAT</i>
</p>

---

**Infrastructure Prerequisites**

| Component | Description |
| :--- | :--- |
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Deployment** | 3 Virtual Machines (1 Domain Controller, 2 Client Workstations) |
| **NIC Configuration** | **DC:** Dual NICs (1 NAT for internet, 1 LAN Segment for private domain) <br> **Clients:** Single NIC each, locked to the LAN Segment |
| **Services** | AD DS, DNS, DHCP (Options 003, 006, 015), RRAS (NAT) |

---


**Implementation Phases:** 

  * **Phase 1:Active Directory Provisioning (IAM):**
Started by spinning up the core server roles and building a fresh Active Directory forest from scratch.

  * **1.1 Role Installation**
Installed **AD DS, DHCP, DNS, and RRAS** via Server Manager to establish identity management and core network services.
<img width="550" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/25106061-fa42-49f5-a821-bea24fbec25d" />

  * **1.2 Forest Creation & AD DS Promotion**
Setup a new forest with `pawplicity.com` as the root domain, creating the main security boundary for the lab.

<img width="550" alt="Naming my Domain" src="https://github.com/user-attachments/assets/cf12dbbf-0bbe-4fd8-8007-adc48cae31ac" />

---

## **Phase 2: DHCP & NAT Routing Setup**
I configured the Domain Controller to manage the private LAN by setting up **DHCP** to automate IP assignments and **RRAS** for external access.

### **DHCP Scope Configuration**
I set up a scope to handle the internal network traffic. This ensures any device added to the LAN automatically receives the correct settings to communicate with the DC and the internet.

* **Address Pool:** 10.5.10.20 – 10.5.10.250
* **DNS (Option 006):** Pointed to `10.5.10.5` for domain resolution.
* **Gateway (Option 003):** Pointed to `10.5.10.5` to facilitate external routing.

<img width="500" height="477" alt="DHCP Options" src="https://github.com/user-attachments/assets/1ec0c663-ad5a-4e5b-a501-5bfdc927dc62" />

---

### **2.1 NAT Implementation**
I utilized the **RRAS console** to transform the server into a gateway. This allows internal clients to communicate with external services while remaining hidden behind the server's external IP. I specifically configured the `_Internet_Access_` interface as the public-facing port with NAT enabled.


<img width="500" height="798" alt="NAT Routing Configured" src="https://github.com/user-attachments/assets/140cbb38-1363-43a1-b13d-109682ddbd36" />

---

### **2.2 Connectivity Testing (Client Side)**
To verify the setup, I booted up a client workstation (**PC_01**). As shown in the screenshot below, the client successfully received an IP from the DHCP pool (`10.5.10.20`) and was able to ping an external address (`8.8.8.8`), proving that the NAT routing is working perfectly.


<img width="500" height="700" alt="Client Side DHCP and NAT Routing" src="https://github.com/user-attachments/assets/3fbac16d-18bd-405e-a982-a25addb11092" />

---

## **Phase 3: IAM & Active Directory Structure**
With the network established, I designed and implemented the **Identity and Access Management (IAM)** hierarchy within Active Directory. The goal was to create an organized structure that reflects the company’s geographical locations and departments for easier policy management.

### **Organizational Unit (OU) Hierarchy**
I created a top-level OU for the company, **Pawplicity**, and nested sub-OUs based on regional offices:
* **New Jersey:** Includes Camden and Union locations.
* **New York:** Includes The Bronx and Westchester locations.

Within each specific office location, I standardized the structure by adding sub-OUs for:
* **Computers:** For managing workstation and server objects.
* **Groups:** To handle department-level security and distribution lists.
* **IT_Admins:** For high-privilege administrative accounts.
* **Users:** For general employee accounts.

<img width="600" height="700" alt="IAM Structure" src="https://github.com/user-attachments/assets/d805d033-7738-42fc-9eea-8774b4a2cdd4" />

---

### **Strategy & Management**
This tiered structure allows for granular **Group Policy Object (GPO)** application. For example, I can now apply specific security settings to the entire New York region or drill down to only affect users in the Westchester office. It also simplifies administrative delegation, ensuring that local IT leads only have access to their respective OUs.


## **Phase 4: Automated Bulk User Creation**

To streamline the onboarding process, I utilized a specialized PowerShell script from the **[AD_PS](https://github.com/joshmadakor1/AD_PS)** repository by **Josh Madakor**. This script is designed to automate Active Directory user creation from an external text-based source file, allowing for a more efficient setup compared to manual entry and ensuring consistency across all new accounts.

### **Implementation & Troubleshooting**
While the script provided the framework for automation, I was responsible for configuring it to work within my specific environment. I encountered and resolved several initial errors during the implementation:

* **Data Sanitization**: I identified that trailing spaces in my `.txt` source file were causing headers to be misread. I manually cleaned the data to ensure proper parsing.
* **Path Resolution**: I corrected the script’s `$OU` variable to match the exact **Distinguished Name** of my "Westchester" OU, specifically handling the space in "New York" that was causing "Object Not Found" errors.

---

### **PowerShell Automation Results**
The script iterates through each row of the text file, dynamically building user attributes and placing them directly into the **Westchester > Users** OU.

**Script Features Implemented:**
* **UPN Standardization**: Automatically generated emails in the `@pawplicity.com` format.
* **Security Compliance**: Set a default temporary password and forced a password change at first logon.
* **Targeted Placement**: Successfully bypassed the default "Users" container to utilize my custom IAM hierarchy.


<img width="600" height="600" alt="CreatedUserswithPS" src="https://github.com/user-attachments/assets/35f12bf5-3d8b-46ec-b20e-9f257d654db3" />

---


### **Lessons Learned**
* **The Importance of Data Integrity**: I learned that even a single trailing space in a source `.txt` file can cause a PowerShell script to misinterpret headers, leading to failed execution.
* **Distinguished Names (DN) are Literal**: Active Directory requires exact syntax; I discovered that any naming mismatch—such as a missing space in "New York"—will cause the script to fail as it cannot resolve the target path.
* **Automation Efficiency**: Utilizing a script significantly decreased onboarding time and reduced the potential for manual typos compared to creating 13+ users via the GUI.
* **Troubleshooting Error Logs**: This phase reinforced the value of reading PowerShell error codes to trace a problem back to its root cause, allowing for quick pivots in script configuration.

---

### **Final Verification**
The screenshot below confirms the successful completion of the bulk import. All users from the source file are now properly populated within the **Westchester > Users** OU with their correct names and attributes.

<img width="600" height="600" alt="ConfirmedUserCreation" src="https://github.com/user-attachments/assets/12d2b0be-50a4-4507-bebb-e512a84f267f" />
