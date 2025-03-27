# Home-Lab-Setup---SOC-Simulator
Overview: This guide will show you how to create a realistic SOC Lab for learning Active Directory, Threat Detection, and Penetration Testing.

SOC Simulator Home Lab Setup (Very Detailed Guide)
Home Lab Setup Overview:
This guide will show you how to create a realistic SOC Lab for learning Active Directory, Threat Detection, and Penetration Testing.

✅ What We’re Building
Active Directory Environment (Evil Corp)

Attacker Machine (Kali Linux)

Monitoring Systems (Security Onion & Wazuh)

Firewall & Network Management (OPNsense)

📌 Step 1: Setting Up Your Virtual Lab Environment
💻 Hardware Requirements
Main Laptop (Running VMware Workstation Pro 17)

CPU: AMD Ryzen 7 4800H (8-core, 16 threads)

RAM: 16GB (Upgrade to 32GB recommended for better performance)

Storage: 1TB PCIe NVMe SSD

OS: Windows 11 Home

HP Envy Laptop (File Server)

OS: Windows 10 Pro or Windows Server 2019/2022

📌 Step 2: Install VMware Workstation Pro 17
Download from: VMware Official Site

Run the Installer:

Click Next > Accept the EULA > Click Next.

Choose the installation path (leave as default) > Click Next.

Click Install.

Enter License Key: If prompted, enter your key. If not, continue with trial.

Reboot your PC (if required).

📌 Step 3: Creating Virtual Networks
🔨 Configuring Networks in VMware
Open VMware Workstation Pro 17.

Click Edit > Virtual Network Editor.

🔧 Network Settings
Network	Type	Subnet IP	Subnet Mask	DHCP
VMnet0	Bridged	(Uses physical NIC)	(Uses router settings)	Yes (Router DHCP)
VMnet1	Host-Only	10.0.100.0	255.255.255.0	Disabled
VMnet8	NAT	10.0.200.0	255.255.255.0	Enabled (Automatic)
📌 Step 4: Setting Up Virtual Machines
We will create five VMs for this lab. Let’s break down each one.

🔧 A. Domain Controller (EvilCorp-DC)
Create New VM (Custom/Advanced).

VM Settings:

OS: Windows Server 2019/2022 ISO.

Processors: 2 Cores.

RAM: 8GB.

Disk: 60GB (Recommended to allocate as one file).

Network Adapter: VMnet1 (Host-Only).

Name: EvilCorp-DC.

Install Windows Server (Follow prompts).

Once installed, rename the server to EvilCorp-DC.

Static IP Address Configuration:

Go to: Control Panel > Network and Sharing Center > Change adapter settings.

Right-click Ethernet0 > Properties > IPv4 > Properties.

Set:

IP Address: 10.0.100.10 (Example IP for your domain controller)

Subnet Mask: 255.255.255.0

Default Gateway: Leave Blank

Preferred DNS Server: 10.0.100.10

Click OK.

Install AD DS Role:

powershell
Copy
Edit
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
Promote to Domain Controller:

powershell
Copy
Edit
Install-ADDSForest -DomainName "evilcorp.local"
Reboot and Log back in.

🔧 B. Attacker Machine (Kali Linux)
Download ISO from: Kali.org

Create New VM (Custom/Advanced):

OS: Linux (Debian 10.x 64-bit).

Processors: 2 Cores.

RAM: 4GB.

Disk: 40GB.

Network Adapter 1: VMnet1 (Host-Only).

Network Adapter 2: VMnet8 (NAT) (For Internet Access).

Install Kali Linux (Standard Installation).

Update Kali Linux:

bash
Copy
Edit
sudo apt update && sudo apt upgrade -y
Install VMware Tools:

bash
Copy
Edit
sudo apt install open-vm-tools-desktop -y
Verify Network Interfaces:

bash
Copy
Edit
ip a
Configure Networking (If needed):

bash
Copy
Edit
sudo nano /etc/network/interfaces
Add:

bash
Copy
Edit
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet dhcp
Restart Networking:

bash
Copy
Edit
sudo systemctl restart networking
🔧 C. Monitoring System (Security Onion)
Download ISO from: Security Onion

Create New VM (Custom/Advanced):

Processors: 4 Cores.

RAM: 8GB.

Disk: 100GB.

Network Adapter: VMnet1 (Host-Only).

Install & Configure as Stand-Alone Sensor.

Set Static IP Address: 10.0.100.30 (Fake example IP for Security Onion).

🔧 D. Monitoring System (Wazuh)
Download Ubuntu Server ISO.

Create New VM (Custom/Advanced):

Processors: 2 Cores.

RAM: 4GB.

Disk: 80GB.

Network Adapter: VMnet1 (Host-Only).

Install Ubuntu Server.

Set Static IP Address: 10.0.100.40 (Fake example IP for Wazuh).

Install Wazuh Agent:

bash
Copy
Edit
curl -sO https://packages.wazuh.com/4.x/apt/wazuh-agent.deb
sudo apt install ./wazuh-agent.deb
Start Wazuh Service:

bash
Copy
Edit
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
🔧 E. Firewall & Network Management (OPNsense)
Download ISO from: OPNsense

Create New VM (Custom/Advanced):

Processors: 2 Cores.

RAM: 2GB.

Disk: 20GB.

Network Adapter 1: VMnet8 (NAT - WAN).

Network Adapter 2: VMnet1 (Host-Only - LAN).

