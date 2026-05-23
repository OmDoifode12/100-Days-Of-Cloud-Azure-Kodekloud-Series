# Day 09 – Attach Network Interface Card (NIC) to Azure Virtual Machine

---

## Task Overview

The DevOps team is migrating services to Microsoft Azure in incremental phases to ensure better control, optimization, and minimal disruption during migration activities.

For this task:

* An existing Virtual Machine named `devops-vm` already exists
* An existing Network Interface Card (NIC) named `devops-nic` already exists
* Both resources are located in the `centralus` region

Your objective is to:

* Attach the network interface `devops-nic` to the Virtual Machine `devops-vm`
* Ensure the NIC status becomes attached successfully
* Ensure Virtual Machine initialization is completed before submission

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

The Virtual Machines service is used to manage Azure compute resources and their networking configurations.

---

### Step 3: Open Existing Virtual Machine

Select the Virtual Machine:

| VM Name     |
| ----------- |
| `devops-vm` |

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

Azure services and VM agents must finish initialization to ensure stable network modification operations.

---

### Step 5: Stop the Virtual Machine

Click:

* **Stop**

Wait until the VM status changes to:

| Status                |
| --------------------- |
| Stopped (deallocated) |

#### Explanation

Azure requires the VM to be deallocated before attaching additional network interfaces.

---

### Step 6: Open Networking Settings

Inside the Virtual Machine menu:

Navigate to:

| Section               |
| --------------------- |
| Settings → Networking |

#### Explanation

The Networking section is used to manage NICs, Public IPs, NSGs, and network communication settings.

---

### Step 7: Attach Existing NIC

Under the **Network Interfaces** section:

Click:

* **Attach network interface**

Select the following NIC:

| NIC Name     |
| ------------ |
| `devops-nic` |

Click:

* **OK**
* **Attach**

#### Explanation

This operation attaches the existing network interface to the Virtual Machine.

---

### Step 8: Start the Virtual Machine

Go back to the Virtual Machine Overview page.

Click:

* **Start**

Wait until the VM status becomes:

| Status  |
| ------- |
| Running |

#### Explanation

The VM must be restarted after NIC attachment to apply networking changes successfully.

---

### Step 9: Verify NIC Attachment

Navigate again to:

| Section               |
| --------------------- |
| Settings → Networking |

Verify that the following NIC is attached:

| NIC          |
| ------------ |
| `devops-nic` |

#### Explanation

This confirms that the network interface has been successfully attached to the Virtual Machine.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="jlwm6h"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="jlwm7j"
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash id="jlwm8k"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Stop the Virtual Machine

Run:

```bash id="jlwm9l"
az vm deallocate \
  --resource-group $RG_NAME \
  --name devops-vm
```

#### Explanation

This command stops and deallocates the Virtual Machine before NIC attachment.

---

### Step 5: Attach NIC to Virtual Machine

Run:

```bash id="jlwm0z"
az vm nic add \
  --resource-group $RG_NAME \
  --vm-name devops-vm \
  --nics devops-nic
```

#### Explanation

This command attaches the existing NIC `devops-nic` to the Virtual Machine `devops-vm`.

---

### Step 6: Start the Virtual Machine

Run:

```bash id="jlwm1x"
az vm start \
  --resource-group $RG_NAME \
  --name devops-vm
```

#### Explanation

This command starts the Virtual Machine after successful NIC attachment.

---

### Step 7: Verify NIC Attachment

Run:

```bash id="jlwm2c"
az vm nic list \
  --resource-group $RG_NAME \
  --vm-name devops-vm \
  --output table
```

#### Explanation

This command lists all NICs attached to the Virtual Machine.

Expected Output:

| Name       |
| ---------- |
| devops-nic |

---

# Best Practices

* Use separate NICs for traffic segregation
* Apply NSGs to secure NIC traffic
* Use proper naming conventions for network interfaces
* Avoid exposing unnecessary interfaces publicly
* Monitor network performance and security regularly

---

# Key Learnings

* NICs connect Azure Virtual Machines to networks
* Azure supports multiple NIC attachments for advanced networking
* VM deallocation is required before attaching additional NICs
* Azure CLI simplifies networking operations and automation
* Proper network interface management improves security and scalability
