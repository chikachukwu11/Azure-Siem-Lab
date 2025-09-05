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

1. <b>Create an Azure Resource Group</b>
  - Log in to the Azure Portal
  - Create a new resource group (e.g., RG-SOCLab) in your local region.

2. <b>Set Up a Virtual Network (VNet)</b>
  - Within that resource group, create a VNet (e.g., VN-SOCLab).
  - Ensure the VNet uses the same Azure region as your resource group.

3. <b>Deploy a Windows VM</b>
  - Add a new VM:
      1. Choose the Windows 10 22H2 image.
      2. Set VM size to Standard_B1s.
  - In the Networking tab:
      1. Enable deletion of public IP and NIC when VM is removed (for cleanup).
      2. Connect the VM to VN-SOCLab.
  
<img src="https://i.imgur.com/3GVEkZG.png" height="80%" width="80%" alt="AzureSiemLab"/>

4. <b>Harden/Loosen Security (Intentionally Weak Setup)</b>
  - In the VM’s NSG inbound rules:
    1. Remove the default RDP restriction.
    2. Add a rule allowing all inbound traffic (Source: Any, Destination: Any, Protocol: Any).
    3. Name it something like DANGER_AllowAnyCustomAnyInbound.

5. <b>Disable Windows Firewall</b>
  - Log in to the VM (via RDP using your credentials).
  - Open wf.msc (Windows Firewall settings) and turn off all profiles (Domain, Private, Public).

6. <b>Generate Failed Login Events</b>
  - In the VM, attempt several failed RDP logins with incorrect credentials.
  - Use Event Viewer to confirm Event ID 4625 (failed login entries) logs are being logged.

7. <b>Connect Azure Sentinel</b>
  - Navigate to Azure Sentinel and attach it to your Log Analytics Workspace.
  - Install the Windows Security Events from the Content Hub via the AMA connector.
  - Enable data collection for your VM.

8. <b>Upload Geo‑IP Watchlist</b>
  - Download or prepare a CSV that maps IP addresses to geolocation (latitude/longitude, city, country).
  - Create a watchlist in Sentinel named geoip and upload the CSV.

9. <b>Run Geolocation KQL Query</b>
  - In Log Analytics, run a query that.
    1. References the GeoIP watchlist.
    2. Filters SecurityEvent for Event ID 4625.
    3. Performs an IPv4 lookup to enrich IPs with geolocation.
  
  Example query snippet:
  <br />
  let GeoIPDB_FULL = _GetWatchlist("geoip");
  <br />
  SecurityEvent
  <br />
  | where EventID == 4625
  <br />
  | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)

10. <b>Build a Visual Workbook (Map)</b>
  - Create a new workbook or edit a custom one.
  - Add the KQL with summary and map visualization: count failures per IP and map coordinates.
  - Use the JSON advanced editor to configure the map layout, color scale, and labels.

11. <b>Observe and Analyze</b>
  - Let the system run for 30 minutes to accumulate data.
  - Observe attacker locations popping up on the map.
  - Optionally, review again after 24 hours to see the expansion of data. 
  