Install OPNsense.

Set LAN IP Address: 10.0.100.1 (Example fake IP for OPNsense).

🔥 SOC Simulator Home Lab (Part 2: Network Configuration & File Server Setup)
✅ Step 5: Configuring OPNsense Firewall (Network Manager & Traffic Control)
🔨 Installing & Configuring OPNsense
Open VMware Workstation Pro 17.

Create New VM (Custom/Advanced):

OS: Other Linux 64-bit.

Processors: 2 Cores.

RAM: 2GB.

Disk Size: 20GB.

Network Adapters:

Adapter 1 (WAN): NAT (VMnet8).

Adapter 2 (LAN): Host-Only (VMnet1).

VM Name: OPNsense-Firewall.

🔨 Installing OPNsense (Inside the VM)
Boot from OPNsense ISO.

Select Install (default).

Follow the installer prompts:

Accept License Agreement.

Select Keyboard Layout (Default is fine).

Select Auto (UFS) Guided Disk Setup.

Reboot the VM after installation completes.

Log in with default credentials:

Username: root

Password: opnsense

🔨 Configuring Network Interfaces (Inside OPNsense VM)
When prompted, select:

WAN (Adapter 1 - VMnet8) → em0

LAN (Adapter 2 - VMnet1) → em1

Set LAN IP Address:

yaml
Copy
Edit
Interface: em1
IP Address: 10.0.100.1
Subnet Mask: 255.255.255.0 (/24)
Leave Gateway Empty & Hit Enter.

Configure DHCP for LAN: No.

Enable Web GUI Access: Yes.

Set HTTP Port: 443 (HTTPS).

🔨 Accessing OPNsense Web Interface (From Host Machine)
Open Browser:

cpp
Copy
Edit
https://10.0.100.1
Login Credentials:

Username: root

Password: opnsense

🔨 Creating Firewall Rules (To Control Traffic)
Navigate to: Firewall > Rules > LAN.

Click Add (+) to create a new rule.

Rule Settings:

Action: Allow.

Interface: LAN.

Protocol: Any.

Source: LAN net.

Destination: Any.

Click Save > Apply Changes.

🔨 Allowing Internet Access for Kali Linux VM (From VMnet1)
Go to Firewall > NAT > Outbound.

Set Mode to Hybrid Outbound NAT rule generation.

Click Add (+) under Manual Outbound NAT Rules.

Rule Settings:

Interface: WAN.

Source Network: 10.0.100.0/24 (Host-Only Network).

Translation Address: Interface Address.

Click Save > Apply Changes.

✅ Step 6: Setting Up the HP Envy Laptop as a File Server
🔨 Preparing the HP Envy Laptop (Physical Machine)
Set Static IP Address (VMnet0 - Bridged):

Go to Control Panel > Network and Sharing Center > Change Adapter Settings.

Right-click your Ethernet/Wi-Fi Adapter > Properties > Select Internet Protocol Version 4 (TCP/IPv4) > Properties.

Set:

IP Address: 10.0.0.50 (Example fake IP for your file server)

Subnet Mask: 255.255.255.0

Default Gateway: Your router’s IP (e.g., 10.0.0.1).

Preferred DNS Server: Your router’s IP (e.g., 10.0.0.1).

🔨 Creating a Shared Folder (EvilCorpFiles)
Create a Folder:

C:\EvilCorpFiles.

Right-click > Properties > Sharing > Advanced Sharing.

Check Share this folder > Permissions.

Set Everyone to Full Control > Click OK > OK.

🔨 Joining the EvilCorp Domain
Go to: Settings > Accounts > Access work or school > Connect.

Select: Join this device to a local Active Directory domain.

Enter Domain Name: evilcorp.local.

Enter Domain Admin Credentials (EvilCorp-DC).

Restart Laptop.

✅ Step 7: Creating Active Directory Structure & Security Groups
🔨 PowerShell Script (Create Security Groups & Organizational Units)
powershell
Copy
Edit
# Create-SecurityGroups.ps1
Import-Module ActiveDirectory

# Define Departments & Groups
$Departments = @("IT", "HR", "Finance", "Sales", "Marketing", "Engineering", "Operations")
$Groups = @("Admins", "Managers", "Users", "Guests")

# Create OUs if not exists
$OUs = @("Users", "Computers", "Groups")
foreach ($OU in $OUs) {
    if (-not (Get-ADOrganizationalUnit -Filter "Name -eq '$OU'")) {
        New-ADOrganizationalUnit -Name $OU -Path "DC=evilcorp,DC=local"
    }
}

# Create Groups
foreach ($Department in $Departments) {
    foreach ($Group in $Groups) {
        $GroupName = "$Department-$Group"
        if (-not (Get-ADGroup -Filter "Name -eq '$GroupName'")) {
            New-ADGroup -Name $GroupName -GroupScope Global -Path "OU=Groups,DC=evilcorp,DC=local"
            Write-Host "Created group: $GroupName"
        }
    }
}
✅ Step 8: Connecting All Systems
Ensure All VMs Can Ping Each Other (Use ping command).

Add Systems to Monitoring (Wazuh & Security Onion).

Verify Domain Connectivity:

powershell
Copy
Edit
nltest /dclist:evilcorp.local
Check Shared Folder Access:

From Kali Linux:

bash
Copy
Edit
smbclient -L //10.0.0.50 -U Administrator
