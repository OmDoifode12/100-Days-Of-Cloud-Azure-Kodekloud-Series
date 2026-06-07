# Day 24 – Securing Virtual Machine SSH Access

---

## Task Overview

The Nautilus DevOps Team needs to set up a secure Azure Virtual Machine (VM) that can be accessed from the `azure-client` host using SSH key-based authentication.

For this task, create a VM with the following specifications:

| Property              | Value          |
| --------------------- | -------------- |
| VM Name               | `nautilus-vm`  |
| Region                | `westus`       |
| VM Size               | `Standard_B1s` |
| Username              | `azureuser`    |
| Authentication Method | SSH Key        |

Additional Requirements:

* Generate an SSH key on the `azure-client` host if it does not already exist
* Configure the VM to use SSH public key authentication
* Enable password-less SSH access from `azure-client`
* Verify successful SSH connectivity

---

# Step-by-Step Implementation Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="bgm9rx"
az login
```

#### Explanation

Authenticates Azure CLI with your Azure account.

---

### Step 2: Check Existing SSH Key

Run:

```bash id="d7f9am"
ls ~/.ssh/id_rsa.pub
```

#### Explanation

Checks whether an SSH public key already exists on the `azure-client` host.

---

### Step 3: Generate SSH Key (If Needed)

If the SSH key does not exist, run:

```bash id="5i9d5q"
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
```

#### Explanation

Creates an RSA SSH key pair:

| File         | Purpose     |
| ------------ | ----------- |
| `id_rsa`     | Private Key |
| `id_rsa.pub` | Public Key  |

---

### Step 4: Verify SSH Public Key

Run:

```bash id="y2j8e6"
cat ~/.ssh/id_rsa.pub
```

#### Explanation

Displays the SSH public key that will be associated with the VM.

---

### Step 5: Check Available Resource Groups

Run:

```bash id="w2evjq"
az group list --output table
```

Store the Resource Group name:

```bash id="tvz3eo"
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 6: Create Azure Virtual Machine

Run:

```bash id="c7e3b1"
az vm create \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location westus \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --storage-sku Standard_LRS
```

#### Explanation

This command:

* Creates the VM in the `westus` region
* Uses Ubuntu image
* Configures SSH key authentication
* Creates the `azureuser` account
* Uses Standard_LRS storage to comply with Azure policies

---

### Step 7: Verify VM Running State

Run:

```bash id="kpqj9d"
az vm list -d --output table
```

Expected Output:

| Name        | PowerState |
| ----------- | ---------- |
| nautilus-vm | VM running |

#### Explanation

Confirms successful VM deployment.

---

### Step 8: Get Public IP Address

Run:

```bash id="oei4tu"
az vm show \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  -d \
  --query publicIps \
  -o tsv
```

Example Output:

```text id="13b1qf"
20.x.x.x
```

#### Explanation

Retrieves the Public IP address assigned to the VM.

---

### Step 9: Test Password-less SSH Access

Run:

```bash id="7v3w1y"
ssh azureuser@PUBLIC_IP
```

Example:

```bash id="x8a7w4"
ssh azureuser@20.10.15.20
```

Expected Output:

```bash id="pj8g1u"
azureuser@nautilus-vm:~$
```

#### Explanation

Confirms successful SSH key-based authentication without requiring a password.

---

### Step 10: Verify Authorized Keys on VM

Inside the VM, run:

```bash id="o1r6qt"
cat ~/.ssh/authorized_keys
```

#### Explanation

Verifies that the SSH public key has been added successfully to the VM.

---

# Azure Portal UI Method

### Step 1: Generate SSH Key

On the `azure-client` host, run:

```bash id="c8d3kz"
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
```

Copy the public key:

```bash id="e4m7vp"
cat ~/.ssh/id_rsa.pub
```

---

### Step 2: Create Azure VM

Search:

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
| Region              | West US          |
| Image               | Ubuntu 22.04 LTS |
| Size                | Standard_B1s     |
| Authentication Type | SSH Public Key   |
| Username            | azureuser        |

Paste the copied SSH public key.

---

### Step 3: Configure Networking

Allow inbound:

| Port     |
| -------- |
| SSH (22) |

#### Explanation

Allows secure SSH access to the VM.

---

### Step 4: Review and Create

Click:

* **Review + Create**
* **Create**

#### Explanation

Azure provisions the VM and configures SSH key authentication.

---

### Step 5: Verify SSH Connectivity

Run:

```bash id="6p4jxr"
ssh azureuser@PUBLIC_IP
```

#### Explanation

Confirms successful password-less SSH access.

---

# Quick Commands Only (Fastest Solution)

```bash id="o7x1wp"
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""

RG_NAME=$(az group list --query '[0].name' -o tsv)

az vm create \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --location westus \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --storage-sku Standard_LRS

az vm show \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  -d \
  --query publicIps \
  -o tsv
```

SSH into VM:

```bash id="x8q7mt"
ssh azureuser@PUBLIC_IP
```

---

# Best Practices

* Use SSH keys instead of passwords
* Protect private keys securely
* Restrict SSH access using NSGs
* Rotate SSH keys periodically
* Apply least-privilege access controls

---

# Key Learnings

* SSH key authentication improves VM security
* Azure VMs support password-less SSH access
* Public keys are stored in `authorized_keys`
* Azure CLI simplifies secure VM provisioning
* Secure remote access is critical in cloud environments
* SSH authentication is a foundational DevOps skill
