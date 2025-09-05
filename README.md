# Azure-Siem-Lab
<h2>Description</h2>
This lab involves me building a cloud-based Security Operations Center (SOC) using a free Azure subscription and Microsoft Sentinel. The objective is to simulate a real-world environment where I can ingest security logs, analyze failed login attempts, and visualize attacker geolocation in near‑real time. By completing this lab, I learned how to set up cloud infrastructure, configure logging and SIEM tools, and create visual insights on attack activity—all within a controlled, self‑contained environment.
<br />

<h2>Enviroments and Utilities</h2>

<b>Cloud Enviroment:</b>
  - <b>Platform:</b> Microsoft Azure.
  - <b>Resource Group:</b> A container (e.g., RG-SOCLab) holding related assets like VMs, networks, and NSG (Network Security Group).

<b>Virtual Machines & Network:</b>
  - <b>Virtual Machine:</b> Windows 10 (22H2).
  - <b>Virtual Network (VNet):</b> Custom within the same resource group/region.
  - <b>Network Security Group (NSG):</b> Modified to allow all inbound traffic (creating vulnerability intentionally). 

<b>Logging & SIEM Tool:</b>
  - <b>Windows Firewall / Security Logs:</b> Configured to capture failed login attempts via Event Viewer using Event ID 4625.
  - <b>Azure Sentinel:</b> Set up to ingest Windows Security Events (via Azure Monitor Agent) into a Log Analytics Workspace.

<b>Geolocation Data:</b>
  - <b>Watchlist:</b> A pre‑downloaded CSV of geo‑IP data uploaded into Sentinel as a watchlist (named geoip).
  - <b>Kusto Query Language (KQL):</b> Used to join security events with geolocation data and visualize it on a map.

<h2>Project Setup</h2>

1. Create an Azure Resource Group
  - Log in to the Azure Portal
  - Create a new resource group (e.g., RG-SOCLab) in your local region.
<br />
2. Set Up a Virtual Network (VNet)
  - Within that resource group, create a VNet (e.g., VN-SOCLab).
  - Ensure the VNet uses the same Azure region as your resource group.
<br />
3. Deploy a Windows VM
  - Add a new VM:
      1. Choose the Windows 10 22H2 image.
      2. Set VM size to Standard_B1s.
  - In the Networking tab:
      1. Enable deletion of public IP and NIC when VM is removed (for cleanup).
      2. Connect the VM to VN-SOCLab.
  <br />
  <img src="https://imgur.com/a/Lf7W1TQ" height="80%" width="80%" alt="AzureSiemLab"/>
  
