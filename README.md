
## Project: Virtualized AD-DS LAB
Description: Designed and deployed a Windows Server 2022 DC acting as a DNS/DHCP server and NAT Router, bridging a VMware NAT interface to an isolated LAN Segment for Windows 11 client connectivity.


##  Lab Overview

| Component | Description |
|------------|-------------|
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Virtual Machines** | 2 (Domain Controller & Client) |
| **VM Resources** | 2 CPUs, 4 GB RAM, 30 GB HDD each |

---



<img width="1923" height="1196" alt="VM Settingss" src="https://github.com/user-attachments/assets/65b89cc3-10af-4864-bcae-e4042e2f275e" />


## Network Configuration

| VM | Adapter 1 | Adapter 2 | Configuration|
|----|------------|-----------|----------|
| **Domain Controller (DC)** | NAT(Routing) | LAN Segment| Static IP|
| **Windows 11 Client** | -- | LAN Segment| InternetAccess & DHCP via DC |





Pre- VM Install Settings

<img width="2439" height="1323" alt="Netwaork Settings" src="https://github.com/user-attachments/assets/80ce2dfb-a27f-4483-8b74-4227b54c7a0f" />




---
CREATE FOLDERS STRUCTURE.


mkdir -p AD-Lab/screenshots
mkdir -p AD-Lab/scripts
cd AD-Lab
touch README.md
touch screenshots/.gitkeep
touch scripts/.gitkeep

### Setup Steps:
 ### INSTALL ACTIVE DIRECTORY & PROMOTE TO DC
-Select “Add Roles and Features” from Server Manager Dashboard.

-Run through the installation wizard leaving “Role-based or feature-based” selected.

-Select Server(DC_NY)

-Select Active Directory Domain Services,DNS Server,DHCP Server and Remote Access.

-Confirm and Install





.<img width="3150" height="1758" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/a49dc094-d992-4a19-af78-62d4e18b9653" />



---<img width="3072" height="1740" alt="Promote Server to DC" src="https://github.com/user-attachments/assets/6ce765c8-c84c-4fc7-b464-46171e09ac76" />


### 
Promote to Domain Controller:
Select "Add New Forest and give a name including domain extension eg. ".com" or ".local"

<img width="2871" height="1731" alt="Naming my Domain" src="https://github.com/user-attachments/assets/462adc4e-2cd1-4da6-9ea7-38c14123bd11" />




<img width="3249" height="1983" alt="After Restart" src="https://github.com/user-attachments/assets/f12e3460-ca59-4a9d-a95c-18c50a0d122e" />






<img width="2970" height="1842" alt="Users and Computers" src="https://github.com/user-attachments/assets/ef43a142-1ed8-4e0d-814a-e8e4c3ff492f" />



#### DHCP SERVER AND SCOPE CONFIGURATION:






### 
📸 *Screenshot Placeholder:*  
`![VM Settings](./screenshots/vm-settings.png)`

---

### 3️⃣ Install Operating Systems
- Mount the downloaded ISOs in each VM.
- Choose **Custom installation** for partition setup.
- For Windows Server:
  - Select **Desktop Experience (GUI)**.
  - Set an **Administrator password**.

📸 *Screenshot Placeholder:*  
`![Windows Server Installation](./screenshots/server-installation.png)`

---

### 4️⃣ Configure Networking
**Domain Controller (DC):**
- Adapter 1 → NAT  
- Adapter 2 → Host-Only  

**Windows 11 Client:**
- Adapter 1 → Host-Only  

📸 *Screenshot Placeholder:*  
`![Network Configuration](./screenshots/network-config.png)`

---

## 🧩 Active Directory Setup

### 5️⃣ Install and Configure AD DS (PowerShell)
On the **DC VM**, open PowerShell as Administrator:

```powershell
# Install ADDS Role
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Configure new forest
Install-ADDSForest -DomainName "homelab.local"

Reboot after promotion and log in as the new Domain Admin.

📸 Screenshot Placeholder:
![AD DS Installation](./screenshots/adds-install.png)

#Add Users via PowerShell

Example command to create a test user:

New-ADUser -Name "John Doe" `
-AccountPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) `
-Enabled $true

📸 Screenshot Placeholder:
![Add AD User](./screenshots/add-user.png)

💻 Join Windows 11 Client to Domain

Change the client’s DNS to the DC’s Host-Only IP (e.g. 192.168.56.10).

Go to:

Settings → System → About → Rename this PC (Advanced)


Enter the domain name:

homelab.local


Provide domain credentials to join.

📸 Screenshot Placeholder:
![Join Domain](./screenshots/join-domain.png)

After restart, log in using the domain account.

cmd:whoami


## 📂 Folder Structure
```
ADDS-LAB/
├── README.md
├── /screenshots
│   └── .gitkeep
├── /scripts
│   └── .gitkeep
├── /notes
│   └── .gitkeep
└── /docs
    └── .gitkeep
```


# 🧱 Active Directory Lab in VMware

![VMware](https://img.shields.io/badge/VMware-0071C5?style=for-the-badge&logo=vmware&logoColor=white)
![Windows Server 2022](https://img.shields.io/badge/Windows%20Server%202022-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Windows 11](https://img.shields.io/badge/Windows%2011-0078D6?style=for-the-badge&logo=windows11&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
[Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
[License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

---

## 🧰 Overview
This project documents my **Active Directory home lab** built on VMware Workstation to strengthen system administration skills.  
It includes setup steps, PowerShell configuration, and screenshots of domain controller and client integration.


### Folder Descriptions
- **/screenshots** → images used in documentation  
- **/scripts** → setup or automation scripts  
- **/notes** → personal notes or test results  
- **/docs** → additional documentation or guides  

---
