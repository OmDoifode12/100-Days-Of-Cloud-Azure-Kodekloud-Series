# Day 06 – Create a Subnet in Azure Virtual Network

---

## Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition.

For this task, create:

* A Virtual Network (VNet) named `xfusion-vnet`
* One subnet named `xfusion-subnet`
* Region should be `westus`
* IPv4 address range should be `10.0.0.0/16`

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

### Step 4: Create Virtual Network and Subnet

Run the following command:

```bash
az network vnet create \
  --resource-group $RG_NAME \
  --name xfusion-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name xfusion-subnet \
  --subnet-prefix 10.0.0.0/24 \
  --location westus
```

#### Explanation

This command creates:

* A Virtual Network named `xfusion-vnet`
* A subnet named `xfusion-subnet`
* Configures the VNet address range as `10.0.0.0/16`
* Configures the subnet range as `10.0.0.0/24`

---

### Step 5: Verify Virtual Network

Run:

```bash
az network vnet list --output table
```

#### Explanation

This command lists all Virtual Networks available in the Azure subscription.

Expected Output:

| Name         | Location |
| ------------ | -------- |
| xfusion-vnet | westus   |

---

### Step 6: Verify Subnet

Run:

```bash
az network vnet subnet list \
  --resource-group $RG_NAME \
  --vnet-name xfusion-vnet \
  --output table
```

#### Explanation

This command lists all subnets configured inside the specified Virtual Network.

Expected Output:

| Name           | AddressPrefix |
| -------------- | ------------- |
| xfusion-subnet | 10.0.0.0/24   |

---

# Best Practices

* Design subnet ranges carefully
* Separate workloads into dedicated subnets
* Apply NSGs to control subnet traffic
* Use meaningful naming conventions
* Avoid overlapping subnet ranges

---

# Key Learnings

* Subnets divide Virtual Networks into smaller logical segments
* CIDR hierarchy is important in network design
* Azure VNets and subnets form the base of cloud networking
* Azure CLI helps automate network provisioning
* Proper subnetting improves security and scalability
