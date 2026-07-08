# Day 48: VM and ACR Integration for Storage

## 📌 Objective

Deploy a containerized Python application using Azure Container Registry (ACR), host it on an Azure Virtual Machine, integrate Azure Blob Storage for configuration management, and validate the application through a web browser.

---

## 🏗️ Architecture

```text
                Dockerfile
                    │
             Docker Build
                    │
                    ▼
      Azure Container Registry (ACR)
                    │
            Docker Pull on VM
                    │
                    ▼
         Azure Virtual Machine
                    │
     Mount config.json from Blob Storage
                    │
                    ▼
          Python Docker Container
                    │
                 Port 80
                    │
                    ▼
                Web Browser
```

---

## ⚙️ Prerequisites

- Azure CLI
- Docker
- SSH Key Pair
- Existing Resource Group

---

## 🚀 Step 1: Get Resource Group

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

---

## 🚀 Step 2: Create SSH Key

```bash
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
```

---

## 🚀 Step 3: Create Azure Virtual Machine

```bash
az vm create \
-g $RG_NAME \
-n nautilus-vm \
-l eastus \
--image Ubuntu2204 \
--size Standard_B1s \
--admin-username azureuser \
--authentication-type ssh \
--ssh-key-values ~/.ssh/id_rsa.pub \
--storage-sku Standard_LRS
```

---

## 🚀 Step 4: Open HTTP Port

```bash
az vm open-port \
-g $RG_NAME \
-n nautilus-vm \
--port 80
```

---

## 🚀 Step 5: Get VM Public IP

```bash
VM_IP=$(az vm show -d \
-g $RG_NAME \
-n nautilus-vm \
--query publicIps \
-o tsv)

echo $VM_IP
```

---

## 🚀 Step 6: Create Azure Container Registry

```bash
az acr create \
-g $RG_NAME \
-n nautilusacr21503 \
-l eastus \
--sku Basic
```

---

## 🚀 Step 7: Enable Admin User

```bash
az acr update \
-n nautilusacr21503 \
--admin-enabled true
```

---

## 🚀 Step 8: Login to ACR

```bash
az acr login \
-n nautilusacr21503
```

---

## 🚀 Step 9: Build Docker Image

```bash
cd /root/pyapp

docker build \
-t nautilus/python-app:latest .
```

---

## 🚀 Step 10: Tag Image

```bash
docker tag \
nautilus/python-app:latest \
nautilusacr21503.azurecr.io/nautilus/python-app:latest
```

---

## 🚀 Step 11: Push Image to ACR

```bash
docker push \
nautilusacr21503.azurecr.io/nautilus/python-app:latest
```

---

## 🚀 Step 12: Verify Repository

```bash
az acr repository list \
-n nautilusacr21503 \
-o table
```

Expected Output:

```text
nautilus/python-app
```

---

## 🚀 Step 13: Create Storage Account

```bash
az storage account create \
-g $RG_NAME \
-n nautilusstor21503 \
-l eastus \
--sku Standard_LRS
```

---

## 🚀 Step 14: Get Storage Account Key

```bash
ACCOUNT_KEY=$(az storage account keys list \
-g $RG_NAME \
-n nautilusstor21503 \
--query "[0].value" \
-o tsv)
```

---

## 🚀 Step 15: Create Blob Container

```bash
az storage container create \
--account-name nautilusstor21503 \
--account-key "$ACCOUNT_KEY" \
--name nautilus-config
```

---

## 🚀 Step 16: Upload Configuration File

```bash
az storage blob upload \
--account-name nautilusstor21503 \
--account-key "$ACCOUNT_KEY" \
--container-name nautilus-config \
--name config.json \
--file /root/config.json
```

---

## 🚀 Step 17: Verify Blob Upload

```bash
az storage blob list \
--account-name nautilusstor21503 \
--account-key "$ACCOUNT_KEY" \
--container-name nautilus-config \
-o table
```

Expected Output:

```text
config.json
```

---

## 🚀 Step 18: SSH into the VM

```bash
ssh azureuser@$VM_IP
```

---

## 🚀 Step 19: Install Docker

```bash
curl -fsSL https://get.docker.com | sudo sh

sudo usermod -aG docker azureuser

newgrp docker
```

---

## 🚀 Step 20: Install Azure CLI

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

---

## 🚀 Step 21: Login to ACR from VM

```bash
docker login \
nautilusacr21503.azurecr.io \
-u nautilusacr21503 \
-p <ACR_PASSWORD>
```

---

## 🚀 Step 22: Pull Docker Image

```bash
docker pull \
nautilusacr21503.azurecr.io/nautilus/python-app:latest
```

---

## 🚀 Step 23: Copy Configuration File

```bash
scp ~/.ssh/id_rsa \
/root/config.json \
azureuser@$VM_IP:/home/azureuser/config.json
```

---

## 🚀 Step 24: Run the Container

```bash
docker run -d \
--name python-app \
-p 80:80 \
-v /home/azureuser/config.json:/app/config.json \
nautilusacr21503.azurecr.io/nautilus/python-app:latest
```

---

## 🚀 Step 25: Verify Container

```bash
docker ps
```

Expected Output:

```text
python-app

0.0.0.0:80->80/tcp
```

---

## 🚀 Step 26: Test Application

```bash
curl http://localhost
```

Expected Output:

```text
Welcome to KKE Azure Labs:
{'key':'value','version':1}
```

Open Browser:

```text
http://<VM_PUBLIC_IP>
```

---

# 💻 Azure CLI Commands Used

```bash
az vm create
az vm show
az vm open-port
az acr create
az acr update
az acr login
az acr repository list
az storage account create
az storage account keys list
az storage container create
az storage blob upload
az storage blob list
```

---

## ✅ Validation Checklist

- Azure VM created
- Docker installed
- Azure CLI installed
- Azure Container Registry created
- Docker image built successfully
- Docker image pushed to ACR
- Repository name is `nautilus/python-app`
- Storage Account created
- Blob Container created
- `config.json` uploaded successfully
- Docker image pulled on VM
- Configuration file mounted successfully
- Container running on port 80
- Application accessible via browser

---

## ⚠️ Challenges Faced

- ACR admin account was disabled, causing Docker authentication failure.
- A directory named `config.json` was accidentally created instead of a file, preventing Docker volume mounting.
- Fixed by enabling the ACR admin user, recreating the configuration file correctly, and mounting it into the container.

---

## 🎯 Key Learnings

- Building and pushing Docker images to Azure Container Registry (ACR).
- Deploying containerized applications on Azure Virtual Machines.
- Managing application configuration with Azure Blob Storage.
- Using Docker volume mounts for external configuration files.
- Troubleshooting Docker authentication, volume mounts, and Azure resource integration.

---

## 📚 Technologies Used

- Microsoft Azure
- Azure Virtual Machine
- Azure Container Registry (ACR)
- Azure Blob Storage
- Docker
- Azure CLI
- Linux
- SSH

---

## 📅 Challenge

**#100DaysOfCloud – Day 48**
```
