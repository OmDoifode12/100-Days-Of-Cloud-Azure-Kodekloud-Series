# Day 29 – Working with Azure Container Registry (ACR)

---

## Task Overview

The Nautilus DevOps Team was tasked with setting up an Azure Container Registry (ACR) to store and manage Docker container images. The goal was to create an ACR instance, build a Docker image using an existing Dockerfile, and push the image to the Azure Container Registry repository.

The following requirements were completed:

* Create an Azure Container Registry named `nautilusacr13657`
* Configure the pricing tier as `Basic`
* Build a Docker image using the Dockerfile located at `/root/pyapp`
* Push the Docker image to ACR using the `latest` tag

Region:

| Location |
| -------- |
| East US  |

---

# Architecture Overview

```text id="m4x8q2"
Dockerfile
    │
    ▼
Docker Build
    │
    ▼
Azure Container Registry (ACR)
    │
    ▼
Docker Push → nautilusacr13657:latest
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run the following command:

```bash id="p7m2x5"
az login
```

Follow the authentication instructions displayed in the browser or terminal.

#### Explanation

This command authenticates Azure CLI with your Azure subscription.

---

# Step 2: Verify Azure Subscription

Run:

```bash id="v1q8m4"
az account show
```

#### Explanation

This verifies the currently active Azure subscription and authentication status.

---

# Step 3: Set Resource Group Variable

Run:

```bash id="k5x1m9"
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

This stores the Resource Group name into a variable for easier command execution.

---

# Step 4: Create Azure Container Registry (ACR)

Run:

```bash id="r3m7q2"
az acr create \
  --resource-group $RG_NAME \
  --name nautilusacr13657 \
  --sku Basic \
  --location eastus
```

#### Explanation

This command creates:

| Setting       | Value              |
| ------------- | ------------------ |
| Registry Name | `nautilusacr13657` |
| Pricing Tier  | `Basic`            |
| Region        | `East US`          |

---

# Step 5: Verify ACR Creation

Run:

```bash id="x9p2m5"
az acr list --output table
```

Expected Output:

| Name               |
| ------------------ |
| `nautilusacr13657` |

#### Explanation

This confirms the Azure Container Registry was created successfully.

---

# Step 6: Login to Azure Container Registry

Run:

```bash id="m6q1x8"
az acr login --name nautilusacr13657
```

#### Explanation

This authenticates Docker with Azure Container Registry.

---

# Step 7: Navigate to Dockerfile Directory

Run:

```bash id="t4m8q1"
cd /root/pyapp
```

Verify Dockerfile exists:

```bash id="q2x7m5"
ls
```

Expected Output:

```text id="p8m4x1"
Dockerfile
```

#### Explanation

The Docker image will be built using this Dockerfile.

---

# Step 8: Build Docker Image

Run:

```bash id="n5q2m8"
docker build -t nautilusacr13657.azurecr.io/nautilusacr13657:latest .
```

#### Explanation

This command:

* Builds the Docker image
* Tags it correctly for Azure Container Registry
* Uses the `latest` image tag

---

# Step 9: Verify Docker Images

Run:

```bash id="v7m1x4"
docker images
```

Expected Output:

| Repository                                     |
| ---------------------------------------------- |
| `nautilusacr13657.azurecr.io/nautilusacr13657` |

#### Explanation

This confirms the Docker image was built successfully.

---

# Step 10: Push Docker Image to ACR

Run:

```bash id="k9q3m1"
docker push nautilusacr13657.azurecr.io/nautilusacr13657:latest
```

#### Explanation

This uploads the Docker image to Azure Container Registry.

---

# Step 11: Verify Repository in ACR

Run:

```bash id="m2x8q5"
az acr repository list \
  --name nautilusacr13657 \
  --output table
```

Expected Output:

| Repository         |
| ------------------ |
| `nautilusacr13657` |

---

# Step 12: Verify Image Tag

Run:

```bash id="p4v9m2"
az acr repository show-tags \
  --name nautilusacr13657 \
  --repository nautilusacr13657 \
  --output table
```

Expected Output:

| Tag      |
| -------- |
| `latest` |

#### Explanation

This confirms that the Docker image was successfully pushed to ACR.

---

# Final Validation Checklist

✅ Azure Container Registry created successfully
✅ Pricing Tier configured as Basic
✅ Docker image built successfully
✅ Docker image tagged correctly
✅ Docker image pushed to ACR
✅ Repository verified in Azure Container Registry
✅ `latest` image tag verified successfully

---

# Common Issues & Fixes

| Issue                       | Resolution                  |
| --------------------------- | --------------------------- |
| `docker: command not found` | Ensure Docker is installed  |
| ACR login failed            | Run `az login` again        |
| Push access denied          | Verify image tagging format |
| Build failed                | Validate Dockerfile syntax  |
| Authentication error        | Run `az acr login` again    |

---

# Best Practices

* Use private registries for secure image storage
* Apply proper image version tagging
* Use Basic SKU for learning and development environments
* Avoid pushing unnecessary large images
* Regularly clean unused container images

---

# Key Learnings

* Azure Container Registry (ACR) securely stores Docker images
* Docker images can be built directly from Dockerfiles
* Azure CLI simplifies container registry management
* Proper image tagging is critical for deployments
* ACR integrates seamlessly with containerized cloud-native workflows
