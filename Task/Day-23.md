# Day 23 – Automating User Data Configuration Using the CLI

---

## Task Overview

The Nautilus DevOps Team is working on setting up a new Azure Virtual Machine (VM) to host a web server for a critical application. The VM must automatically install and configure Nginx during deployment using Azure CLI and User Data (cloud-init script).

For this task, create a VM with the following specifications:

| Property    | Value           |
| ----------- | --------------- |
| VM Name     | `devops-vm`     |
| Image       | Ubuntu          |
| Region      | East US         |
| VM Size     | Standard_B1s    |
| Web Server  | Nginx           |
| HTTP Access | Port 80 Enabled |

Additional Requirements:

* Install the Nginx package automatically during VM creation
* Start the Nginx service automatically
* Allow HTTP traffic on Port 80 from the internet
* Use Azure CLI commands to complete the setup

---

# Step-by-Step Implementation Using Azure CLI

### Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash
az group list --output table
```

Store the Resource Group name:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 3: Create User Data Script

Create a cloud-init script:

```bash
cat <<EOF > nginx-script.sh
#!/bin/bash
apt-get update -y
apt-get install nginx -y
systemctl enable nginx
systemctl start nginx
EOF
```

#### Explanation

This script automatically:

* Updates package repositories
* Installs Nginx
* Enables Nginx on boot
* Starts the Nginx service

---

### Step 4: Create Azure VM

Run:

```bash
az vm create \
  --resource-group $RG_NAME \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location eastus \
  --admin-username azureuser \
  --generate-ssh-keys \
  --custom-data nginx-script.sh \
  --storage-sku Standard_LRS
```

#### Explanation

This command:

* Creates the Virtual Machine
* Uses Ubuntu 22.04 image
* Generates SSH keys automatically
* Executes the custom script during deployment
* Uses Standard_LRS storage to comply with Azure lab policies

---

### Step 5: Allow HTTP Traffic on Port 80

Run:

```bash
az vm open-port \
  --resource-group $RG_NAME \
  --name devops-vm \
  --port 80
```

#### Explanation

Updates the Network Security Group (NSG) to allow inbound HTTP traffic.

---

### Step 6: Verify VM Running State

Run:

```bash
az vm list -d --output table
```

Expected Output:

| Name      | PowerState |
| --------- | ---------- |
| devops-vm | VM running |

#### Explanation

Confirms successful VM deployment.

---

### Step 7: Get Public IP Address

Run:

```bash
az vm show \
  --resource-group $RG_NAME \
  --name devops-vm \
  -d \
  --query publicIps \
  -o tsv
```

Example Output:

```text
20.x.x.x
```

#### Explanation

Retrieves the Public IP address assigned to the VM.

---

### Step 8: Test Nginx Web Server

Open browser:

```text
http://PUBLIC_IP
```

Example:

```text
http://20.10.15.20
```

Expected Page:

```text
Welcome to nginx!
```

#### Explanation

Confirms that Nginx was installed successfully and is accessible from the internet.

---

### Step 9: Verify Nginx Service via SSH

SSH into the VM:

```bash
ssh azureuser@PUBLIC_IP
```

Check Nginx service:

```bash
systemctl status nginx
```

Expected Output:

```text
active (running)
```

#### Explanation

Confirms the Nginx service is running correctly.

---

# Quick Commands Only (Fastest Solution)

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

cat <<EOF > nginx-script.sh
#!/bin/bash
apt-get update -y
apt-get install nginx -y
systemctl enable nginx
systemctl start nginx
EOF

az vm create \
  --resource-group $RG_NAME \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location eastus \
  --admin-username azureuser \
  --generate-ssh-keys \
  --custom-data nginx-script.sh \
  --storage-sku Standard_LRS

az vm open-port \
  --resource-group $RG_NAME \
  --name devops-vm \
  --port 80
```

---

# Best Practices

* Automate server configuration using User Data
* Use Infrastructure as Code wherever possible
* Open only required ports in NSGs
* Use SSH keys instead of passwords
* Validate service status after deployment

---

# Key Learnings

* Azure CLI automates infrastructure deployment
* User Data (cloud-init) automates VM configuration
* Nginx is commonly used as a web server and reverse proxy
* NSGs control inbound network traffic
* Infrastructure automation is a core DevOps practice
* Cloud provisioning should be automated for consistency and scalability
