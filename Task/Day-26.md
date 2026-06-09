# Day 26 – Deploying Virtual Machines in a Public Virtual Network

---

## Task Overview

The Nautilus DevOps Team received a request from the Networking Team to deploy a new Azure Virtual Machine (VM) inside a public Virtual Network (VNet). This setup will host public-facing applications accessible from the internet.

For this task, create the following resources:

| Resource        | Name                |
| --------------- | ------------------- |
| Virtual Network | `devops-pub-vnet`   |
| Subnet          | `devops-pub-subnet` |
| Virtual Machine | `devops-pub-vm`     |

Additional Requirements:

* Deploy all resources in the **East US** region
* Ensure the VM receives a Public IP automatically
* Allow inbound SSH access on Port 22
* Verify SSH connectivity from the internet

---

# Architecture Overview

```text id="n7m2x5"
Internet
    │
    ▼
Public IP
    │
    ▼
Network Security Group
(Allow SSH Port 22)
    │
    ▼
devops-pub-vnet
        │
        ▼
devops-pub-subnet
        │
        ▼
devops-pub-vm
```

---

# Step-by-Step Implementation Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="t4x9m2"
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="y6q1p8"
az group list --output table
```

Store the Resource Group name:

```bash id="r2v7m5"
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 3: Create Virtual Network and Subnet

Run:

```bash id="p8m4x1"
az network vnet create \
  --resource-group $RG_NAME \
  --name devops-pub-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name devops-pub-subnet \
  --subnet-prefix 10.0.1.0/24 \
  --location eastus
```

#### Explanation

This command creates:

* Azure Virtual Network
* Public subnet
* Address space configuration

---

### Step 4: Create Azure Virtual Machine

Run:

```bash id="c5t8q2"
az vm create \
  --resource-group $RG_NAME \
  --name devops-pub-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location eastus \
  --vnet-name devops-pub-vnet \
  --subnet devops-pub-subnet \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-sku Standard \
  --storage-sku Standard_LRS
```

#### Explanation

This command:

* Creates the Virtual Machine
* Attaches the VM to the custom VNet and subnet
* Automatically assigns a Public IP
* Creates a Network Security Group
* Uses Standard_LRS storage to comply with Azure lab policies

---

### Step 5: Allow SSH Access on Port 22

Run:

```bash id="m1w7r4"
az vm open-port \
  --resource-group $RG_NAME \
  --name devops-pub-vm \
  --port 22
```

#### Explanation

Updates the NSG to allow inbound SSH traffic from the internet.

---

### Step 6: Verify VM Running State

Run:

```bash id="x9p3t6"
az vm list -d --output table
```

Expected Output:

| Name          | PowerState |
| ------------- | ---------- |
| devops-pub-vm | VM running |

#### Explanation

Confirms successful VM deployment.

---

### Step 7: Get Public IP Address

Run:

```bash id="v2m8q5"
az vm show \
  --resource-group $RG_NAME \
  --name devops-pub-vm \
  -d \
  --query publicIps \
  -o tsv
```

Example Output:

```text id="q4x7n1"
20.x.x.x
```

#### Explanation

Retrieves the Public IP assigned to the VM.

---

### Step 8: Verify SSH Connectivity

Run:

```bash id="j8r2m4"
ssh azureuser@PUBLIC_IP
```

Example:

```bash id="t5p9x3"
ssh azureuser@20.10.15.20
```

Expected Output:

```bash id="w7m1q6"
azureuser@devops-pub-vm:~$
```

#### Explanation

Confirms successful SSH access from the internet.

---

# Azure Portal UI Method

### Step 1: Create Virtual Network

Search:

| Search           |
| ---------------- |
| Virtual Networks |

Click:

* **+ Create**

Configure:

| Setting       | Value             |
| ------------- | ----------------- |
| Name          | `devops-pub-vnet` |
| Region        | East US           |
| Address Space | `10.0.0.0/16`     |

Create Subnet:

| Setting       | Value               |
| ------------- | ------------------- |
| Name          | `devops-pub-subnet` |
| Address Range | `10.0.1.0/24`       |

---

### Step 2: Create Virtual Machine

Search:

| Search           |
| ---------------- |
| Virtual Machines |

Click:

* **+ Create**
* **Azure Virtual Machine**

Configure:

| Setting        | Value            |
| -------------- | ---------------- |
| VM Name        | `devops-pub-vm`  |
| Region         | East US          |
| Image          | Ubuntu 22.04 LTS |
| Size           | Standard_B1s     |
| Username       | azureuser        |
| Authentication | SSH Public Key   |

---

### Step 3: Configure Networking

Select:

| Setting   | Value               |
| --------- | ------------------- |
| VNet      | `devops-pub-vnet`   |
| Subnet    | `devops-pub-subnet` |
| Public IP | Enabled             |

Allow inbound:

| Port     |
| -------- |
| SSH (22) |

#### Explanation

Configures internet accessibility and SSH connectivity.

---

### Step 4: Review and Create

Click:

* **Review + Create**
* **Create**

#### Explanation

Azure provisions the VM and networking resources.

---

### Step 5: Verify SSH Connectivity

Run:

```bash id="n6x4r8"
ssh azureuser@PUBLIC_IP
```

#### Explanation

Verifies successful internet connectivity and SSH access.

---

# Quick Commands Only (Fastest Solution)

```bash id="r3m8x2"
RG_NAME=$(az group list --query '[0].name' -o tsv)

az network vnet create \
  --resource-group $RG_NAME \
  --name devops-pub-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name devops-pub-subnet \
  --subnet-prefix 10.0.1.0/24 \
  --location eastus

az vm create \
  --resource-group $RG_NAME \
  --name devops-pub-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location eastus \
  --vnet-name devops-pub-vnet \
  --subnet devops-pub-subnet \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-sku Standard \
  --storage-sku Standard_LRS

az vm open-port \
  --resource-group $RG_NAME \
  --name devops-pub-vm \
  --port 22
```

---

# Best Practices

* Use NSGs to control inbound traffic
* Restrict SSH access in production environments
* Use custom VNets for network isolation
* Use Standard_LRS storage for lab environments
* Verify internet accessibility after deployment

---

# Key Learnings

* Azure VNets provide network isolation
* Subnets logically separate cloud resources
* Public IPs enable internet access
* NSGs secure inbound traffic
* Azure CLI automates networking and VM deployment
* Cloud networking is a foundational DevOps skill
