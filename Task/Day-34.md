# Day 34 – Enabling Internet Connectivity for Virtual Machines

---

## Task Overview

The Nautilus DevOps Team encountered a connectivity issue on an Azure Virtual Machine named `devops-vm`. Due to the issue, the VM was unable to access the internet, preventing package installation and software updates. The objective was to identify the root cause of the problem and restore normal internet connectivity.

The following requirements were completed:

* Investigate the connectivity issue affecting `devops-vm`
* Verify network routing and internet access
* Analyze Virtual Network, Subnet, and NSG configurations
* Identify the root cause preventing package installation
* Restore outbound internet connectivity
* Verify successful package installation after the fix

---

# Architecture Overview

```text
Azure Virtual Machine
(devops-vm)
        │
        ▼
Network Security Group
(devops-nsg)
        │
        ▼
Block-All-Outbound Rule ❌
        │
        ▼
Internet Access Blocked
        │
        ▼
Package Installation Failed

------------------------------------------------

After Fix

Azure Virtual Machine
(devops-vm)
        │
        ▼
Network Security Group
(devops-nsg)
        │
        ▼
Outbound Access Allowed ✅
        │
        ▼
Internet Connectivity Restored
        │
        ▼
Package Installation Successful
```

---

# Step-by-Step Troubleshooting Using Azure CLI

---

# Step 1: Verify Azure Login

Run:

```bash
az account show
```

#### Explanation

Verifies access to the Azure subscription.

---

# Step 2: Identify Resource Group

Run:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the resource group containing the VM.

---

# Step 3: Verify VM Status

Run:

```bash
az vm list -d -o table
```

#### Explanation

Confirms the VM is running and identifies its public IP.

---

# Step 4: Connect to the VM

Run:

```bash
ssh -i /root/.ssh/id_rsa azureuser@<PUBLIC_IP>
```

#### Explanation

Connects to the Azure Virtual Machine using the provided SSH key.

---

# Step 5: Test Internet Connectivity

Run:

```bash
ping 8.8.8.8 -c 4
```

#### Output

```text
100% packet loss
```

#### Explanation

Confirms the VM cannot reach external networks.

---

# Step 6: Verify Routing Configuration

Run:

```bash
ip route
```

#### Output

```text
default via 10.0.0.1 dev eth0
```

#### Explanation

Confirms the VM has a valid default route.

---

# Step 7: Inspect Effective Route Table

Run:

```bash
az network nic show-effective-route-table \
  -g $RG_NAME \
  -n devops-vmVMNic \
  -o table
```

#### Important Finding

```text
0.0.0.0/0 → Internet
```

#### Explanation

Internet routing was configured correctly.

---

# Step 8: Review Network Security Group Rules

Run:

```bash
az network nsg rule list \
  -g $RG_NAME \
  --nsg-name devops-nsg \
  -o table
```

#### Output

```text
Allow-SSH
Allow-HTTP
Block-All-Outbound
```

#### Explanation

A custom outbound deny rule was blocking all internet traffic.

---

# Step 9: Remove Blocking Outbound Rule

Run:

```bash
az network nsg rule delete \
  --resource-group $RG_NAME \
  --nsg-name devops-nsg \
  --name Block-All-Outbound
```

#### Explanation

Removes the NSG rule preventing outbound internet access.

---

# Step 10: Verify Connectivity Restoration

SSH back into the VM and run:

```bash
ping 8.8.8.8 -c 4
```

Expected Output:

```text
64 bytes from 8.8.8.8
```

#### Explanation

Confirms internet connectivity is restored.

---

# Step 11: Verify Package Installation

Run:

```bash
sudo apt update
```

Expected Output:

```text
Reading package lists... Done
```

#### Explanation

Confirms package repositories are now reachable.

---

# Final Validation Checklist

✅ VM connectivity issue identified

✅ Network routes verified

✅ Effective route table inspected

✅ NSG rules analyzed

✅ Root cause identified

✅ Blocking outbound rule removed

✅ Internet access restored

✅ Package installation working successfully

✅ Task completed successfully

---

# Common Issues & Fixes

| Issue                            | Resolution                           |
| -------------------------------- | ------------------------------------ |
| VM cannot ping external IPs      | Check NSG outbound rules             |
| Package installation fails       | Verify internet connectivity         |
| Route table issue suspected      | Inspect effective routes             |
| Public IP exists but no internet | Check outbound NSG rules             |
| DNS issues                       | Test connectivity using IP addresses |

---

# Best Practices

* Always verify connectivity before troubleshooting applications
* Inspect NSG rules before modifying routing configurations
* Use effective route tables to validate network paths
* Avoid unnecessary outbound deny rules
* Test connectivity after every network-related change

---

# Key Learnings

* Azure NSGs can block outbound traffic even when routing is correct
* Effective Route Tables help validate packet flow
* Internet connectivity issues are not always route table related
* Systematic troubleshooting reduces resolution time
* Network Security Groups are a critical component of Azure networking
