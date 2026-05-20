# Day 04 – Create a Virtual Network (VNet) in Azure

---

## Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations.

For this task, create a Virtual Network (VNet) with the following requirements:

* The VNet name should be `devops-vnet`
* Region should be `westus`
* IPv4 CIDR block can be any valid IPv4 range

---

# Step-by-Step Implementation

### Step 1: Login to Azure CLI

Run:

```bash id="wjlwm0"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Verify Available Resource Groups

Run:

```bash id="b6e5d0"
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

```bash id="qeb27u"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Create Virtual Network

Run the following command:

```bash id="vkedaf"
az network vnet create \
  --resource-group $RG_NAME \
  --name devops-vnet \
  --address-prefix 10.0.0.0/16 \
  --location westus
```

#### Explanation

This command creates a Virtual Network named `devops-vnet` with the IPv4 CIDR block `10.0.0.0/16` in the `westus` region.

---

### Step 5: Verify Virtual Network

Run:

```bash id="r3o0vr"
az network vnet list --output table
```

#### Explanation

This command lists all available Virtual Networks in the Azure subscription.

Expected Output:

| Name        | Location |
| ----------- | -------- |
| devops-vnet | westus   |

---

### Step 6: Check VNet Details

Run:

```bash id="9z8r5q"
az network vnet show \
  --resource-group $RG_NAME \
  --name devops-vnet \
  --output table
```

#### Explanation

This command displays detailed configuration information about the Virtual Network.

---

# Best Practices

* Plan IP ranges carefully before deployment
* Avoid overlapping CIDR blocks between environments
* Use separate VNets for Production, Development, and Testing
* Apply proper subnet segmentation
* Use NSGs for additional network security

---

# Key Learnings

* Azure VNets provide isolated cloud networking
* CIDR notation defines the network IP range
* VNets are foundational building blocks in Azure
* Azure CLI enables infrastructure automation
* Proper network planning improves scalability and security
