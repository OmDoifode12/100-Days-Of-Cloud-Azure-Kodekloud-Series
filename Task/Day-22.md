# Day 22 – Configuring Instances with User Data

---

## Task Overview

The Nautilus DevOps Team is setting up a new Azure Virtual Machine (VM) to host a web server for a critical application. The VM must automatically install and configure Nginx during deployment using User Data / Custom Script Extension.

For this task, create a VM with the following specifications:

| Property    | Value            |
| ----------- | ---------------- |
| VM Name     | `datacenter-vm`  |
| Image       | Any Ubuntu Image |
| VM Size     | Standard_B1s     |
| Web Server  | Nginx            |
| HTTP Access | Port 80 Allowed  |

Additional Requirements:

* Install the Nginx package automatically during VM creation
* Start the Nginx service automatically
* Configure Network Security Group (NSG) to allow HTTP traffic on Port 80

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft's web-based management interface used to manage Azure resources.

---

### Step 2: Navigate to Virtual Machines

Search for:

| Search           |
| ---------------- |
| Virtual Machines |

Click:

* **+ Create**
* **Azure Virtual Machine**

#### Explanation

This starts the VM deployment process.

---

### Step 3: Configure Basic VM Settings

Fill the following details:

| Setting             | Value                   |
| ------------------- | ----------------------- |
| Resource Group      | Existing Resource Group |
| VM Name             | `datacenter-vm`         |
| Region              | East US                 |
| Image               | Ubuntu 22.04 LTS        |
| Size                | Standard_B1s            |
| Authentication Type | SSH Public Key          |
| Username            | azureuser               |

#### Explanation

These settings define the VM operating system, size, and authentication method.

---

### Step 4: Configure SSH Key

Choose:

| Setting        | Value                 |
| -------------- | --------------------- |
| SSH Key Source | Generate New Key Pair |

Key Pair Name:

```text
datacenter-key
```

#### Explanation

Azure generates an SSH key pair for secure VM access.

---

### Step 5: Configure Inbound Ports

Under:

| Section            |
| ------------------ |
| Inbound Port Rules |

Select:

| Port      |
| --------- |
| SSH (22)  |
| HTTP (80) |

#### Explanation

SSH allows remote administration and HTTP allows web traffic to Nginx.

---

### Step 6: Add Custom Script (User Data)

Go to:

| Section  |
| -------- |
| Advanced |

Locate:

| Field       |
| ----------- |
| Custom Data |

Paste the following script:

```bash id="7x15m8"
#!/bin/bash
apt-get update -y
apt-get install nginx -y
systemctl enable nginx
systemctl start nginx
```

#### Explanation

This script automatically:

* Updates package repositories
* Installs Nginx
* Enables Nginx on boot
* Starts the Nginx service

---

### Step 7: Review and Create

Click:

* **Review + Create**
* **Create**

Download the SSH private key if prompted.

#### Explanation

Azure now provisions the VM and runs the custom script automatically during boot.

---

### Step 8: Verify VM Deployment

Navigate to:

| Service          |
| ---------------- |
| Virtual Machines |

Verify:

| Property | Expected Value |
| -------- | -------------- |
| VM Name  | datacenter-vm  |
| Status   | Running        |

#### Explanation

Confirms successful VM deployment.

---

### Step 9: Verify Nginx in Browser

Copy the Public IP address of the VM.

Open browser:

```text id="8zznt1"
http://PUBLIC_IP
```

Example:

```text id="t7umxh"
http://20.10.15.20
```

Expected Page:

```text
Welcome to nginx!
```

#### Explanation

This confirms that the custom script successfully installed and started Nginx.

---

### Step 10: Verify Nginx Service via SSH

SSH into the VM:

```bash id="e0jlwm"
ssh azureuser@PUBLIC_IP
```

Check service status:

```bash id="jlwm2p"
systemctl status nginx
```

Expected Output:

```text
active (running)
```

#### Explanation

Confirms that the Nginx service is running successfully.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="jlwm3a"
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Identify Resource Group

Run:

```bash id="jlwm3b"
az group list --output table
```

Store the Resource Group name:

```bash id="jlwm3c"
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group for easier command execution.

---

### Step 3: Create User Data Script

Run:

```bash id="jlwm3d"
cat <<EOF > nginx-script.sh
#!/bin/bash
apt-get update -y
apt-get install nginx -y
systemctl enable nginx
systemctl start nginx
EOF
```

#### Explanation

Creates a cloud-init bootstrap script for automatic Nginx installation.

---

### Step 4: Create Azure VM

Run:

```bash id="jlwm3e"
az vm create \
  --resource-group $RG_NAME \
  --name datacenter-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --custom-data nginx-script.sh \
  --storage-sku Standard_LRS
```

#### Explanation

Creates the VM and executes the custom script during provisioning.

---

### Step 5: Open HTTP Port

Run:

```bash id="jlwm3f"
az vm open-port \
  --resource-group $RG_NAME \
  --name datacenter-vm \
  --port 80
```

#### Explanation

Allows inbound HTTP traffic to access the Nginx web server.

---

### Step 6: Verify VM Running

Run:

```bash id="jlwm3g"
az vm list -d --output table
```

Expected Output:

| Name          | PowerState |
| ------------- | ---------- |
| datacenter-vm | VM running |

#### Explanation

Confirms the VM is successfully running.

---

### Step 7: Get Public IP

Run:

```bash id="jlwm3h"
az vm show \
  --resource-group $RG_NAME \
  --name datacenter-vm \
  -d \
  --query publicIps \
  -o tsv
```

#### Explanation

Retrieves the Public IP address of the VM.

---

### Step 8: Test Nginx

Open browser:

```text
http://PUBLIC_IP
```

Expected Page:

```text
Welcome to nginx!
```

#### Explanation

Confirms successful Nginx installation and HTTP access.

---

# Best Practices

* Automate server provisioning using User Data
* Use Infrastructure as Code whenever possible
* Open only required network ports
* Use SSH keys instead of passwords
* Validate services after deployment

---

# Key Learnings

* User Data automates VM configuration during provisioning
* Nginx is commonly used as a web server and reverse proxy
* Azure NSGs control inbound network traffic
* Custom Scripts simplify DevOps automation workflows
* Azure CLI accelerates infrastructure deployment
* Automated provisioning is essential in modern cloud environments
