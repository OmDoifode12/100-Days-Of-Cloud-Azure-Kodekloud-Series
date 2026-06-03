# Day 20 – Deploy Azure Resources Using ARM Template

---

## Task Overview

The Nautilus DevOps team is adopting Infrastructure as Code (IaC) practices to automate Azure resource deployments. An existing ARM Template is available and needs to be modified before deployment.

The ARM Template file is located at:

```bash
/root/arm-templates/vnet-deployment-template.json
```

Required changes:

* Change the Virtual Network name to `arm-vnet-datacenter`
* Change the `displayName` tag to `arm-vnet-datacenter`
* Update the address prefix to `192.168.0.0/16`
* Add a new tag:

| Tag Name    | Value          |
| ----------- | -------------- |
| Environment | KKE-datacenter |

After updating the template, deploy it using Azure CLI.

To identify the resource group:

```bash
az group list --query '[].name' --output table | grep 'kml'
```

---

# Step-by-Step Implementation

### Step 1: Identify the Resource Group

Run:

```bash
az group list --query '[].name' --output table | grep 'kml'
```

Example Output:

```bash
kml_rg_main-145620288f634136
```

Store it in a variable:

```bash
RG_NAME=$(az group list --query '[].name' -o tsv | grep kml)
```

#### Explanation

This command finds the Resource Group where the deployment will be executed.

---

### Step 2: Check ARM Template File

Run:

```bash
ls -l /root/arm-templates/
```

#### Explanation

The provided template is usually read-only in KodeKloud labs.

---

### Step 3: Copy Template to Editable Location

Run:

```bash
cp /root/arm-templates/vnet-deployment-template.json /tmp/vnet-deployment-template.json
```

#### Explanation

Creates an editable copy of the ARM template.

---

### Step 4: Edit the ARM Template

Open the file:

```bash
vi /tmp/vnet-deployment-template.json
```

Update the template with the following values:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "name": "arm-vnet-datacenter",
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2023-11-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "arm-vnet-datacenter",
                "Environment": "KKE-datacenter"
            },
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "192.168.0.0/16"
                    ]
                }
            }
        }
    ],
    "outputs": {}
}
```

#### Explanation

The template now contains the required VNet name, tags, and address space.

---

### Step 5: Validate ARM Template

Run:

```bash
az deployment group validate \
  --resource-group $RG_NAME \
  --template-file /tmp/vnet-deployment-template.json
```

#### Explanation

Checks the template for syntax and deployment errors before deployment.

---

### Step 6: Deploy ARM Template

Run:

```bash
az deployment group create \
  --resource-group $RG_NAME \
  --template-file /tmp/vnet-deployment-template.json
```

#### Explanation

Deploys the Virtual Network defined in the ARM Template.

---

### Step 7: Verify Deployment

List Virtual Networks:

```bash
az network vnet list \
  --resource-group $RG_NAME \
  --output table
```

Expected Output:

```text
arm-vnet-datacenter
```

#### Explanation

Confirms successful VNet deployment.

---

### Step 8: Verify Address Space

Run:

```bash
az network vnet show \
  --resource-group $RG_NAME \
  --name arm-vnet-datacenter \
  --query addressSpace.addressPrefixes
```

Expected Output:

```json
[
  "192.168.0.0/16"
]
```

#### Explanation

Verifies the configured address range.

---

### Step 9: Verify Tags

Run:

```bash
az network vnet show \
  --resource-group $RG_NAME \
  --name arm-vnet-datacenter \
  --query tags
```

Expected Output:

```json
{
  "displayName": "arm-vnet-datacenter",
  "Environment": "KKE-datacenter"
}
```

#### Explanation

Confirms the required tags were applied successfully.

---

# Best Practices

* Store ARM Templates in version control repositories
* Validate templates before deployment
* Use tags for governance and resource management
* Follow Infrastructure as Code principles
* Automate deployments through CI/CD pipelines

---

# Key Learnings

* ARM Templates enable Infrastructure as Code in Azure
* Azure Resource Manager automates cloud deployments
* Templates provide repeatable and consistent infrastructure provisioning
* Tags improve governance and resource organization
* Azure CLI simplifies ARM template deployments
* Infrastructure as Code is a foundational DevOps skill
