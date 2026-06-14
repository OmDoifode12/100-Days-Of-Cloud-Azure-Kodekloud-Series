# Day 30 – Create Azure SQL Database

---

## Task Overview

The Nautilus DevOps Team started working on deploying and configuring managed database services on Microsoft Azure as part of their cloud migration strategy. The objective of this task was to create a publicly accessible Azure SQL Database instance with specific compute, storage, redundancy, and authentication configurations.

The following requirements were completed:

* Create an Azure SQL Server named `nautilus-server-31031`
* Create an Azure SQL Database named `nautilus-sqldb`
* Configure the compute tier as `Basic`
* Set backup redundancy to `Locally-redundant`
* Configure database size to `2 GiB`
* Enable public accessibility using firewall rules
* Verify the database reaches the `Online/Ready` state

Region:

| Location   |
| ---------- |
| Central US |

---

# Architecture Overview

```text id="m4x8q2"
Azure SQL Server
       │
       ▼
nautilus-server-31031
       │
       ▼
Azure SQL Database
       │
       ▼
nautilus-sqldb
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run the following command:

```bash id="p7m2x5"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure subscription.

---

# Step 2: Verify Azure Subscription

Run:

```bash id="v1q8m4"
az account show
```

#### Explanation

This verifies the active Azure subscription and login status.

---

# Step 3: Set Resource Group Variable

Run:

```bash id="k5x1m9"
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Stores the Resource Group name into a variable for easier command usage.

---

# Step 4: Create Azure SQL Server

Run:

```bash id="r3m7q2"
az sql server create \
  --name nautilus-server-31031 \
  --resource-group $RG_NAME \
  --location centralus \
  --admin-user nautilus-admin \
  --admin-password 'N@utilusSQL#2026'
```

#### Explanation

This command creates the Azure SQL logical server.

| Setting        | Value                   |
| -------------- | ----------------------- |
| Server Name    | `nautilus-server-31031` |
| Region         | `centralus`             |
| Admin Username | `nautilus-admin`        |

---

# Step 5: Configure Firewall Rule

Run:

```bash id="x9p2m5"
az sql server firewall-rule create \
  --resource-group $RG_NAME \
  --server nautilus-server-31031 \
  --name AllowAllAzureIPs \
  --start-ip-address 0.0.0.0 \
  --end-ip-address 255.255.255.255
```

#### Explanation

This firewall rule enables public accessibility to the Azure SQL Server.

---

# Step 6: Create Azure SQL Database

Run:

```bash id="m6q1x8"
az sql db create \
  --resource-group $RG_NAME \
  --server nautilus-server-31031 \
  --name nautilus-sqldb \
  --service-objective Basic \
  --backup-storage-redundancy Local \
  --max-size 2GB
```

#### Explanation

This command creates the Azure SQL Database with:

| Setting           | Value            |
| ----------------- | ---------------- |
| Database Name     | `nautilus-sqldb` |
| Compute Tier      | `Basic`          |
| Backup Redundancy | `Local`          |
| Database Size     | `2 GiB`          |

---

# Step 7: Verify Database Status

Run:

```bash id="t4m8q1"
az sql db show \
  --resource-group $RG_NAME \
  --server nautilus-server-31031 \
  --name nautilus-sqldb \
  --query status \
  -o tsv
```

Expected Output:

```text id="q2x7m5"
Online
```

#### Explanation

This confirms that the database is successfully provisioned and ready.

---

# Step 8: Verify SQL Server

Run:

```bash id="p8m4x1"
az sql server list --output table
```

Expected Output:

| Server Name             |
| ----------------------- |
| `nautilus-server-31031` |

---

# Final Validation Checklist

✅ Azure SQL Server created successfully
✅ Azure SQL Database created successfully
✅ Compute tier configured as Basic
✅ Backup redundancy configured as Local
✅ Database size set to 2 GiB
✅ Firewall rules configured for public access
✅ Database status verified as Online

---

# Common Issues & Fixes

| Issue                                 | Resolution                                                       |
| ------------------------------------- | ---------------------------------------------------------------- |
| Password complexity validation failed | Use strong password with uppercase, lowercase, numbers & symbols |
| Server name unavailable               | Use globally unique server name                                  |
| Firewall access issue                 | Configure firewall rules correctly                               |
| Database provisioning delay           | Wait several minutes before verification                         |
| Invalid redundancy configuration      | Use `Local` redundancy                                           |

---

# Best Practices

* Use strong administrator passwords
* Restrict firewall access in production
* Use least privilege access policies
* Monitor database storage and performance
* Enable proper backup and disaster recovery strategies

---

# Key Learnings

* Azure SQL Database is a fully managed cloud database service
* Azure SQL Servers act as logical containers for databases
* Firewall rules control external database access
* Backup redundancy improves database availability and durability
* Azure CLI simplifies database provisioning and management
