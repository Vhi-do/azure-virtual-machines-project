##### Virtual Machine Setup and Remote Connection Learning Project

###### Project Overview
This project demonstrates the creation of 2 secure Azure Virtual Machines (VMs), a Windows VM and Linux VM, the configuration of network security groups, and the establishment of a remote desktop connection (RDP) and an SSH connection to access the Windows-based VM and Linux VM respectively.

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

---

## 5. Linux Virtual Machine Setup & SSH Connection

This section outlines the deployment of an Ubuntu Linux VM, the resolution of SSH key authentication, and the successful hosting of a custom web page using Nginx.

###### VM Deployment via Azure CLI
The Linux VM was provisioned using the following Azure CLI command:

**BASH**

```

az vm create \
  --resource-group rg-linux-ssh \
  --name linux-vm-olof \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --size Standard_B1s \
  --public-ip-sku Standard

  ```

  ###### Network Security Group (NSG) Configuration
To secure the SSH port (22) and the HTTP port (80), specific inbound rules were configured:

| Rule Name | Port | Source | Priority |
| :--- | :--- | :--- | :--- |
| default-allow-ssh | 22 | My IP Address | 1000 |
| AllowHTTP | 80 | Any (*) | 1010 |

###### SSH Key Authentication & Troubleshooting
During initial connection attempts, a `Permission denied (publickey)` error was encountered. This was resolved by generating a dedicated SSH key pair and manually injecting the public key into the VM using Azure's `RunShellScript` command.


###### SSH Key Authentication & Troubleshooting
During initial connection attempts, a `Permission denied (publickey)` error was encountered. This was resolved by generating a dedicated SSH key pair and manually injecting the public key into the VM using Azure's RunShellScript command.

```

# Generate a dedicated key pair
ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_vm_key -N ""

# Inject the key into the VM
az vm run-command invoke \
  --resource-group rg-linux-ssh \
  --name linux-vm-olof \
  --command-id RunShellScript \
  --scripts "mkdir -p /home/azureuser/.ssh && echo '$(cat ~/.ssh/azure_vm_key.pub)' >> /home/azureuser/.ssh/authorized_keys && chown -R azureuser:azureuser /home/azureuser/.ssh && chmod 700 /home/azureuser/.ssh && chmod 600 /home/azureuser/.ssh/authorized_keys"

# Successful connection command
ssh -i ~/.ssh/azure_vm_key azureuser@20.109.165.190

```

###### Application Deployment & Access
Once connected, the Nginx web server was installed to demonstrate administrative control over the VM:

**BASH**

```

sudo apt update
sudo apt install nginx -y

```

The default Nginx welcome page was customized directly in the VM's file system (/var/www/html) to display a personal message, proving full root access and web server configuration capabilities.

> Public IP Access: http://20.109.165.190