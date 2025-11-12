# 🧠 Active Directory Lab in VMware

## 📘 Purpose
The goal of this lab is to practice and strengthen system administration skills, focusing on **Active Directory Domain Services (AD DS)** setup, domain joining, and user management — all within a virtualized environment.



Error Explanation:
The PowerShell error occurred because the ADDSDeployment module and its cmdlets (such as Install-ADDSForest) were not available on the system. These commands are part of the Active Directory Domain Services (AD DS) feature in Windows Server, not a downloadable PowerShell module. Installing the AD DS role with management tools (Install-WindowsFeature AD-Domain-Services -IncludeManagementTools) resolves the issue by adding the required module and cmdlets.
---

## ⚙️ Lab Overview

| Component | Description |
|------------|-------------|
| **Hypervisor** | VMware Workstation / VMware Fusion |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Virtual Machines** | 2 (Domain Controller & Client) |
| **VM Resources** | 2 CPUs, 2 GB RAM, 30 GB HDD each |

---

## 🌐 Network Configuration

| VM | Adapter 1 | Adapter 2 | Purpose |
|----|------------|-----------|----------|
| **Domain Controller (DC)** | NAT | Host-Only | Internet access + internal LAN |
| **Windows 11 Client** | Host-Only | — | Internal domain connectivity |

> **Why NAT + Host-Only?**  
> The **NAT** adapter lets the DC reach the internet through the host machine’s IP, while **Host-Only** creates a private network where the DC and client can communicate securely.

---
CREATE FOLDERS STRUCTURE.


mkdir -p AD-Lab/screenshots
mkdir -p AD-Lab/scripts
cd AD-Lab
touch README.md
touch screenshots/.gitkeep
touch scripts/.gitkeep

## 🏗️ Setup Steps

### 1️⃣ Prepare the Environment
- Install **VMware** on your host PC.  
- Download ISO images for:
  - **Windows Server 2022**
  - **Windows 11**

---

### 2️⃣ Create Virtual Machines
Create two VMs and assign resources:

| VM Name | OS | CPU | RAM | HDD | Notes |
|----------|----|-----|-----|-----|-------|
| `DC-Server2022` | Windows Server 2022 | 2 | 2 GB | 30 GB | Domain Controller |
| `Client-Win11` | Windows 11 | 2 | 2 GB | 30 GB | Domain-Joined Client |

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
