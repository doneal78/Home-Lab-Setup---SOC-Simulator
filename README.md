# Home-Lab-Setup---SOC-Simulator
Overview: This guide will show you how to create a realistic SOC Lab for learning Active Directory, Threat Detection, and Penetration Testing.

What We’re Building:
Active Directory Environment (Evil Corp)

Attacker Machine (Kali Linux)

Monitoring Systems (Security Onion & Wazuh)

Firewall & Network Management (OPNsense)

Step 1: Setting Up Your Virtual Lab Environment
Hardware Requirements:

Main Laptop (Running VMware Workstation Pro 17)

CPU: AMD Ryzen 7 4800H (8-core, 16 threads)

RAM: 16GB (Upgrade to 32GB recommended for better performance)

Storage: 1TB PCIe NVMe SSD

OS: Windows 11 Home

HP Envy Laptop (File Server)

OS: Windows 10 Pro or Windows Server 2019/2022

Step 2: Install VMware Workstation Pro 17
Download from VMware Official Site

Run the Installer, accept EULA, and continue the installation steps.

Step 3: Creating Virtual Networks
Network Settings

Network	Type	Subnet IP	Subnet Mask	DHCP
VMnet0	Bridged	(Uses physical NIC)	(Uses router settings)	Yes (Router DHCP)
VMnet1	Host-Only	192.168.1.0	255.255.255.0	Disabled
VMnet8	NAT	192.168.200.0	255.255.255.0	Enabled (Automatic)
Step 4: Setting Up Virtual Machines
A. Domain Controller (EvilCorp-DC)
Create New VM (Custom/Advanced).

VM Settings:

OS: Windows Server 2019/2022

IP Address: 192.168.1.10

Subnet Mask: 255.255.255.0

DNS Server: 192.168.1.10

B. Attacker Machine (Kali Linux)
Install Kali Linux.

Update Kali Linux:

bash
Copy
Edit
sudo apt update && sudo apt upgrade -y
Configure networking:

bash
Copy
Edit
sudo nano /etc/network/interfaces
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet dhcp
C. Monitoring System (Security Onion)
Install Security Onion on VM.

Set Static IP Address: 192.168.1.30

D. Monitoring System (Wazuh)
Install Ubuntu Server and Wazuh agent.

Set Static IP Address: 192.168.1.40

E. Firewall & Network Management (OPNsense)
Install OPNsense.

Set LAN IP Address: 192.168.1.1

Step 5: Configuring OPNsense Firewall (Network Manager & Traffic Control)
WAN (Adapter 1 - VMnet8) → em0

LAN (Adapter 2 - VMnet1) → em1

Set LAN IP Address:

bash
Copy
Edit
Interface: em1
IP Address: 192.168.1.1
Subnet Mask: 255.255.255.0
Step 6: File Server Setup on HP Envy Laptop
Set Static IP: 192.168.0.50

Create a Shared Folder: C:\EvilCorpFiles

Join the domain evilcorp.local.

Step 7: PowerShell Script to Create Security Groups
powershell
Copy
Edit
# Create-SecurityGroups.ps1
Import-Module ActiveDirectory

$Departments = @("IT", "HR", "Finance", "Sales", "Marketing", "Engineering", "Operations")
$Groups = @("Admins", "Managers", "Users", "Guests")

foreach ($OU in $OUs) {
    if (-not (Get-ADOrganizationalUnit -Filter "Name -eq '$OU'")) {
        New-ADOrganizationalUnit -Name $OU -Path "DC=evilcorp,DC=local"
    }
}

foreach ($Department in $Departments) {
    foreach ($Group in $Groups) {
        $GroupName = "$Department-$Group"
        New-ADGroup -Name $GroupName -GroupScope Global -Path "OU=Groups,DC=evilcorp,DC=local"
    }
}
Step 8: Verifying Connectivity
Ensure All VMs Can Ping Each Other:

bash
Copy
Edit
ping 192.168.1.10
Verify Domain Connectivity:

powershell
Copy
Edit
nltest /dclist:evilcorp.local
By replacing all sensitive data like real IP addresses, usernames, and passwords with placeholders, you make sure your lab setup is sharable without compromising privacy. Always ensure that any sensitive scripts, config files, or system information is properly anonymized before pushing it to public repositories.

Finally, push all the files to your GitHub repository using Git commands:

bash
Copy
Edit
git init
git add .
git commit -m "Initial commit of SOC simulator setup"
git remote add origin <your-repository-url>
git push -u origin master
