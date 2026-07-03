# Day 43 – Configuring Azure VM with Application Gateway

---

## Task Overview

As part of building a highly available web application infrastructure, the Nautilus DevOps Team configured an Azure Virtual Machine behind an Azure Application Gateway. The objective was to deploy an Nginx web server on a Linux VM, secure it with a Network Security Group (NSG), and expose it through an Azure Application Gateway for intelligent Layer 7 traffic routing.

The following requirements were completed:

* Create a Network Security Group named `nautilus-nsg`
* Allow inbound HTTP (TCP Port 80)
* Create an Ubuntu Virtual Machine named `nautilus-vm`
* Configure SSH Public Key Authentication
* Install and start Nginx automatically using Cloud-Init
* Create an Azure Application Gateway named `nautilus-agw`
* Create Public IP `nautilus-agw-ip`
* Create Backend Pool `nautilus-backendpool`
* Configure HTTP Settings on Port 80
* Create Listener `nautilus-listener`
* Create Routing Rule `nautilus-routing-rule`
* Verify Nginx is accessible through the Application Gateway Public IP

---

# Architecture Overview

```text
                    Internet
                        │
                        ▼
            Public IP (nautilus-agw-ip)
                        │
                        ▼
        Azure Application Gateway
             (nautilus-agw)
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼
 Backend Pool (nautilus-backendpool)
                        │
                        ▼
          Ubuntu Virtual Machine
             (nautilus-vm)
                        │
                        ▼
               Nginx Web Server
```

---

# Step-by-Step Implementation

---

# Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

Authenticates Azure CLI with your Azure subscription.

---

# Step 2: Get Resource Group

Run:

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default Resource Group for the lab.

---

# Step 3: Create Network Security Group

Run:

```bash
az network nsg create \
  --resource-group $RG_NAME \
  --name nautilus-nsg \
  --location westus
```

#### Explanation

Creates the NSG that will protect the virtual machine.

---

# Step 4: Allow HTTP Traffic

Run:

```bash
az network nsg rule create \
  --resource-group $RG_NAME \
  --nsg-name nautilus-nsg \
  --name Allow-HTTP \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --destination-port-ranges 80
```

#### Explanation

Allows inbound HTTP traffic to the VM.

---

# Step 5: Generate SSH Key (If Needed)

Run:

```bash
ls ~/.ssh
```

If `id_rsa.pub` does not exist:

```bash
ssh-keygen -t rsa
```

#### Explanation

Creates an SSH key pair for VM authentication.

---

# Step 6: Create Cloud-Init Script

Run:

```bash
cat > cloud-init.txt <<EOF
#cloud-config
package_update: true
packages:
 - nginx
runcmd:
 - systemctl enable nginx
 - systemctl start nginx
EOF
```

#### Explanation

Automatically installs and starts Nginx during VM provisioning.

---

# Step 7: Create Ubuntu Virtual Machine

Run:

```bash
az vm create \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --location westus \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --authentication-type ssh \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --storage-sku Standard_LRS \
  --nsg nautilus-nsg \
  --custom-data cloud-init.txt
```

#### Explanation

Deploys the Ubuntu VM and installs Nginx automatically.

---

# Step 8: Verify Nginx

Run:

```bash
az vm run-command invoke \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --command-id RunShellScript \
  --scripts "systemctl status nginx"
```

Expected Output:

```text
active (running)
```

#### Explanation

Verifies that the Nginx service is running.

---

# Step 9: Create Azure Application Gateway (Azure Portal)

Perform the following steps:

- Create Application Gateway named `nautilus-agw`
- Region: **West US**
- SKU: **Standard V2**
- Create Public IP `nautilus-agw-ip`
- Create Backend Pool `nautilus-backendpool`
- Add VM `nautilus-vm` to Backend Pool
- Create HTTP Setting `nautilus-http-settings` on Port **80**
- Create Listener `nautilus-listener`
- Create Routing Rule `nautilus-routing-rule`
- Create Application Gateway Subnet if required
- Wait until deployment completes successfully

#### Explanation

Azure Application Gateway distributes HTTP traffic to backend virtual machines.

---

# Step 10: Validate Deployment

Open the Application Gateway Public IP in a browser.

Expected Output:

```text
Welcome to nginx!
```

#### Explanation

Confirms that traffic is successfully routed through the Application Gateway.

---

# Final Validation Checklist

✅ Network Security Group created

✅ HTTP rule configured

✅ Ubuntu Virtual Machine deployed

✅ SSH authentication configured

✅ Cloud-Init installed Nginx automatically

✅ Nginx service running

✅ Application Gateway deployed

✅ Backend Pool configured

✅ HTTP Settings configured

✅ Listener configured

✅ Routing Rule configured

✅ Nginx accessible through Application Gateway Public IP

---

# Issues Faced During the Lab

| Issue | Resolution |
|--------|------------|
| Application Gateway deployment took 10–15 minutes | Waited until provisioning state became **Succeeded** |
| Backend Health initially unhealthy | Verified Nginx service and NSG HTTP rule |
| Understanding Application Gateway components | Mapped Listener → Routing Rule → HTTP Settings → Backend Pool |
| Cloud-Init execution verification | Used Azure Run Command to confirm Nginx was running |

---

# Best Practices

* Use Cloud-Init for automated VM provisioning.
* Always restrict NSG rules to required ports only.
* Wait until Application Gateway provisioning is fully complete before testing.
* Monitor Backend Health after deployment.
* Use Application Gateway for Layer 7 traffic routing and high availability.

---

# Key Learnings

* Creating Azure Virtual Machines with Azure CLI
* Automating server configuration using Cloud-Init
* Working with Azure Network Security Groups
* Understanding Azure Application Gateway architecture
* Configuring Backend Pools and HTTP Settings
* Creating Listeners and Routing Rules
* Managing Layer 7 traffic routing in Azure
* Deploying scalable and production-ready web applications
