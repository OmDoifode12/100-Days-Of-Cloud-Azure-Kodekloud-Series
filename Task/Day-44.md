# Day 44: Integrating Azure Event Hub with Virtual Machines

## 📌 Objective
Integrate an Azure Virtual Machine with Azure Event Hubs for centralized log collection by creating an Event Hubs Namespace, Event Hub, and verifying log ingestion using Event Hub metrics.

---

## 🏗️ Architecture

```
Azure VM
    │
    │ Execute Python Script
    ▼
Azure Event Hub
    │
    ▼
Event Hub Metrics
(Incoming Messages, Requests)
```

---

## ⚙️ Prerequisites

- Azure CLI
- Existing Resource Group
- Existing VM: `datacenter-vm`
- Python3 installed on VM
- Existing `send_logs.py` script

---

## 🚀 Step 1: Get Resource Group

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

---

## 🚀 Step 2: Create Event Hubs Namespace

```bash
az eventhubs namespace create \
  --resource-group $RG_NAME \
  --name datacenter-namespace \
  --location eastus \
  --sku Standard \
  --enable-auto-inflate true \
  --maximum-throughput-units 2
```

---

## 🚀 Step 3: Create Event Hub

```bash
az eventhubs eventhub create \
  --resource-group $RG_NAME \
  --namespace-name datacenter-namespace \
  --name datacenter-hub
```

---

## 🚀 Step 4: Get VM Public IP

```bash
az vm show -d \
  --resource-group $RG_NAME \
  --name datacenter-vm \
  --query publicIps \
  -o tsv
```

---

## 🚀 Step 5: SSH into VM

```bash
ssh azureuser@<VM_PUBLIC_IP>
```

---

## 🚀 Step 6: Execute Python Script

```bash
python3 /home/azureuser/send_logs.py
```

Run it multiple times:

```bash
for i in {1..10}
do
    python3 /home/azureuser/send_logs.py
done
```

Expected Output:

```
Message Sent Successfully
```

---

## 🚀 Step 7: Verify Event Hub Metrics

Azure Portal →

```
Event Hubs
    ↓
datacenter-namespace
    ↓
Overview
```

Verify:

- Incoming Requests
- Incoming Messages
- Throughput

---

## ✅ Validation Checklist

- Event Hubs Namespace created
- Standard SKU selected
- Auto Inflate enabled
- Event Hub created
- Existing VM used
- Python script executed multiple times
- Incoming Messages visible in Event Hub metrics

---

# 💻 Azure CLI Commands Used

```bash
az group list

az eventhubs namespace create

az eventhubs eventhub create

az vm show

ssh

python3 /home/azureuser/send_logs.py
```

---

# 🎯 Key Learnings

- Azure Event Hubs is a highly scalable event streaming platform.
- Auto Inflate automatically scales throughput units.
- Applications can stream logs to Event Hubs using Python.
- Event Hub Metrics help verify successful event ingestion.
- Event Hubs are commonly used for centralized logging, telemetry, and real-time analytics in cloud-native architectures.

---

## 📚 Technologies Used

- Microsoft Azure
- Azure Event Hubs
- Azure Virtual Machines
- Azure CLI
- Python
- Event Streaming
- Cloud Monitoring

---

## 📅 Challenge

**#100DaysOfCloud – Day 44**
