# Day 21 – Assigning Public IP to Virtual Machines

---

## Task Overview

The Nautilus DevOps Team received a request from the Development Team to deploy a new Azure Virtual Machine (VM) that requires a stable public IP address for reliable external access.

For this task, create an Azure VM with the following requirements:

| Resource        | Name             |
| --------------- | ---------------- |
| Virtual Machine | `nautilus-vm`    |
| Public IP       | `nautilus-pip`   |
| Region          | `Central US`     |
| VM Size         | `Standard_B1s`   |
| Image           | Any Ubuntu Image |

Additional Requirements:

* Generate an SSH key on the `azure-client` host
* Associate the SSH public key with the VM
* Use a **Static Public IP**
* Ensure SSH access works successfully

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open:

https://portal.azure.com

Login using the provided Azure credentials.

#### Explanation

The Azure Portal is Microsoft's web-based interface for creating and managing Azure resources.

---

### Step 2: Create Public IP Address

Search for:

| Search              |
| ------------------- |
| Public IP addresses |

Open the service and click:

* **+ Create**

Configure:

| Setting    | Value          |
| ---------- | -------------- |
| Name       | `nautilus-pip` |
| Region     | Central US     |
| SKU        | Standard       |
| Assignment | Static         |

Click:

* **Review + Create**
* **Create**

#### Explanation

A Static Public IP ensures the VM always retains the same IP address.

---

### Step 3: Generate SSH Key

On the `azure-client` host, run:

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
```

Verify:

```bash
cat ~/.ssh/id_rsa.pub
```

#### Explanation

This generates an RSA SSH key pair for secure password-less authentication.

---

### Step 4: Create Virtual Machine

Search for:

| Search           |
| ---------------- |
| Virtual Machines |

Click:

* **+ Create**
* **Azure Virtual Machine**

Configure:

| Setting             | Value            |
| ------------------- | ---------------- |
| VM Name             | `nautilus-vm`    |
| Region              | Central US       |
| Image               | Ubuntu 22.04 LTS |
| Size                | Standard_B1s     |
| Authentication Type | SSH Public Key   |
| Username            | azureuser        |

Paste the contents of:

```bash
~/.ssh/id_rsa.pub
```

into the SSH key field.

#### Explanation

This configures SSH authentication using the generated public key.

---

### Step 5: Configure Networking

Under Networking settings:

Select the Public IP:

| Setting   | Value          |
| --------- | -------------- |
| Public IP | `nautilus-pip` |

Allow:

| Port     |
| -------- |
| SSH (22) |

#### Explanation

This associates the Static Public IP with the VM and allows SSH access.

---

### Step 6: Review and Create

Click:

* **Review + Create**
* **Create**

Download the private key if prompted.

#### Explanation

Azure now provisions the VM, NIC, NSG, and Public IP association.

---

### Step 7: Verify VM Running State

Navigate to:

| Service          |
| ---------------- |
| Virtual Machines |

Verify:

| Property | Expected Value |
| -------- | -------------- |
| VM Name  | nautilus-vm    |
| Status   | Running        |

#### Explanation

This confirms successful VM deployment.

---

### Step 8: Verify Static Public IP

Open:

| Resource     |
| ------------ |
| nautilus-pip |

Verify:

| Property   | Expected Value |
| ---------- | -------------- |
| Assignment | Static         |

#### Explanation

Ensures the Public IP remains unchanged after reboots.

---

### Step 9: Verify SSH Access

Get the Public IP address from the VM Overview page.

Run:

```bash
ssh azureuser@PUBLIC_IP
```

Example:

```bash
ssh azureuser@20.10.15.20
```

Expected Output:

```bash
azureuser@nautilus-vm:~$
```

#### Explanation

This confirms successful SSH authentication using the generated SSH key.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Identify Resource Group

Run:

```bash
az group list --output table
```

Store the Resource Group:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 3: Generate SSH Key

Run:

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
```

#### Explanation

Generates SSH keys for VM authentication.

---

### Step 4: Create Static Public IP

Run:

```bash
az network public-ip create \
  --resource-group $RG_NAME \
  --name nautilus-pip \
  --sku Standard \
  --allocation-method Static \
  --location centralus
```

#### Explanation

Creates a Static Public IP resource.

---

### Step 5: Create Virtual Machine

Run:

```bash
az vm create \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --public-ip-address nautilus-pip \
  --location centralus \
  --storage-sku Standard_LRS
```

#### Explanation

Creates the VM and associates the Static Public IP with it.

---

### Step 6: Verify VM Status

Run:

```bash
az vm list -d --output table
```

Expected Output:

| Name        | PowerState |
| ----------- | ---------- |
| nautilus-vm | VM running |

#### Explanation

Confirms the VM is successfully running.

---

### Step 7: Verify Public IP Assignment

Run:

```bash
az network public-ip show \
  --resource-group $RG_NAME \
  --name nautilus-pip \
  --query publicIPAllocationMethod
```

Expected Output:

```text
"Static"
```

#### Explanation

Confirms the Public IP is statically assigned.

---

### Step 8: Verify SSH Connectivity

Get Public IP:

```bash
az vm show \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  -d \
  --query publicIps \
  -o tsv
```

SSH into the VM:

```bash
ssh azureuser@PUBLIC_IP
```

#### Explanation

Verifies successful SSH access using the generated SSH key.

---

# Best Practices

* Use Static Public IPs for production workloads
* Prefer SSH keys over passwords
* Restrict inbound SSH access in production environments
* Use NSGs to secure network traffic
* Apply least-privilege access principles

---

# Key Learnings

* Azure Public IPs provide external connectivity to VMs
* Static Public IPs maintain consistent addresses
* SSH keys improve VM security
* Azure CLI simplifies VM provisioning
* NSGs control inbound network traffic
* Cloud networking is a foundational Azure skill
