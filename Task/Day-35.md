# Day 35 – Configuring Virtual Network Peering

---

## Task Overview

The Nautilus DevOps Team was tasked with demonstrating Azure Virtual Network (VNet) Peering to enable communication between resources deployed in separate virtual networks. The objective was to establish secure connectivity between a public VNet containing a public VM and a private VNet hosting a private VM.

The following requirements were completed:

* Verify existing Azure networking resources
* Create a VNet Peering named `devops-pub-to-priv-peering`
* Connect the Public VNet and Private VNet
* Enable communication between both networks
* Validate peering status and synchronization
* Verify connectivity between `devops-pub-vm` and `devops-priv-vm`

---

# Architecture Overview

```text
Public Virtual Network
(devops-pub-vnet)
        │
        │
        ▼
VNet Peering
(devops-pub-to-priv-peering)
        ▲
        │
        │
Private Virtual Network
(devops-priv-vnet)

        │
        │
        ▼

devops-pub-vm  <----->  devops-priv-vm
```

---

# Step-by-Step Implementation Using Azure Portal

---

# Step 1: Login to Azure Portal

Login using the credentials provided in the lab environment.

Portal:

```text
https://portal.azure.com
```

#### Explanation

Provides access to Azure resources required for the task.

---

# Step 2: Verify Existing Virtual Networks

Navigate to:

```text
Azure Portal
→ Virtual Networks
```

Verify the following VNets exist:

```text
devops-pub-vnet
devops-priv-vnet
```

#### Explanation

These VNets will be connected using VNet Peering.

---

# Step 3: Open Public Virtual Network

Navigate to:

```text
Virtual Networks
→ devops-pub-vnet
→ Peerings
```

Click:

```text
+ Add
```

#### Explanation

Creates a new peering connection.

---

# Step 4: Configure VNet Peering

Enter:

| Setting                      | Value                      |
| ---------------------------- | -------------------------- |
| Peering Name                 | devops-pub-to-priv-peering |
| Remote Virtual Network       | devops-priv-vnet           |
| Allow Virtual Network Access | Enabled                    |

Click:

```text
Add
```

#### Explanation

Creates connectivity between the public and private VNets.

---

# Step 5: Verify Peering Status

Navigate to:

```text
devops-pub-vnet
→ Peerings
```

Expected Status:

```text
Connected
```

#### Explanation

Confirms the peering connection was successfully established.

---

# Step 6: Validate Reverse Peering

Navigate to:

```text
devops-priv-vnet
→ Peerings
```

Verify:

```text
devops-pub-to-priv-peering
Status: Connected
```

#### Explanation

Confirms bidirectional communication between VNets.

---

# Step 7: Retrieve Public VM Information

Navigate to:

```text
Virtual Machines
→ devops-pub-vm
```

Copy:

```text
Public IP Address
```

#### Explanation

Used to access the public VM for connectivity testing.

---

# Step 8: Retrieve Private VM Information

Navigate to:

```text
Virtual Machines
→ devops-priv-vm
```

Copy:

```text
Private IP Address
```

Example:

```text
10.1.1.4
```

#### Explanation

Used to validate communication through the peering connection.

---

# Step 9: Verify Connectivity

Connect to the public VM and test communication with the private VM.

Example:

```bash
ping <private-vm-ip>
```

#### Explanation

Confirms that resources in separate VNets can communicate through the peering connection.

---

# Step 10: Verify Peering Using Azure CLI

Run:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

az network vnet peering list \
-g $RG_NAME \
--vnet-name devops-pub-vnet \
-o table
```

Expected Output:

```text
PeeringState      Connected
ProvisioningState Succeeded
PeeringSyncLevel  FullyInSync
```

#### Explanation

Validates successful VNet Peering configuration.

---

# Final Validation Checklist

✅ Public VNet verified

✅ Private VNet verified

✅ VNet Peering created successfully

✅ Peering name configured correctly

✅ Virtual Network Access enabled

✅ Peering status shows Connected

✅ Peering synchronization shows FullyInSync

✅ Communication between VNets established

✅ Task completed successfully

---

# Common Issues & Fixes

| Issue                       | Resolution                                |
| --------------------------- | ----------------------------------------- |
| Peering status disconnected | Verify both VNets exist                   |
| Unable to ping private VM   | Check NSG inbound rules                   |
| SSH access denied           | Verify SSH key configuration              |
| Peering not visible         | Refresh Azure Portal                      |
| Connectivity test fails     | Verify VNet address spaces do not overlap |

---

# Best Practices

* Use VNet Peering instead of VPN when VNets are within Azure
* Avoid overlapping CIDR ranges between VNets
* Use NSGs to control traffic between peered networks
* Validate peering synchronization after creation
* Monitor network communication using Azure Network Watcher

---

# Key Learnings

* Azure VNet Peering enables private communication between VNets
* Peering provides low-latency and high-bandwidth connectivity
* Connected VNets communicate without public internet exposure
* NSGs still control traffic across peered networks
* VNet Peering is a fundamental building block for enterprise Azure networking
