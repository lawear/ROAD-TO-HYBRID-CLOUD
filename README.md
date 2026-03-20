
 
## **Project: Secure ADDS Lab via NAT Routing**

In this project I built a simulation of real-world enterprise level Active Directory environment. My main focus was providing internet access to my virtual machines without exposing them directly to Home Router aka the outside world for security. By configuring the Windows Server(Domain) as a **NAT Gateway**, I created a "middleman" that keeps the lab isolated while still allowing for updates and web traffic.

For governance, I deployed Active Directory Domain Services and a suite of Group Policy Objects to automate security baselines. 

<p align="left">
  <img src="screenshots/larry.png" alt="Network Topology Diagram" width="500">
  <br>
  <b>Figure 1:</b> <i>Dual-Homed Edge Gateway Topology with RRAS/NAT</i>
</p>


**Key implementations include:**

* **Windows Server (DC & Gateway):**
    * **RRAS & NAT:** Acts as the router. It bridges the private lab network to my physical internet connection.
    * **DHCP & DNS:** Handles all the internal networking logic so clients can find the domain and get online automatically.
* **Identity & Access Management (IAM):**
    * Designed a custom **OU structure** to organize users and service accounts.
    * Provisioned **Security Groups** to test permissions and access control.
* **Windows Client:** A domain-joined workstation used to verify that the networking and policies actually work in practice.


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
* **Custom Policies (GPOs):**
    * **Drive Mapping:** Automatically map the I: drive to \\NY-DC\Pawp_drive_shares for everyone in the Westchester office. Using Group Policy Preferences.
    * 
**1.Linking the Policy:**
I created the GPO_DriveMapping_Public object and linked it directly to the Westchester > Users OU. By targeting the OU instead of the whole domain, I ensured this share only hits the regional staff who actually need access to these files.

<img width="2000" height="1000" alt="Drive_Mapping" src="https://github.com/user-attachments/assets/e2740a36-24d7-40e5-b97a-a871732a857e" />

<img width="2000" height="1000" alt="wttg" src="https://github.com/user-attachments/assets/4c2d9424-c92e-4426-b54f-7ef4d6111461" />




**2.Configuring the Mapping:**
Inside the GPO Editor, I set the action to Update. I used "Update" over "Create" because it’s more flexible; if I ever change the drive label or the server path later, the policy will push those changes out automatically without me having to delete and recreate the map.

Path: \\NY-DC\Pawp_drive_shares

Drive Letter: I:

Label: my_pawp_drive

<img width="2904" height="1425" alt="Creating Drive" src="https://github.com/user-attachments/assets/ef2d13ab-5c27-4923-9560-f572b19e9db6" />



**3.Testing & Verification:**
Confirmed auto mapping on client machine logging in with authorized user linked to policy.

<img width="3099" height="1623" alt="Drive_mapped" src="https://github.com/user-attachments/assets/a2f3dde1-9427-4692-b7dd-453972a822a1" />


**Screensaver Lock:** Enforces a mandatory screensaver and timeout lock to ensure workstations are secured when idle.

**Workstation Hardening(Personalization & Security)**
Objective: Standardize the desktop environment for the Westchester branch and enforce an idle-lock policy that cannot be bypassed by end-users.

**1.Scope & Implementation:**
I created and linked the Workstation_Personalization_Hardening GPO to the Westchester > Users OU. Applying this at the User level ensures the security baseline follows the employee to any workstation or VM they sign into within the domain.

**2.Technical Configuration**
Using the Group Policy Management Editor, I moved beyond simple preferences to strictly enforced lockdowns:

Prevent changing screen saver: Set to Enabled.

Prevent changing color scheme & theme: Set to Enabled.

Password protect screen saver: Set to Enabled.

Screen saver timeout: Hard-coded to 900 seconds (15 minutes).

<img width="3270" height="1683" alt="Personalization settings" src="https://github.com/user-attachments/assets/80b43eee-e1ff-4b6a-a257-473c85e4a9e4" />

**3.Validation & Testing**
To verify, I ran gpresult /r on PC_01 for user Martin Ødegaard.
<img width="2000" height="1000" alt="ScreenSaver Policy applied" src="https://github.com/user-attachments/assets/cf8b6b53-4ba3-4547-b64e-2b98fc304328" />


Terminal Output: Confirmed Workstation_Personalization_Hardening under Applied Group Policy Objects.

Client Impact: Attempting to access the Display Control Panel triggers an immediate system restriction notice: "Your system administrator has disabled launching of the Display Control Panel."
* **Local Admin Control:** Centralized workstation management by adding a dedicated `Pawp_Admin` to the local Administrators group of all domain-joined PCs.


### **Lessons Learned & Technical Insights**
* **Data Integrity & Syntax**: I discovered how literal Active Directory is with naming conventions. Even a single trailing space in a .txt source file or a missing space in a Distinguished Name (DN) (e.g., "New York" vs "NewYork") causes script failure.
  
* **DNS is NOT Optional**: I learned Active Directory is entirely dependent on DNS. If a client cannot resolve the Domain Controller’s SRV records, the domain join will fail. I configured the DC as the primary DNS server for the entire lab to ensure seamless name resolution and authentication.

* **DHCP & Gateway Authority**: To enable NAT Routing, I configured specific DHCP Options: 003 (Default Gateway) and 006 (DNS Servers). This automated client-side networking, allowing workstations to resolve the DC while routing internet traffic through the NAT server.

* **Automation vs. GUI**: Scripting the user creation saved a massive amount of time. Manually creating 13+ users in the GUI is slow and prone to typos; automation ensures every account is built with the exact same attributes every time.

* **Log-Based Troubleshooting**: Instead of guessing why a script failed, I learned to lean on the PowerShell error codes. Reading the specific "Object Not Found" or "Access Denied" errors allowed me to pivot and fix the configuration in seconds rather than minutes.
---

### **Final Verification**
The screenshot below confirms the successful completion of the bulk import. All users from the source file are now properly populated within the **Westchester > Users** OU with their correct names and attributes.

<img width="600" height="600" alt="ConfirmedUserCreation" src="https://github.com/user-attachments/assets/12d2b0be-50a4-4507-bebb-e512a84f267f" />
