# Day 27 – Deploying Virtual Machines in a Private Virtual Network

---

## Task Overview

The Nautilus DevOps Team is expanding its Azure infrastructure by creating a private Virtual Network (VNet) environment to securely host internal resources. The goal is to deploy a Virtual Machine (VM) inside a private subnet and restrict SSH access so it is only accessible from within the VNet.

For this task, create the following resources:

| Resource               | Name                  |
| ---------------------- | --------------------- |
| Virtual Network        | `xfusion-priv-vnet`   |
| Subnet                 | `xfusion-priv-subnet` |
| Network Security Group | `xfusion-priv-nsg`    |
| Virtual Machine        | `xfusion-priv-vm`     |

Additional Requirements:

* Region: `Central US`
* VNet CIDR: `10.0.0.0/16`
* Allow SSH only within the VNet CIDR block
* Do NOT assign a Public IP to the VM

---

# Architecture Overview

```text id="v7p2m4"
Private Azure Infrastructure

xfusion-priv-vnet
      10.0.0.0/16
            │
            ▼
xfusion-priv-subnet
      10.0.1.0/24
            │
            ▼
xfusion-priv-vm
            │
            ▼
xfusion-priv-nsg
(Allow SSH only from 10.0.0.0/16)
```

---

# Step-by-Step Implementation Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="m4x8q1"
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="q9v2m5"
az group list --output table
```

Store the Resource Group name:

```bash id="t6p1x4"
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 3: Create Private Virtual Network and Subnet

Run:

```bash id="r2m8w5"
az network vnet create \
  --resource-group $RG_NAME \
  --name xfusion-priv-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name xfusion-priv-subnet \
  --subnet-prefix 10.0.1.0/24 \
  --location centralus
```

#### Explanation

Creates:

* Azure Virtual Network
* Private subnet
* Internal address space

---

### Step 4: Create Network Security Group (NSG)

Run:

```bash id="y5x2p7"
az network nsg create \
  --resource-group $RG_NAME \
  --name xfusion-priv-nsg \
  --location centralus
```

#### Explanation

Creates an NSG to control inbound and outbound network traffic.

---

### Step 5: Create NSG Rule for Internal SSH Access

Run:

```bash id="k1m7v4"
az network nsg rule create \
  --resource-group $RG_NAME \
  --nsg-name xfusion-priv-nsg \
  --name Allow-Internal-SSH \
  --priority 1000 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes 10.0.0.0/16 \
  --destination-address-prefixes 10.0.0.0/16 \
  --destination-port-ranges 22
```

#### Explanation

This rule allows SSH traffic only from resources within the VNet CIDR block.

---

### Step 6: Create Private Azure VM

Run:

```bash id="u8q3m6"
az vm create \
  --resource-group $RG_NAME \
  --name xfusion-priv-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location centralus \
  --vnet-name xfusion-priv-vnet \
  --subnet xfusion-priv-subnet \
  --nsg xfusion-priv-nsg \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-address "" \
  --storage-sku Standard_LRS
```

#### Explanation

This command:

* Creates the Virtual Machine
* Deploys the VM inside the private subnet
* Associates the NSG
* Disables Public IP assignment
* Uses Standard_LRS storage to comply with Azure lab policies

---

### Step 7: Verify VM Running State

Run:

```bash id="c4x9r2"
az vm list -d --output table
```

Expected Output:

| Name            | PowerState |
| --------------- | ---------- |
| xfusion-priv-vm | VM running |

#### Explanation

Confirms successful VM deployment.

---

### Step 8: Verify VM Has No Public IP

Run:

```bash id="n7m2p5"
az vm show \
  --resource-group $RG_NAME \
  --name xfusion-priv-vm \
  -d \
  --query publicIps
```

Expected Output:

```text id="z5v8q1"
null
```

#### Explanation

Confirms the VM is private and inaccessible directly from the internet.

---

### Step 9: Verify NSG Rule

Run:

