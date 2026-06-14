# Day 31 – Deploying and Managing a Python Web Application on Azure

---

## Task Overview

The Nautilus DevOps Team was tasked with deploying a Python-based web application using Azure App Services. The objective was to create a Linux-based Azure Web App along with a dedicated App Service Plan and configure the required runtime, monitoring, and tagging settings.

The following requirements were completed:

* Create a Web App named `xfusion-webapp`
* Configure Publish option as `Code`
* Use Python runtime with Linux operating system
* Create a new App Service Plan named `xfusion-learn-python`
* Configure SKU as `Basic B1`
* Disable Application Insights
* Add custom tags
* Ensure the Web App is in Running state

Region:

| Location   |
| ---------- |
| Central US |

---

# Architecture Overview

```text id="x7m2q5"
Azure App Service Plan
        │
        ▼
xfusion-learn-python
        │
        ▼
Azure Web App
        │
        ▼
xfusion-webapp
(Python + Linux)
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run:

```bash id="p4x8m1"
az login
```

#### Explanation

Authenticates Azure CLI with the Azure subscription.

---

# Step 2: Verify Azure Subscription

Run:

```bash id="v1m6q9"
az account show
```

#### Explanation

This verifies the currently active Azure subscription.

---

# Step 3: Set Resource Group Variable

Run:

```bash id="k8m3x2"
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Stores the default Resource Group name.

---

# Step 4: Create App Service Plan

Run:

```bash id="q5v1m7"
az appservice plan create \
  --name xfusion-learn-python \
  --resource-group $RG_NAME \
  --location centralus \
  --sku B1 \
  --is-linux
```

#### Explanation

Creates a Linux-based App Service Plan.

| Setting   | Value                  |
| --------- | ---------------------- |
| Plan Name | `xfusion-learn-python` |
| SKU       | `Basic B1`             |
| OS        | Linux                  |

---

# Step 5: Create Python Web App

Run:

```bash id="t4m9x2"
az webapp create \
  --resource-group $RG_NAME \
  --plan xfusion-learn-python \
  --name xfusion-webapp \
  --runtime "PYTHON|3.11"
```

#### Explanation

Creates the Azure Web App using Python runtime on Linux.

---

# Step 6: Disable Application Insights

Run:

```bash id="n6q1v8"
az monitor app-insights component delete \
  --app xfusion-webapp \
  --resource-group $RG_NAME
```

If Application Insights does not exist, safely ignore the warning.

#### Explanation

Disables Application Insights monitoring as required.

---

# Step 7: Add Tags to Web App

Run:

```bash id="k3x7m5"
az tag create --resource-id $(az webapp show \
  --resource-group $RG_NAME \
  --name xfusion-webapp \
  --query id -o tsv) \
  --tags WebAppLearning=Environment Environment=Dev
```

#### Explanation

Adds custom Azure tags to the Web App resource.

---

# Step 8: Verify Web App State

Run:

```bash id="r2p8q4"
az webapp show \
  --resource-group $RG_NAME \
  --name xfusion-webapp \
  --query state \
  -o tsv
```

Expected Output:

```text id="v5m1x9"
Running
```

#### Explanation

Confirms the Web App is successfully deployed and running.

---

# Step 9: Verify Web App URL

Run:

```bash id="q7p3m2"
az webapp show \
  --resource-group $RG_NAME \
  --name xfusion-webapp \
  --query defaultHostName \
  -o tsv
```

Expected Output:

```text id="m4v8q1"
xfusion-webapp.azurewebsites.net
```

Open in browser:

```text id="x9m2p5"
https://xfusion-webapp.azurewebsites.net
```

---

# Final Validation Checklist

✅ Azure Web App created successfully
✅ Python runtime configured correctly
✅ Linux operating system selected
✅ App Service Plan created successfully
✅ Basic B1 SKU configured
✅ Application Insights disabled
✅ Tags added successfully
✅ Web App verified in Running state

---

# Common Issues & Fixes

| Issue                             | Resolution                    |
| --------------------------------- | ----------------------------- |
| Web App name unavailable          | Use globally unique name      |
| Runtime stack mismatch            | Verify Python runtime version |
| App not starting                  | Restart Web App               |
| Application Insights auto-enabled | Disable manually              |
| Missing tags                      | Add using Azure CLI           |

---

# Best Practices

* Use Linux-based App Services for Python applications
* Use proper resource tagging strategies
* Disable unnecessary services in development environments
* Monitor App Service health regularly
* Use App Service Plans based on workload requirements

---

# Key Learnings

* Azure App Services simplify cloud web hosting
* Python applications can run efficiently on Linux App Services
* App Service Plans define compute and scaling resources
* Azure tags improve resource organization and management
* Azure CLI enables fast and repeatable web application deployments
