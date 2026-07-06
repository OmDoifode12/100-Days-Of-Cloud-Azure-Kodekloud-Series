# Day 45: Azure Kubernetes Service (AKS) Setup and Management

## 📌 Objective

Deploy a private Azure Kubernetes Service (AKS) cluster with autoscaling, Kubernetes v1.33.0, and a high-availability node pool configured for production-ready workloads.

---

## 🏗️ Architecture

```text
                Azure AKS Cluster
                      │
        ┌─────────────┴─────────────┐
        │                           │
  Private API Server         System Node Pool
                                  │
                         Standard_D2s_v3 VM
                           Auto Scaling (1-2)
```

---

## ⚙️ Prerequisites

- Azure CLI
- Existing Resource Group
- Azure Subscription
- SSH Keys

---

## 🚀 Step 1: Get Resource Group

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

---

## 🚀 Step 2: Create Private AKS Cluster

```bash
az aks create \
--resource-group $RG_NAME \
--name xfusion-aks \
--location centralus \
--kubernetes-version 1.33.0 \
--node-vm-size Standard_D2s_v3 \
--node-count 1 \
--enable-cluster-autoscaler \
--min-count 1 \
--max-count 2 \
--load-balancer-sku standard \
--enable-private-cluster \
--generate-ssh-keys \
--enable-managed-identity
```

> **Note:**  
> `--disable-local-accounts` was removed because Kubernetes v1.25+ requires Microsoft Entra ID (Azure AD) integration for this option.

---

## 🚀 Step 3: Verify Cluster

```bash
az aks show \
-g $RG_NAME \
-n xfusion-aks \
--query "{State:provisioningState,Version:kubernetesVersion}"
```

Expected Output:

```json
{
  "State": "Succeeded",
  "Version": "1.33.0"
}
```

---

## 🚀 Step 4: Verify Private Cluster

```bash
az aks show \
-g $RG_NAME \
-n xfusion-aks \
--query "apiServerAccessProfile.enablePrivateCluster"
```

Expected Output:

```text
true
```

---

## 🚀 Step 5: Verify Node Pool

```bash
az aks show \
-g $RG_NAME \
-n xfusion-aks \
--query agentPoolProfiles
```

Verify:

- Node Size: `Standard_D2s_v3`
- Min Count: `1`
- Max Count: `2`
- Auto Scaling: Enabled

---

## 🚀 Step 6: Verify Monitoring Add-ons

```bash
az aks show \
-g $RG_NAME \
-n xfusion-aks \
--query addonProfiles
```

Expected Output:

```text
null
```

This confirms that **Container Insights and Monitoring are disabled.**

---

## 🚀 Step 7: Verify Node Pool Configuration

```bash
az aks show \
-g $RG_NAME \
-n xfusion-aks \
--query agentPoolProfiles
```

Verify:

- Kubernetes Version: **1.33.0**
- VM Size: **Standard_D2s_v3**
- Autoscaling: **Enabled**
- Minimum Nodes: **1**
- Maximum Nodes: **2**

---

## ✅ Validation Checklist

- AKS Cluster created
- Kubernetes Version **1.33.0**
- Central US Region
- Private Cluster Enabled
- Managed Identity Enabled
- Standard_D2s_v3 Node Size
- Autoscaling Enabled (1–2 Nodes)
- Only One System Node Pool
- Container Insights Disabled
- Cluster Status = Succeeded

---

# 💻 Azure CLI Commands Used

```bash
az group list

az aks create

az aks show

az aks show --query apiServerAccessProfile.enablePrivateCluster

az aks show --query agentPoolProfiles

az aks show --query addonProfiles
```

---

# ⚠️ Troubleshooting

### Error

```text
Since kubernetes version 1.25, disableLocalAccounts can only be set on Azure AD integration enabled cluster.
```

### Solution

Remove:

```bash
--disable-local-accounts
```

and recreate the cluster.

---

# 🎯 Key Learnings

- Azure Kubernetes Service (AKS) simplifies Kubernetes deployment and management.
- Private AKS clusters secure the Kubernetes API server using private endpoints.
- Cluster Autoscaler automatically adjusts node count based on workload demand.
- Managed Identity eliminates the need to manage service principal credentials.
- Disabling unnecessary monitoring reduces resource usage and operational costs.

---

## 📚 Technologies Used

- Microsoft Azure
- Azure Kubernetes Service (AKS)
- Kubernetes
- Azure CLI
- Managed Identity
- Private Cluster
- Cluster Autoscaler

---

## 📅 Challenge

**#100DaysOfCloud – Day 45**
