# Day 10 – Attach Public IP to Azure Virtual Machine

---

## Task Overview

The Nautilus DevOps team has already provisioned a Virtual Machine and allocated a Public IP address in Microsoft Azure. The final step is to attach the Public IP address to the Virtual Machine’s Network Interface Card (NIC) to enable internet connectivity.

For this task:

* An existing Virtual Machine named `datacenter-vm-pip` already exists
* An existing Public IP Address named `datacenter-pip` already exists

Your objective is to:

* Attach the Public IP `datacenter-pip` to the Network Interface Card (NIC) of the Virtual Machine `datacenter-vm-pip`
* Ensure the VM is properly assigned the Public IP address

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

[Microsoft Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft Azure’s web-based management interface used to create and manage cloud resources.

---

### Step 2: Navigate to Virtual Machines

Use the top search bar and search for:

| Search           |
| ---------------- |
| Virtual Machines |

Open the **Virtual Machines** service from the results.

#### Explanation

The Virtual Machines service is used to manage Azure compute resources and networking configurations.

---

### Step 3: Open Existing Virtual Machine

Select the Virtual Machine:

| VM Name             |
| ------------------- |
| `datacenter-vm-pip` |

#### Explanation

This opens the configuration page of the existing Azure Virtual Machine.

---

### Step 4: Verify VM Initialization

Ensure the Virtual Machine status is:

| Property | Expected Value |
| -------- | -------------- |
| Status   | Running        |

#### Important

Wait for VM initialization to complete fully before proceeding.

#### Explanation

Azure services and VM agents must finish initialization to ensure stable network configuration operations.

---

### Step 5: Open Networking Settings

Inside the Virtual Machine menu:

Navigate to:

| Section    |
| ---------- |
| Networking |

#### Explanation

The Networking section is used to manage NICs, Public IPs, NSGs, and connectivity settings.

---

### Step 6: Open Network Interface Card (NIC)

Under the **Network Interface** section:

Click the attached NIC name.

Example:

| NIC                      |
| ------------------------ |
| `datacenter-vm-pipVMNic` |

#### Explanation

The Public IP Address is attached to the Network Interface Card, not directly to the Virtual Machine.

---

### Step 7: Open IP Configurations

Inside the NIC menu:

Navigate to:

| Section                      |
| ---------------------------- |
| Settings → IP configurations |

#### Explanation

IP configurations manage Private IPs and Public IP assignments for the NIC.

---

### Step 8: Edit IP Configuration

Click the existing IP configuration:

| IP Configuration |
| ---------------- |
| `ipconfig1`      |

#### Explanation

This opens the IP configuration settings for modification.

---

### Step 9: Attach Public IP Address

Under the **Public IP address** field:

Select:

| Public IP        |
| ---------------- |
| `datacenter-pip` |

Click:

* **Save**

#### Explanation

This operation associates the existing Public IP Address with the Network Interface Card attached to the Virtual Machine.

---

### Step 10: Verify Public IP Assignment

Go back to the Virtual Machine Overview page and verify:

| Property          | Expected Value |
| ----------------- | -------------- |
| Public IP Address | Assigned       |

Example:

| Public IP  |
| ---------- |
| `20.x.x.x` |

#### Explanation

This confirms that the Public IP has been successfully attached to the Virtual Machine.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="jlwm2d"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="jlwm3f"
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash id="jlwm4h"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Get Network Interface Name

Run:

```bash id="jlwm5j"
az vm nic list \
  --resource-group $RG_NAME \
  --vm-name datacenter-vm-pip \
  --output table
```

#### Explanation

This command lists all Network Interfaces attached to the Virtual Machine.

---

### Step 5: Attach Public IP to NIC

Run the following command:

```bash id="jlwm6k"
az network nic ip-config update \
  --resource-group $RG_NAME \
  --nic-name datacenter-vm-pipVMNic \
  --name ipconfig1 \
  --public-ip-address datacenter-pip
```

#### Explanation

This command associates the Public IP `datacenter-pip` with the Network Interface Card attached to the Virtual Machine.

---

### Step 6: Verify Public IP Assignment

Run:

```bash id="jlwm7l"
az vm list-ip-addresses \
  --resource-group $RG_NAME \
  --name datacenter-vm-pip \
  --output table
```

#### Explanation

This command displays the IP addresses assigned to the Virtual Machine.

Expected Output:

| VirtualMachine    | PublicIPAddresses |
| ----------------- | ----------------- |
| datacenter-vm-pip | 20.x.x.x          |

---

# Best Practices

* Avoid assigning Public IPs directly to all Virtual Machines
* Use NSGs to restrict inbound access
* Use Bastion Hosts for secure VM access
* Monitor internet-facing resources regularly
* Use proper naming conventions for networking resources

---

# Key Learnings

* Public IPs provide internet connectivity for Azure resources
* Public IPs are attached to NICs, not directly to VMs
* NIC IP configurations manage Public and Private IP assignments
* Azure CLI simplifies cloud networking operations
* Proper network configuration is critical in production environments
