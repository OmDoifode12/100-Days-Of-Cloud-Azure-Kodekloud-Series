# Day 39 – Deploying a Static Website Using Azure Storage

---

## Task Overview

As part of an internal information portal project, the Nautilus DevOps Team was tasked with hosting a static website using Azure Storage. The objective was to create a Storage Account, enable Static Website Hosting, upload the website's `index.html` file to the special `$web` container, and make the website publicly accessible through the Azure Storage Static Website endpoint.

The following requirements were completed:

* Create a Storage Account named `xfusionwebst14258`
* Configure the Storage Account in the `East US` region
* Enable Static Website Hosting
* Set `index.html` as the default document
* Upload the `index.html` file to the `$web` container
* Make the website publicly accessible
* Verify the website using the Azure Static Website endpoint

---

# Architecture Overview

```text
Azure Storage Account
xfusionwebst14258
        │
        ▼
Static Website Hosting
        │
        ▼
$web Container
        │
        ▼
index.html
        │
        ▼
Azure Static Website Endpoint
https://xfusionwebst14258.z13.web.core.windows.net/
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

# Step 2: Verify Resource Group

Run:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default Resource Group created for the lab.

---

# Step 3: Create Storage Account

Run:

```bash
az storage account create \
  --name xfusionwebst14258 \
  --resource-group $RG_NAME \
  --location eastus \
  --sku Standard_LRS
```

#### Explanation

Creates an Azure Storage Account using Locally Redundant Storage (LRS).

| Setting | Value |
|----------|-------|
| Storage Account | xfusionwebst14258 |
| Region | East US |
| Redundancy | Standard LRS |

---

# Step 4: Enable Static Website Hosting

Run:

```bash
az storage blob service-properties update \
  --account-name xfusionwebst14258 \
  --static-website \
  --index-document index.html
```

#### Explanation

Enables Static Website Hosting and configures `index.html` as the default landing page.

---

# Step 5: Retrieve Storage Account Access Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name xfusionwebst14258 \
  --query "[0].value" \
  -o tsv)

echo $ACCOUNT_KEY
```

#### Explanation

Retrieves the Storage Account access key required for authentication.

---

# Step 6: Upload Website Files

Run:

```bash
az storage blob upload \
  --account-name xfusionwebst14258 \
  --account-key $ACCOUNT_KEY \
  --container-name '$web' \
  --name index.html \
  --file /root/index.html
```

#### Explanation

Uploads the website's homepage to the automatically created `$web` container.

---

# Step 7: Retrieve Static Website URL

Run:

```bash
az storage account show \
  --name xfusionwebst14258 \
  --resource-group $RG_NAME \
  --query "primaryEndpoints.web" \
  -o tsv
```

Example Output:

```text
https://xfusionwebst14258.z13.web.core.windows.net/
```

#### Explanation

Displays the public endpoint for the hosted static website.

---

# Step 8: Verify Website Accessibility

Run:

```bash
curl $(az storage account show \
  --name xfusionwebst14258 \
  --resource-group $RG_NAME \
  --query "primaryEndpoints.web" \
  -o tsv)
```

Or simply open the website URL in a browser.

#### Explanation

Confirms that the static website is publicly accessible.

---

# Final Validation Checklist

✅ Storage Account created successfully

✅ Static Website Hosting enabled

✅ `$web` container created automatically

✅ `index.html` uploaded successfully

✅ Website accessible through Azure Storage URL

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| `$web` container not visible | Enabled Static Website Hosting first |
| Website returned 404 | Verified `index.html` was uploaded to the `$web` container |
| Static website endpoint unavailable initially | Waited a few moments after enabling Static Website Hosting |
| Upload failed | Verified Storage Account name and access key |
| Incorrect container selected | Uploaded files specifically to the `$web` container |

---

# Best Practices

* Use Azure Storage Static Website Hosting for lightweight web applications.
* Keep Storage Account names globally unique.
* Always upload website files to the `$web` container.
* Verify the Static Website endpoint after deployment.
* Use Azure CLI for repeatable infrastructure deployments.

---

# Key Learnings

* Hosting static websites using Azure Storage
* Enabling Static Website Hosting with Azure CLI
* Understanding the purpose of the `$web` container
* Uploading website files to Azure Blob Storage
* Accessing websites through Azure Storage Static Website endpoints
* Building cost-effective web hosting solutions without managing web servers
* Verifying public website accessibility after deployment
