# Day 49: VM Setup and Web Storage Integration

## 📌 Objective

Host a static website on an Azure Virtual Machine by securely downloading `index.html` from a private Azure Blob Storage container and serving it using **Nginx**.

---

## 🛠️ Services Used

- Azure Virtual Network (VNet)
- Azure Subnet
- Azure Virtual Machine
- Azure Storage Account
- Azure Blob Storage
- Azure CLI
- Nginx
- SSH

---

## 📋 Task Overview

- Create a Virtual Network and Subnet.
- Create a private Azure Storage Account.
- Create a Blob Container.
- Upload `index.html` to Blob Storage.
- Deploy an Azure Virtual Machine.
- Install Nginx.
- Download `index.html` from Blob Storage using Azure CLI.
- Configure Nginx to serve the downloaded file.
- Verify the application through the VM Public IP.

---

# Step 1: Get Resource Group

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

---

# Step 2: Create Virtual Network

```bash
az network vnet create \
-g $RG_NAME \
-n devops-vnet \
-l eastus \
--address-prefix 10.0.0.0/16 \
--subnet-name devops-subnet \
--subnet-prefix 10.0.1.0/24
```

---

# Step 3: Create Storage Account

```bash
az storage account create \
-g $RG_NAME \
-n devopsstor7470 \
-l eastus \
--sku Standard_LRS \
--allow-blob-public-access false
```

---

# Step 4: Get Storage Account Key

```bash
ACCOUNT_KEY=$(az storage account keys list \
-g $RG_NAME \
-n devopsstor7470 \
--query "[0].value" \
-o tsv)

echo $ACCOUNT_KEY
```

---

# Step 5: Create Blob Container

```bash
az storage container create \
--account-name devopsstor7470 \
--account-key "$ACCOUNT_KEY" \
--name devops-container
```

---

# Step 6: Upload index.html

```bash
az storage blob upload \
--account-name devopsstor7470 \
--account-key "$ACCOUNT_KEY" \
--container-name devops-container \
--name index.html \
--file /root/index.html
```

---

# Step 7: Generate SSH Key

```bash
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
```

---

# Step 8: Create Virtual Machine

```bash
az vm create \
-g $RG_NAME \
-n devops-vm \
-l eastus \
--image Ubuntu2204 \
--size Standard_B1s \
--admin-username azureuser \
--authentication-type ssh \
--ssh-key-values ~/.ssh/id_rsa.pub \
--vnet-name devops-vnet \
--subnet devops-subnet
```

---

# Step 9: Open HTTP Port

```bash
az vm open-port \
-g $RG_NAME \
-n devops-vm \
--port 80
```

---

# Step 10: Get VM Public IP

```bash
VM_IP=$(az vm show -d \
-g $RG_NAME \
-n devops-vm \
--query publicIps \
-o tsv)

echo $VM_IP
```

---

# Step 11: Connect to VM

```bash
ssh azureuser@$VM_IP
```

---

# Step 12: Install Azure CLI

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

---

# Step 13: Install Nginx

```bash
sudo apt update

sudo apt install nginx -y

sudo systemctl enable nginx

sudo systemctl start nginx
```

---

# Step 14: Download index.html

```bash
sudo az storage blob download \
--account-name devopsstor7470 \
--account-key <ACCOUNT_KEY> \
--container-name devops-container \
--name index.html \
--file /var/www/html/index.html
```

---

# Step 15: Restart Nginx

```bash
sudo systemctl restart nginx
```

---

# Step 16: Verify Website

```bash
curl http://localhost
```

Expected Output

```html
<!DOCTYPE html>
<html>
...
<h1>Welcome to KKE Azure Labs!</h1>
...
</html>
```

---

# Step 17: Verify Blob

```bash
az storage blob list \
--account-name devopsstor7470 \
--account-key "$ACCOUNT_KEY" \
--container-name devops-container \
-o table
```

---

# Step 18: Verify Storage Account

```bash
az storage account show \
-g $RG_NAME \
-n devopsstor7470 \
--query allowBlobPublicAccess
```

Expected Output

```text
false
```

---

# Step 19: Verify VM

```bash
az vm list -d -o table
```

---

# Final Validation

- ✅ VNet created
- ✅ Subnet created
- ✅ Private Storage Account created
- ✅ Blob Container created
- ✅ index.html uploaded
- ✅ VM deployed
- ✅ Nginx installed
- ✅ index.html downloaded from Blob Storage
- ✅ Website accessible through VM Public IP

---

## 🎯 Outcome

Successfully deployed a secure web server on an Azure Virtual Machine where the static website was fetched from a **private Azure Blob Storage container** and served through **Nginx**, demonstrating secure content delivery without exposing the Storage Account publicly.
