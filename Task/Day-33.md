# Day 33 – Integrating Virtual Machines with Application Load Balancer

---

## Task Overview

The Nautilus DevOps Team was tasked with placing an Azure Load Balancer in front of an existing Virtual Machine running Nginx. The objective was to distribute incoming HTTP traffic through the Load Balancer while continuously monitoring backend health using health probes.

The following requirements were completed:

* Create an Azure Load Balancer named `devops-lb`
* Configure a frontend IP configuration named `devops-lb-ip`
* Create and associate a Public IP named `devops-lb-ip`
* Create a backend pool named `devops-backend-pool`
* Add the Nginx Virtual Machine to the backend pool
* Create a health probe named `devops-health-probe` on port `80`
* Create a load balancing rule named `devops-lb-rule`
* Route frontend traffic on port `80` to backend port `80`
* Configure the Network Security Group (NSG) to allow HTTP traffic

---

# Architecture Overview

```text
Internet
    │
    ▼
Public IP
(devops-lb-ip)
    │
    ▼
Azure Load Balancer
(devops-lb)
    │
    ▼
Backend Pool
(devops-backend-pool)
    │
    ▼
Virtual Machine
(Nginx Server)
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

Authenticates Azure CLI with the Azure subscription.

---

# Step 2: Verify Azure Subscription

Run:

```bash
az account show
```

#### Explanation

Verifies the active Azure subscription and tenant.

---

# Step 3: Identify Resource Group

Run:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default resource group provided by the lab.

---

# Step 4: Create Public IP Address

Run:

```bash
az network public-ip create \
  --resource-group $RG_NAME \
  --name devops-lb-ip \
  --sku Standard \
  --allocation-method Static
```

#### Explanation

Creates a static public IP address for the Azure Load Balancer.

---

# Step 5: Create Azure Load Balancer

Run:

```bash
az network lb create \
  --resource-group $RG_NAME \
  --name devops-lb \
  --sku Standard \
  --public-ip-address devops-lb-ip \
  --frontend-ip-name devops-lb-ip \
  --backend-pool-name devops-backend-pool
```

#### Explanation

Creates the Azure Load Balancer with frontend and backend configurations.

---

# Step 6: Create Health Probe

Run:

```bash
az network lb probe create \
  --resource-group $RG_NAME \
  --lb-name devops-lb \
  --name devops-health-probe \
  --protocol tcp \
  --port 80
```

#### Explanation

Monitors backend VM health on port 80.

---

# Step 7: Create Load Balancing Rule

Run:

```bash
az network lb rule create \
  --resource-group $RG_NAME \
  --lb-name devops-lb \
  --name devops-lb-rule \
  --protocol tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --frontend-ip-name devops-lb-ip \
  --backend-pool-name devops-backend-pool \
  --probe-name devops-health-probe
```

#### Explanation

Routes incoming HTTP traffic to the backend VM.

---

# Step 8: Identify VM Network Interface

Run:

```bash
NIC_NAME=$(az vm nic list \
  --resource-group $RG_NAME \
  --vm-name devops-vm \
  --query "[0].id" \
  -o tsv | awk -F'/' '{print $NF}')

echo $NIC_NAME
```

#### Explanation

Retrieves the VM network interface attached to the Nginx server.

---

# Step 9: Add VM to Backend Pool

Run:

```bash
IPCONFIG_NAME=$(az network nic ip-config list \
  --resource-group $RG_NAME \
  --nic-name $NIC_NAME \
  --query "[0].name" \
  -o tsv)

az network nic ip-config address-pool add \
  --address-pool devops-backend-pool \
  --ip-config-name $IPCONFIG_NAME \
  --nic-name $NIC_NAME \
  --resource-group $RG_NAME \
  --lb-name devops-lb
```

#### Explanation

Registers the Virtual Machine as a backend target for the Load Balancer.

---

# Step 10: Allow HTTP Traffic in NSG

Run:

```bash
az vm open-port \
  --resource-group $RG_NAME \
  --name devops-vm \
  --port 80
```

#### Explanation

Creates an inbound NSG rule allowing HTTP traffic.

---

# Step 11: Verify Nginx Service

SSH into the VM:

```bash
ssh azureuser@<vm-public-ip>
```

Check Nginx:

```bash
sudo systemctl status nginx
```

If required:

```bash
sudo systemctl enable nginx
sudo systemctl restart nginx
```

#### Explanation

Ensures the web server is active and listening on port 80.

---

# Step 12: Verify Load Balancer Access

Retrieve Load Balancer Public IP:

```bash
az network public-ip show \
  --resource-group $RG_NAME \
  --name devops-lb-ip \
  --query ipAddress \
  -o tsv
```

Open:

```text
http://<load-balancer-public-ip>
```

Expected Output:

```text
Welcome to nginx!
```

#### Explanation

Confirms traffic is successfully reaching the backend VM through the Azure Load Balancer.

---

# Final Validation Checklist

✅ Azure Load Balancer created

✅ Frontend IP configuration configured

✅ Public IP address associated

✅ Backend pool created

✅ Virtual Machine added to backend pool

✅ Health probe configured on port 80

✅ Load balancing rule configured

✅ NSG allows HTTP traffic

✅ Nginx service running successfully

✅ Application accessible through Load Balancer public IP

---

# Common Issues & Fixes

| Issue                        | Resolution                         |
| ---------------------------- | ---------------------------------- |
| Backend unhealthy            | Verify Nginx service is running    |
| Health probe failed          | Ensure port 80 is open             |
| Website inaccessible         | Verify NSG inbound rule            |
| VM not receiving traffic     | Check backend pool association     |
| Load Balancer not responding | Validate frontend IP configuration |

---

# Best Practices

* Use health probes for backend monitoring
* Restrict only necessary ports in NSG
* Use Standard SKU Load Balancer for production workloads
* Monitor backend pool health regularly
* Design highly available architectures with Load Balancers

---

# Key Learnings

* Azure Load Balancer distributes traffic across backend resources
* Health probes determine backend availability
* NSGs play a critical role in network accessibility
* Backend pools connect application servers to Load Balancers
* Load balancing improves availability and scalability of cloud applications
