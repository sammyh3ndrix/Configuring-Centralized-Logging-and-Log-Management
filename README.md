Lab: Configuring Centralized Logging and Log Management
In this lab, we will configure centralized logging between two Windows Server systems and explore concepts related to log management. This lab demonstrates how to use PowerShell to enable Windows Remote Management (WinRM), configure firewall rules, and create Event Viewer subscriptions to collect logs from a source computer to a centralized location.

Lab Objectives
This lab is designed to cover the following objectives:

Explain system and network architecture concepts in security operations.
Analyze indicators of malicious activity.
Use appropriate tools to determine malicious activity.
1. Understand the Environment
This lab will use three virtual machines:

DC10: A Windows Server 2019 VM acting as the log collector.
MS10: A Windows Server 2016 VM, which will act as the log source.
LAMP: Ubuntu Server (not covered in this lab).
Screenshot: Virtual Machines Setup
Include a screenshot of the virtual machine dashboard.

2. Configuring Centralized Logging
Step 1: Configure Windows Remote Management on DC10
Open PowerShell as an administrator on DC10.

Execute the following PowerShell script to enable WinRM and configure the Group Policy to accept connections from all IPs:

powershell
Copy code
Import-Module GroupPolicy
$gpo = Get-GPO -Name "cc-domain-default"
$winrmRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service"
Set-GPRegistryValue -Name $gpo.DisplayName -Key $winrmRegKey -ValueName "IPv4Filter" -Type String -Value "*"
gpupdate /force
Explanation:
This script modifies the default setting of the WinRM listener to accept connections from any IP address.

Screenshot: PowerShell Output
Include a screenshot of the PowerShell window after the command is executed.

Step 2: Enable Windows Event Collector Service
In the same PowerShell window, run the following command:

powershell
Copy code
wecutil qc
When prompted, enter Y to confirm.

Explanation:
This command configures the Windows Event Collector service to start with a delayed start mode, allowing it to collect logs from the source system.

Screenshot: Service Configuration
Include a screenshot of the output confirming the service has been successfully configured.

Step 3: Configure MS10 for Log Forwarding
Log in to MS10 and open PowerShell as an administrator.

Enable the required firewall rules for remote log management:

powershell
Copy code
Set-NetFirewallRule -DisplayGroup "Remote Event Log Management" -Enabled True -Profile Domain
Set-NetFirewallRule -DisplayGroup "Remote Event Monitor" -Enabled True -Profile Domain
Run the following command to ensure WinRM is configured:

powershell
Copy code
winrm quickconfig
Explanation:
These commands configure MS10 to allow remote access for event log management and monitoring through the Windows Firewall.

Screenshot: Firewall Rule Setup
Include a screenshot showing the commands executed and confirmation of the firewall rules enabled.

3. Configuring Event Viewer Subscription on DC10
On DC10, open Event Viewer and create a new subscription to collect logs from MS10:
Name the subscription "Logs from MS10".
Set the Collector Initiated option and add MS10 as the source computer.
Configure the log query to pull logs from the last 24 hours and select all event levels (Critical, Warning, Error, Information, Verbose).
Screenshot: Event Viewer Subscription
Include a screenshot showing the Event Viewer subscription setup.

4. Testing the Configuration
Once the subscription is active, logs from MS10 will start appearing in the Forwarded Events section of Event Viewer on DC10. You may need to refresh the Event Viewer to see the forwarded logs.

Here’s a full list of all the technologies used in the lab:

Operating Systems/Virtual Machines:
Windows Server 2019 (DC10 VM)
Windows Server 2016 (MS10 VM)
Ubuntu Server (LAMP VM)
Security and Network Management:
Windows Event Collector (WEC) – For centralized logging on Windows.
Windows Remote Management (WinRM) – Remote management service.
Firewall (Windows Defender Firewall) – Enabled for Remote Event Log Management and Remote Event Monitor.
Scripting/Configuration Tools:
PowerShell – Used for configuring WinRM and firewall rules.
Group Policy – Managed via PowerShell to change registry values for WinRM.
Set-GPRegistryValue PowerShell cmdlet.
Get-GPO PowerShell cmdlet.
Log Analysis and Management:
Event Viewer (Windows) – Used for monitoring and creating log subscriptions.
Subscriptions – For creating event log subscriptions (pulling logs from the MS10 VM to the DC10 VM).
Forwarded Events – Logs forwarded from MS10 VM to DC10 VM.
Network/Firewall Configuration:
Set-NetFirewallRule – PowerShell cmdlet used for enabling specific firewall rules.
Regular Expressions (Regex):
Grep Searching – Regex usage to locate IPv4 addresses or user activities within logs.
User and Group Management:
Computer Management (Windows) – Used to configure user and group access permissions.
Event Log Readers Group – Configured to allow DC10 to read logs from MS10.
This lab covers a range of technologies from log management, remote management, firewall configuration, and the use of scripting for automation.

Screenshot: Forwarded Events
Include a screenshot of the Forwarded Events log showing events from MS10.

Conclusion
This lab demonstrated how to configure centralized logging between Windows servers. By setting up WinRM, configuring firewall rules, and creating Event Viewer subscriptions, we enabled the collection and analysis of log data from multiple systems, which is essential for security monitoring and incident response.
