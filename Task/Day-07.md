# Day 07 – Create a Public IP Address for Azure VM

---

## Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations.

For this task, allocate a Public IP address with the following requirement:

* The Public IP name should be `nautilus-pip`

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

[Microsoft Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft Azure’s web-based management interface used to create and manage cloud resources.

---

### Step 2: Navigate to Public IP Addresses

Use the top search bar and search for:

| Search              |
| ------------------- |
| Public IP addresses |

Open the **Public IP addresses** service from the results.

#### Explanation

Azure Public IP service is used to create and manage internet-routable IP addresses for Azure resources.

---

### Step 3: Start Creating Public IP

Click the **+ Create** button located at the top-left corner.

#### Explanation

This starts the process of creating a new Public IP resource in Azure.

---

### Step 4: Configure Project Details

Under **Project Details**, configure the following settings:

| Setting        | Value                      |
| -------------- | -------------------------- |
| Subscription   | Default Azure Subscription |
| Resource Group | Existing Resource Group    |

#### Explanation

A Resource Group is a logical container used to organize and manage Azure resources together.

---

### Step 5: Configure Public IP Details

Under **Instance Details**, configure the following:

| Setting    | Value                     |
| ---------- | ------------------------- |
| Name       | `nautilus-pip`            |
| Region     | Same region as VM or VNet |
| IP Version | IPv4                      |
| SKU        | Standard                  |
| Assignment | Static                    |

#### Explanation

Azure allocates a Public IP address that can later be attached to resources such as Virtual Machines, Load Balancers, or NAT Gateways.

---

### Step 6: Review and Create

Click:

* **Review + create**
* **Create**

after validation passes successfully.

#### Explanation

Azure validates all configuration settings before provisioning the Public IP resource.

---

### Step 7: Verify Public IP Address

Go back to the **Public IP addresses** service and verify the following details:

| Property   | Expected Value |
| ---------- | -------------- |
| Name       | `nautilus-pip` |
| IP Version | IPv4           |
| Assignment | Static         |

#### Explanation

This confirms that the Public IP resource was created successfully.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="v9g0yu"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="r6v3u3"
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash id="6bux6o"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Create Public IP Address

Run the following command:

```bash id="8v5yzn"
az network public-ip create \
  --resource-group $RG_NAME \
  --name nautilus-pip \
  --sku Standard \
  --version IPv4 \
  --allocation-method Static
```

#### Explanation

This command creates a Standard SKU Static Public IPv4 address named `nautilus-pip`.

---

### Step 5: Verify Public IP Address

Run:

```bash id="g9d1i7"
az network public-ip list \
  --resource-group $RG_NAME \
  --output table
```

#### Explanation

This command lists all Public IP resources inside the specified Resource Group.

Expected Output:

| Name         | Location |
| ------------ | -------- |
| nautilus-pip | westus   |

---

### Step 6: Check Public IP Details

Run:

```bash id="1n0tqj"
az network public-ip show \
  --resource-group $RG_NAME \
  --name nautilus-pip \
  --output table
```

#### Explanation

This command displays detailed configuration information about the Public IP resource.

---

# Best Practices

* Use Static IPs for production workloads
* Avoid assigning Public IPs directly to all Virtual Machines
* Use Bastion Hosts or Load Balancers when possible
* Apply NSGs to restrict inbound traffic
* Monitor internet-facing resources carefully

---

# Key Learnings

* Public IPs enable internet connectivity for Azure resources
* Azure supports both Static and Dynamic IP allocation
* Public IPs are commonly attached to VMs and Load Balancers
* Azure CLI helps automate networking tasks
* Secure exposure of cloud resources is critical in production environments
