# Day 50: VM Setup and Configuration for Azure Application Gateway

## 📌 Objective

Deploy two Azure Virtual Machines running Nginx and configure an **Azure Application Gateway** to distribute HTTP traffic between them using Layer-7 load balancing.

---

## 🛠️ Services Used

- Azure Virtual Network (VNet)
- Azure Subnets
- Azure Network Security Group (NSG)
- Azure Virtual Machines
- Azure Application Gateway
- Azure Public IP
- Nginx
- Azure CLI
- SSH

---

## 📋 Task Overview

- Create a Virtual Network and dedicated subnets.
- Deploy two Ubuntu Virtual Machines.
- Install Nginx on both VMs.
- Configure different web pages on each VM.
- Create an Azure Application Gateway.
- Configure Backend Pool, HTTP Settings, Listener, and Routing Rule.
- Verify Layer-7 Load Balancing.

---

# Step 1: Get Resource Group

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

---

# Step 2: Generate SSH Key

```bash
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
```

---

# Step 3: Create Virtual Network

```bash
az network vnet create \
-g $RG_NAME \
-n datacenter-vnet \
-l eastus \
--address-prefix 10.0.0.0/16 \
--subnet-name datacenter-subnet \
--subnet-prefix 10.0.1.0/24
```

---

# Step 4: Create Application Gateway Subnet

```bash
az network vnet subnet create \
-g $RG_NAME \
--vnet-name datacenter-vnet \
-n datacenter-apgw-subnet \
--address-prefixes 10.0.2.0/24
```

---

# Step 5: Create Network Security Group

```bash
az network nsg create \
-g $RG_NAME \
-n datacenter-nsg
```

---

# Step 6: Allow SSH

```bash
az network nsg rule create \
-g $RG_NAME \
--nsg-name datacenter-nsg \
-n AllowSSH \
--priority 800 \
--direction Inbound \
--access Allow \
--protocol Tcp \
--destination-port-ranges 22
```

---

# Step 7: Allow HTTP

```bash
az network nsg rule create \
-g $RG_NAME \
--nsg-name datacenter-nsg \
-n AllowHTTP \
--priority 1000 \
--direction Inbound \
--access Allow \
--protocol Tcp \
--destination-port-ranges 80
```

---

# Step 8: Create VM1

```bash
az vm create \
-g $RG_NAME \
-n datacenter-vm1 \
-l eastus \
--image Ubuntu2204 \
--size Standard_B1s \
--admin-username azureuser \
--authentication-type ssh \
--ssh-key-values ~/.ssh/id_rsa.pub \
--vnet-name datacenter-vnet \
--subnet datacenter-subnet \
--nsg datacenter-nsg \
--storage-sku Standard_LRS
```

---

# Step 9: Create VM2

```bash
az vm create \
-g $RG_NAME \
-n datacenter-vm2 \
-l eastus \
--image Ubuntu2204 \
--size Standard_B1s \
--admin-username azureuser \
--authentication-type ssh \
--ssh-key-values ~/.ssh/id_rsa.pub \
--vnet-name datacenter-vnet \
--subnet datacenter-subnet \
--nsg datacenter-nsg \
--storage-sku Standard_LRS
```

---

# Step 10: Open HTTP Port

```bash
az vm open-port -g $RG_NAME -n datacenter-vm1 --port 80

az vm open-port -g $RG_NAME -n datacenter-vm2 --port 80
```

---

# Step 11: Get Public IPs

```bash
VM1=$(az vm show -d -g $RG_NAME -n datacenter-vm1 --query publicIps -o tsv)

VM2=$(az vm show -d -g $RG_NAME -n datacenter-vm2 --query publicIps -o tsv)

echo $VM1
echo $VM2
```

---

# Step 12: Configure VM1

```bash
ssh azureuser@$VM1
```

```bash
sudo apt update

sudo apt install nginx -y

echo "Welcome to KKE Labs:Version 1" | sudo tee /var/www/html/index.html

sudo systemctl enable nginx

sudo systemctl restart nginx

exit
```

---

# Step 13: Configure VM2

```bash
ssh azureuser@$VM2
```

```bash
sudo apt update

sudo apt install nginx -y

echo "Welcome to KKE Labs:Version 2" | sudo tee /var/www/html/index.html

sudo systemctl enable nginx

sudo systemctl restart nginx

exit
```

---

# Step 14: Verify Both VMs

```bash
curl http://$VM1
```

Expected Output

```text
Welcome to KKE Labs:Version 1
```

```bash
curl http://$VM2
```

Expected Output

```text
Welcome to KKE Labs:Version 2
```

---

# Azure Portal Configuration

## Create Application Gateway

- Name: **datacenter-apgw**
- Region: **East US**
- Tier: **Standard V2**

---

## Frontend IP

- Name: **datacenter-apgw-ip**
- Type: **Public**

---

## Networking

- VNet: **datacenter-vnet**
- Subnet: **datacenter-apgw-subnet**

---

## Backend Pool

Name:

```
datacenter-backendpool
```

Add Backend Targets:

- datacenter-vm1
- datacenter-vm2

---

## HTTP Settings

Name:

```
datacenter-http-settings
```

Protocol:

```
HTTP
```

Port:

```
80
```

---

## Listener

Name:

```
datacenter-listener
```

Protocol:

```
HTTP
```

Frontend Port:

```
80
```

---

## Routing Rule

Name:

```
datacenter-routing-rule
```

Rule Type:

```
Basic
```

Backend Pool:

```
datacenter-backendpool
```

HTTP Settings:

```
datacenter-http-settings
```

---

## Wait for Deployment

Wait until the Application Gateway provisioning state becomes:

```
Succeeded
```

---

# Verify Application Gateway

Open:

```
http://<Application Gateway Public IP>
```

Refresh multiple times.

Expected Output:

```
Welcome to KKE Labs:Version 1
```

or

```
Welcome to KKE Labs:Version 2
```

---

# Final Validation Checklist

- ✅ VNet created
- ✅ Two Subnets created
- ✅ NSG configured
- ✅ Two Ubuntu Virtual Machines deployed
- ✅ Nginx installed on both VMs
- ✅ Version 1 page configured
- ✅ Version 2 page configured
- ✅ Azure Application Gateway deployed
- ✅ Backend Pool configured
- ✅ HTTP Listener configured
- ✅ Routing Rule configured
- ✅ Traffic successfully load balanced between both VMs

---

## 🎯 Outcome

Successfully deployed a highly available web application architecture using **Azure Application Gateway** as a Layer-7 Load Balancer. The Application Gateway distributed incoming HTTP requests across two Nginx web servers hosted on Azure Virtual Machines, demonstrating enterprise-grade traffic management, scalability, and high availability.
