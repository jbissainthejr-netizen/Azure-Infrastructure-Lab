# Azure Infrastructure Lab

## Overview

This project involved building and administering a small Microsoft Azure cloud environment from scratch.

The goal was to gain hands-on experience with core Azure infrastructure concepts while applying skills related to cloud administration, Windows Server, networking, security, monitoring, access control, storage, and troubleshooting.

The environment was built around a Windows Server virtual machine connected to an Azure Virtual Network and later expanded with monitoring, centralized logging, RBAC, governance controls, and Azure Blob Storage.

**Project workflow:**

Deploy → Configure → Secure → Monitor → Troubleshoot → Govern → Store → Validate

---

## Technologies and Azure Services

- Microsoft Azure
- Azure Virtual Machines
- Windows Server 2022 Datacenter: Azure Edition
- Azure Virtual Network
- Subnets
- Network Security Groups
- Public and Private IP Addressing
- Azure Monitor
- Azure Monitor Agent
- Log Analytics
- Data Collection Rules
- Kusto Query Language (KQL)
- Azure Role-Based Access Control (RBAC)
- Resource Locks
- Azure Tags
- Azure Storage
- Blob Storage
- Microsoft Storage Service Endpoints
- PowerShell
- Remote Desktop Protocol (RDP)

---

## Architecture

The lab environment included:

- Resource Group: `AZ900-Lab-RG`
- Virtual Network: `AZ900-Lab-VNet-West`
- Address Space: `10.0.0.0/16`
- Default Subnet: `10.0.0.0/24`
- Windows Server VM: `AZ900-Lab-VM`
- Private VM IP: `10.0.0.4`
- Network Security Group
- Public IP for remote administration
- Log Analytics Workspace: `AZ900-Lab-Logs`
- Windows Event Log Data Collection Rule: `AZ900-Windows-EventLogs-DCR`
- Storage Account: `az900labstorage01`
- Private Blob Storage container

---

## Project Highlights

### Azure Infrastructure Deployment

I created the Azure environment from scratch, including the resource group, virtual network, subnet, Windows Server virtual machine, network interface, public IP address, Network Security Group, and managed operating system disk.

The VM was deployed using Windows Server 2022 Datacenter: Azure Edition and connected to the lab VNet and subnet.

---

### Networking and Remote Administration

I configured the lab network using:

- VNet address space: `10.0.0.0/16`
- Subnet range: `10.0.0.0/24`
- VM private IP: `10.0.0.4`
- Default gateway: `10.0.0.1`

I connected to the server using Remote Desktop and used PowerShell commands such as:

```powershell
ipconfig
hostname
```
to verify the Windows networking configuration matched the Azure configuration.
## RDP Security Hardening
The original RDP rule allowed TCP port 3389 from any public IP address.
I updated the Network Security Group rule so that RDP access was only permitted from my current public IP address.
This reduced unnecessary exposure of the VM while still allowing remote administration.
## Monitoring and Centralized Logging
I configured Azure Monitor to review VM performance metrics including:
- CPU utilization
- Network traffic
- Disk activity
- Memory-related telemetry
- System uptime
I also enabled the Azure Monitor Agent and configured Data Collection Rules for the VM.
A Log Analytics workspace named:
AZ900-Lab-Logs
was created to centralize Windows Event Log data.
A separate Data Collection Rule named:
AZ900-Windows-EventLogs-DCR
was configured to collect selected Windows System and Application events, including:
- Critical
- Error
- Warning
## End-to-End Log Validation
To verify that centralized logging was working correctly, I generated a test Windows Application error event from the VM:
eventcreate /T ERROR /ID 100 /L APPLICATION /D "AZ900 lab test event"

I then queried Log Analytics using KQL:
Event
| where TimeGenerated > ago(30m)
| where RenderedDescription contains "AZ900 lab test event"
| project TimeGenerated, Computer, EventLog, EventLevelName, EventID, Source, RenderedDescription
| order by TimeGenerated desc

