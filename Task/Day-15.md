# Day 15 – Create and Configure Network Security Group (NSG) in Azure

---

## Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition.

For this task, create a Network Security Group (NSG) with the following requirements:

- NSG Name: `datacenter-nsg`
- Create an inbound rule named `Allow-HTTP`
  - Protocol: TCP
  - Port: 80
  - Source: 0.0.0.0/0
- Create another inbound rule named `Allow-SSH`
  - Protocol: TCP
  - Port: 22
  - Source: 0.0.0.0/0

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft's web-based interface for managing Azure resources.

---

### Step 2: Navigate to Network Security Groups

Use the search bar and search for:

| Search |
|----------|
| Network Security Groups |

Open the **Network Security Groups** service.

#### Explanation

NSGs are used to filter and control inbound and outbound network traffic.

---

### Step 3: Create a New NSG

Click:

- **+ Create**

Configure:

| Setting | Value |
|----------|----------|
| Subscription | Default Azure Subscription |
| Resource Group | Existing Resource Group |
| Name | `datacenter-nsg` |
| Region | Same region as your resources |

Click:

- **Review + Create**
- **Create**

#### Explanation

This creates the Network Security Group resource.

---

### Step 4: Open the NSG

After deployment completes:

Open:

| NSG |
|----------|
| datacenter-nsg |

---

### Step 5: Add HTTP Inbound Rule

Navigate to:

| Section |
|----------|
| Inbound Security Rules |

Click:

- **+ Add**

Configure:

| Setting | Value |
|----------|----------|
| Source | IP Addresses |
| Source IP Addresses/CIDR | 0.0.0.0/0 |
| Source Port Ranges | * |
| Destination | Any |
| Service | HTTP |
| Action | Allow |
| Priority | 100 |
| Name | Allow-HTTP |

Click:

- **Add**

#### Explanation

This rule allows HTTP traffic on port 80 from anywhere.

---

### Step 6: Add SSH Inbound Rule

Click:

- **+ Add**

Configure:

| Setting | Value |
|----------|----------|
| Source | IP Addresses |
| Source IP Addresses/CIDR | 0.0.0.0/0 |
| Source Port Ranges | * |
| Destination | Any |
| Service | SSH |
| Action | Allow |
| Priority | 110 |
| Name | Allow-SSH |

Click:

- **Add**

#### Explanation

This rule allows SSH traffic on port 22 from anywhere.

---

### Step 7: Verify Rules

Under Inbound Security Rules verify:

| Rule Name | Port | Action |
|------------|------|----------|
| Allow-HTTP | 80 | Allow |
| Allow-SSH | 22 | Allow |

#### Explanation

This confirms successful NSG configuration.

---

# Method 2: Using Azure CLI

### Step 1: Login

Run:

```bash
az login
```

---

### Step 2: Set Resource Group

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

---

### Step 3: Create NSG

```bash
az network nsg create \
  --resource-group $RG_NAME \
  --name datacenter-nsg
```

---

### Step 4: Create HTTP Rule

```bash
az network nsg rule create \
  --resource-group $RG_NAME \
  --nsg-name datacenter-nsg \
  --name Allow-HTTP \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes 0.0.0.0/0 \
  --destination-port-ranges 80
```

---

### Step 5: Create SSH Rule

```bash
az network nsg rule create \
  --resource-group $RG_NAME \
  --nsg-name datacenter-nsg \
  --name Allow-SSH \
  --priority 110 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes 0.0.0.0/0 \
  --destination-port-ranges 22
```

---

### Step 6: Verify Rules

```bash
az network nsg rule list \
  --resource-group $RG_NAME \
  --nsg-name datacenter-nsg \
  --output table
```

Expected Output:

| Name | Port | Access |
|--------|--------|--------|
| Allow-HTTP | 80 | Allow |
| Allow-SSH | 22 | Allow |

---

# Best Practices

- Use NSGs to control network access
- Follow the principle of least privilege
- Avoid exposing unnecessary ports
- Restrict SSH access to trusted IPs in production
- Regularly review NSG rules

---

# Key Learnings

* NSGs act as virtual firewalls in Azure
* Inbound rules control incoming traffic
* Port 80 is used for HTTP traffic
* Port 22 is used for SSH access
* NSGs are essential for securing Azure resources
