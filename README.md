##### Virtual Machine Setup and Remote Connection Learning Project

###### Project Overview
This project demonstrates the creation of a secure Azure Virtual Machine (VM), the configuration of network security groups, and the establishment of a remote desktop connection (RDP) to access a Windows-based VM.

The goal is to simulate real-world cloud administration tasks, focusing on connectivity, security rule enforcement, and secure access management using Azure Portal and CLI.

###### Technologies Used
- Microsoft Azure Portal
- Azure CLI (Command Line Interface)
- Windows Remote Desktop Protocol (RDP)
- Network Security Groups (NSGs)
- Public IP and DNS Management

###### Implementation Steps

###### 1. VM Deployment via Azure CLI
*A Windows Server 2019 Datacenter VM was created using the Azure CLI command below:*

**BASH**

```

az vm create
  --resource-group rg-dev-web-westus2-001 
  --name win-vm-olof 
  --image Win2019Datacenter 
  --admin-username azureuser 
  --admin-password '**********' 
  --size Standard_B2s_v2 
  --public-ip-sku Standard

```

###### 2. Network Security Group (NSG) Configuration

To secure the VM, an Inbound Security Rule was created to restrict RDP (Remote Desktop Protocol) access to only the administrator's specific public IP address.

**Rule Details:**

| Field | Value |
| :--- | :--- |
| **Service** | RDP |
| **Protocol** | TCP |
| **Destination Port Range** | 3389 |
| **Source** | My IP Address (Private IP whitelisted) |
| **Action** | Allow |
| **Priority** | 1000 |

<br>

## 3. Public IP & DNS Configuration

The VM's public IP was upgraded to **Static** to ensure it never changes. A friendly DNS label was assigned for easy access.

- **DNS Name:** `win-vm-olof.westus2.cloudapp.azure.com`
- **Public IP:** `20.98.95.201`

<br>

## 4. Remote Desktop Connection (RDP) Success

A remote desktop connection was initiated from the local Windows machine using the assigned DNS name. The connection was successful, and full administrative access to the VM desktop GUI was verified via the Windows Remote Desktop Client.