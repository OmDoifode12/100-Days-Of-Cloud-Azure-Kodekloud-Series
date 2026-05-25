# Day 11 – Change Azure Virtual Machine Size Using Console

---

## Task Overview

The Nautilus DevOps team is migrating a portion of its infrastructure to Microsoft Azure. During the migration process, multiple Virtual Machines (VMs) have been deployed across different regions. One of the Virtual Machines has started experiencing increased workload demands and now requires additional compute resources to maintain optimal performance.

For this task:

* An existing Virtual Machine named `devops-vm` already exists
* Current VM size is `Standard_B1s`

Your objective is to:

* Change the VM size from `Standard_B1s` to `Standard_B2s`
* Ensure the VM remains in the running state after the resize operation is completed

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

The Virtual Machines service is used to manage Azure compute resources and infrastructure configurations.

---

### Step 3: Open Existing Virtual Machine

Select the Virtual Machine:

| VM Name     |
| ----------- |
| `devops-vm` |

#### Explanation

This opens the configuration page of the existing Azure Virtual Machine.

---

### Step 4: Verify VM Status

Ensure the Virtual Machine status is:

| Property | Expected Value |
| -------- | -------------- |
| Status   | Running        |

#### Explanation

The VM should be fully initialized before performing compute resize operations.

---

### Step 5: Open VM Size Settings

Inside the Virtual Machine menu:

Navigate to:

| Section         |
| --------------- |
| Settings → Size |

#### Explanation

The Size section is used to modify CPU, memory, and compute resource allocation for the Virtual Machine.

---

### Step 6: Select New VM Size

Search for the following VM size:

| VM Size        |
| -------------- |
| `Standard_B2s` |

Select the size and click:

* **Resize**

#### Explanation

This operation upgrades the Virtual Machine resources from `Standard_B1s` to `Standard_B2s`.

---

### Step 7: Wait for Resize Operation

Azure will automatically:

* Stop the VM temporarily if required
* Allocate new compute resources
* Apply the new VM size
* Restart the Virtual Machine

#### Explanation

Azure dynamically reallocates infrastructure resources during the resize process.

---

### Step 8: Verify VM Resize

Go back to the Virtual Machine Overview page and verify the following:

| Property | Expected Value |
| -------- | -------------- |
| VM Size  | `Standard_B2s` |
| Status   | Running        |

#### Explanation

This confirms that the resize operation completed successfully and the VM is operational.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="jlwm5y"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="jlwm6u"
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash id="jlwm7i"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Resize the Virtual Machine

Run the following command:

```bash id="jlwm8o"
az vm resize \
  --resource-group $RG_NAME \
  --name devops-vm \
  --size Standard_B2s
```

#### Explanation

This command changes the Virtual Machine size from `Standard_B1s` to `Standard_B2s`.

---

### Step 5: Verify VM Size

Run:

```bash id="jlwm9p"
az vm show \
  --resource-group $RG_NAME \
  --name devops-vm \
  --query hardwareProfile.vmSize
```

Expected Output:

```text id="jlwm0a"
Standard_B2s
```

#### Explanation

This command displays the current Virtual Machine size.

---

### Step 6: Verify VM Running State

Run:

```bash id="jlwm1s"
az vm list -d --output table
```

Expected Output:

| Name      | PowerState |
| --------- | ---------- |
| devops-vm | VM running |

#### Explanation

This confirms that the Virtual Machine is successfully running after the resize operation.

---

# Best Practices

* Monitor VM performance regularly
* Resize VMs based on workload requirements
* Avoid overprovisioning compute resources
* Use autoscaling where possible
* Plan capacity upgrades carefully in production environments

---

# Key Learnings

* Azure allows dynamic Virtual Machine resizing
* VM sizes determine CPU and memory allocation
* Vertical Scaling improves workload performance
* Azure CLI simplifies infrastructure management
* Proper resource scaling is critical in cloud environments