```bash id="w3p6x9"
az network nsg rule list \
  --resource-group $RG_NAME \
  --nsg-name xfusion-priv-nsg \
  --output table
```

Expected Output:

| Name               | Port | Source      |
| ------------------ | ---- | ----------- |
| Allow-Internal-SSH | 22   | 10.0.0.0/16 |

#### Explanation

Verifies the NSG allows SSH access only within the private VNet.

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

| Setting       | Value               |
| ------------- | ------------------- |
| Name          | `xfusion-priv-vnet` |
| Region        | Central US          |
| Address Space | `10.0.0.0/16`       |

Create subnet:

| Setting       | Value                 |
| ------------- | --------------------- |
| Name          | `xfusion-priv-subnet` |
| Address Range | `10.0.1.0/24`         |

---

### Step 2: Create Network Security Group

Search:

| Search                  |
| ----------------------- |
| Network Security Groups |

Click:

* **+ Create**

Configure:

| Setting | Value              |
| ------- | ------------------ |
| Name    | `xfusion-priv-nsg` |
| Region  | Central US         |

---

### Step 3: Create NSG Rule

Add inbound rule:

| Setting     | Value         |
| ----------- | ------------- |
| Source      | `10.0.0.0/16` |
| Destination | `10.0.0.0/16` |
| Protocol    | TCP           |
| Port        | 22            |
| Action      | Allow         |

#### Explanation

Allows SSH traffic only within the VNet.

---

### Step 4: Create Virtual Machine

Search:

| Search           |
| ---------------- |
| Virtual Machines |

Click:

* **+ Create**
* **Azure Virtual Machine**

Configure:

| Setting        | Value             |
| -------------- | ----------------- |
| VM Name        | `xfusion-priv-vm` |
| Region         | Central US        |
| Image          | Ubuntu 22.04 LTS  |
| Size           | Standard_B1s      |
| Authentication | SSH Public Key    |

---

### Step 5: Configure Networking

Select:

| Setting   | Value                 |
| --------- | --------------------- |
| VNet      | `xfusion-priv-vnet`   |
| Subnet    | `xfusion-priv-subnet` |
| NSG       | `xfusion-priv-nsg`    |
| Public IP | None                  |

#### Explanation

Ensures the VM remains private and accessible only internally.

---

### Step 6: Review and Create

Click:

* **Review + Create**
* **Create**

#### Explanation

Azure provisions the private infrastructure resources.

---

# Quick Commands Only (Fastest Solution)

```bash id="v4m7q2"
RG_NAME=$(az group list --query '[0].name' -o tsv)

az network vnet create \
  --resource-group $RG_NAME \
  --name xfusion-priv-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name xfusion-priv-subnet \
  --subnet-prefix 10.0.1.0/24 \
  --location centralus

az network nsg create \
  --resource-group $RG_NAME \
  --name xfusion-priv-nsg \
  --location centralus

az network nsg rule create \
  --resource-group $RG_NAME \
  --nsg-name xfusion-priv-nsg \
  --name Allow-Internal-SSH \
  --priority 1000 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes 10.0.0.0/16 \
  --destination-address-prefixes 10.0.0.0/16 \
  --destination-port-ranges 22

az vm create \
  --resource-group $RG_NAME \
  --name xfusion-priv-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location centralus \
  --vnet-name xfusion-priv-vnet \
  --subnet xfusion-priv-subnet \
  --nsg xfusion-priv-nsg \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-address "" \
  --storage-sku Standard_LRS
```

---

# Best Practices

* Use private VNets for internal workloads
* Restrict SSH access using NSGs
* Avoid Public IPs for sensitive resources
* Use least-privilege network access policies
* Isolate workloads using subnets

---

# Key Learnings

* Azure VNets provide secure network isolation
* Private subnets improve infrastructure security
* NSGs control inbound and outbound traffic
* Private VMs reduce external attack surfaces
* Azure CLI simplifies secure infrastructure deployment
* Cloud networking security is a core DevOps skill