The event was successfully returned in Log Analytics.
This validated the complete logging path:
Windows Server VM → Windows Event Log → Azure Monitor Agent → Data Collection Rule → Log Analytics → KQL
## Troubleshooting Experience
VM Availability by Region
During the initial deployment, the selected VM size was unavailable for my subscription in East US.
Azure returned:
NotAvailableForSubscription
I tested other regions and determined that the same VM size was available in West US 2.
I then created a new West US 2 VNet and deployed the working environment there.
This demonstrated how Azure region selection can affect resource and VM SKU availability.
Azure Monitor Onboarding Failure
Azure Monitor onboarding initially failed because the required Microsoft.Insights resource provider was not properly registered.
I identified the issue through the deployment error, checked the subscription's Resource Providers, confirmed the required provider registration, and retried the deployment successfully.
This demonstrated the importance of subscription-level resource providers when deploying Azure services.
## Identity and Access Management
I reviewed Azure Role-Based Access Control and confirmed that my account had the Owner role inherited from the subscription level.
I also compared several Azure roles, including:
- Owner
- Reader
- Virtual Machine Contributor
This helped reinforce the relationship between:
- identity
- roles
- permissions
- scope
- inheritance
- least privilege
For example, Virtual Machine Contributor can manage virtual machines without automatically providing access to the guest operating system or control over connected networking and storage resources.
## Azure Governance
### Resource Lock
I created a Delete lock named:
AZ900-Lab-DeleteLock
at the resource group level.
This helps prevent accidental deletion of protected lab resources.
### Tags
I added organizational metadata using Azure tags, including:
Environment = Lab
Project = AZ900
Tags were applied to both the resource group and the virtual machine.
## Azure Storage
I created an Azure Storage Account using:
- Standard performance
- Locally Redundant Storage (LRS)
- Azure Blob Storage
The storage account was configured with network access limited to:
- My approved public IP address
- AZ900-Lab-VNet-West
- The selected subnet
A Microsoft Storage service endpoint was added to the subnet.
Security settings included:
- Secure transfer required
- Anonymous blob access disabled
- Minimum TLS version 1.2
- Microsoft-managed encryption keys
I then created a private Blob Storage container and successfully uploaded a test text file to verify storage functionality.
## Security Controls Implemented
The completed environment included several security controls:
- RDP restricted to an approved public IP
- Network Security Group filtering
- Microsoft Defender Firewall enabled on Windows Server
- Microsoft Defender Antivirus real-time protection enabled
- Anonymous Blob Storage access disabled
- Secure transfer required for storage
- TLS 1.2 minimum
- Storage networking restricted to approved sources
- Azure RBAC
- Resource group Delete lock
- Azure Monitor and centralized event logging
## Skills Demonstrated
This project provided hands-on experience with:
- Azure infrastructure deployment
- Windows Server administration
- Virtual networking
- IP addressing and subnetting
- Network Security Groups
- Remote administration
- Cloud security
- Azure monitoring
- Centralized Windows logging
- KQL
- Azure RBAC
- Least privilege
- Azure governance
- Resource tagging
- Resource locks
- Azure Storage
- Blob Storage
- Service endpoints
- Troubleshooting
- Technical documentation
## Key Takeaways
This project helped move my Azure experience beyond certification study and into hands-on administration.
I gained practical experience deploying and managing Azure infrastructure, securing network access, administering a Windows Server VM, configuring monitoring and logging, troubleshooting deployment issues, applying governance controls, and validating cloud storage.
The project also reinforced the connection between Azure infrastructure, Windows administration, networking, security, and operational monitoring.
## Screenshots
Recommended screenshots for this repository:
1. Resource group showing the deployed Azure resources
2. VM overview page
3. VM Network Security Group showing restricted RDP access
4. Windows Server / PowerShell network verification
5. Azure Monitor metrics
6. Log Analytics query showing the test Windows event
7. Azure RBAC / IAM view
8. Resource group Delete lock
9. VM tags
10. Storage account networking restrictions
11. Blob container with uploaded test file
