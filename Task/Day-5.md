# Day 05 – Create a Virtual Network (IPv4) in Azure

---

## Task Overview

The Nautilus DevOps team is strategically planning the migration of a portion of their infrastructure to the Azure cloud. Acknowledging the magnitude of this endeavor, they have chosen to tackle the migration incrementally rather than as a single, massive transition. Their approach involves creating Virtual Networks (VNets) as the initial step, as they will be provisioning various services under different VNets.

For this task, create a Virtual Network (VNet) with the following requirements:

* The VNet name should be `devops-vnet`
* Region should be `centralus`
* IPv4 CIDR block should be `192.168.0.0/24`

---

# Step-by-Step Implementation

### Step 1: Login to Azure CLI

Run:

```bash
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Verify Available Resource Groups

Run:

```bash
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

Example Output:

| Name                         | Location |
| ---------------------------- | -------- |
| kml_rg_main-145620288f634136 | eastus   |

---

### Step 3: Set Resource Group Variable

Run:

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Create Virtual Network

Run the following command:

```bash
az network vnet create \
  --resource-group $RG_NAME \
  --name devops-vnet \
  --address-prefix 192.168.0.0/24 \
  --location centralus
```

#### Explanation

This command creates a Virtual Network named `devops-vnet` with the specified IPv4 CIDR block in the `centralus` region.

---

### Step 5: Verify Virtual Network

Run:

```bash
az network vnet list --output table
```

#### Explanation

This command lists all available Virtual Networks in the Azure subscription.

Expected Output:

| Name        | Location  |
| ----------- | --------- |
| devops-vnet | centralus |

---

### Step 6: Check VNet Details

Run:

```bash
az network vnet show \
  --resource-group $RG_NAME \
  --name devops-vnet \
  --output table
```

#### Explanation

This command displays detailed configuration information about the Virtual Network.

---

# Best Practices

* Use proper CIDR planning before creating VNets
* Avoid overlapping IP ranges across environments
* Use separate VNets for Production, Development, and Testing
* Apply Network Security Groups (NSGs) for subnet security
* Document network architecture properly

---

# Key Learnings

* Azure VNets provide isolated private cloud networking
* CIDR notation defines the IP address range
* VNets are foundational components of Azure infrastructure
* Azure CLI simplifies infrastructure provisioning
* Proper network planning is essential in cloud environments
