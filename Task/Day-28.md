# Day 28 – Troubleshooting Public Virtual Network Configurations

---

## Task Overview

The Nautilus DevOps Team attempted to deploy an Nginx server on an Azure Virtual Machine inside a public Virtual Network, but the server remained inaccessible from the internet due to networking and routing issues.

As part of this task, the following objectives needed to be completed:

* Verify the VNet configuration and internet connectivity
* Attach an existing Public IP to the VM
* Configure Network Security Group (NSG) rules
* Ensure HTTP traffic on port 80 is allowed
* Install and configure Nginx on the VM
* Troubleshoot internet access and connectivity issues

The following resources were used:

| Resource        | Name            |
| --------------- | --------------- |
| Virtual Network | `nautilus-vnet` |
| Virtual Machine | `nautilus-vm`   |
| Public IP       | `nautilus-pip`  |

Region:

| Location |
| -------- |
| West US  |

---

# Architecture Overview

```text id="x4m8q2"
Internet
    │
    ▼
nautilus-pip (Public IP)
    │
    ▼
nautilus-vm
    │
    ▼
nautilus-vmNSG
(Allow HTTP & SSH)
    │
    ▼
nautilus-vnet
```

---

# Step-by-Step Implementation Using Azure Portal

---

# Step 1: Login to Azure Portal

Open:

https://portal.azure.com

Login using the Azure credentials provided in the lab.

#### Explanation

The Azure Portal provides a graphical interface to manage Azure resources and troubleshoot infrastructure.

---

# Step 2: Open Virtual Machine

Search:

| Search           |
| ---------------- |
| Virtual Machines |

Open:

| VM            |
| ------------- |
| `nautilus-vm` |

#### Explanation

This VM was already created but inaccessible due to networking issues.

---

# Step 3: Attach Existing Public IP Address

Inside the VM page:

Navigate to:

```text id="k2p7m4"
Networking
```

Click the attached Network Interface.

Then go to:

```text id="m5x1q8"
IP Configurations
```

Open the existing IP configuration.

Under:

| Setting           | Value          |
| ----------------- | -------------- |
| Public IP Address | `nautilus-pip` |

Click:

```text id="v9m2x5"
Save
```

#### Explanation

A Public IP is required for internet access to the Virtual Machine.

---

# Step 4: Configure NSG Inbound Rule for HTTP

Return to:

```text id="q4x8m1"
Networking
```

Under:

```text id="r7m2q5"
Inbound port rules
```

Click:

```text id="t1v9m4"
+ Add inbound port rule
```

Configure:

| Setting                 | Value      |
| ----------------------- | ---------- |
| Source                  | Any        |
| Source Port Ranges      | *          |
| Destination             | Any        |
| Destination Port Ranges | 80         |
| Protocol                | TCP        |
| Action                  | Allow      |
| Priority                | 1101       |
| Name                    | Allow-HTTP |

Click:

```text id="n6p3x8"
Add
```

#### Explanation

This NSG rule allows HTTP traffic from the internet to the VM.

---

# Step 5: Verify SSH Rule

Ensure another inbound rule exists:

| Port | Action |
| ---- | ------ |
| 22   | Allow  |

#### Explanation

SSH access is required for VM administration and troubleshooting.

---

# Step 6: Verify VNet Internet Connectivity

Search:

| Search       |
| ------------ |
| Route Tables |

If any Route Table was attached to the subnet and blocking internet access:

* Remove subnet association

OR

* Delete the Route Table

#### Explanation

The Route Table configuration blocked outbound internet access, preventing Nginx installation and package downloads.

---

# Step 7: Connect to VM Using SSH

Inside VM page:

Click:

```text id="p8x4m2"
Connect
```

Then:

```text id="m3q7v1"
Native SSH
```

Use the VM Public IP address:

Example:

```bash id="k5x1m9"
ssh azureuser@20.x.x.x
```

#### Explanation

SSH access allows remote administration of the Linux Virtual Machine.

---

# Step 8: Install and Configure Nginx

Inside the VM terminal, run:

```bash id="r2m8q4"
sudo apt update
```

Then:

```bash id="v7x3m1"
sudo apt install nginx -y
```

Enable Nginx:

```bash id="m1q5x8"
sudo systemctl enable nginx
```

Start Nginx:

```bash id="q9v2m6"
sudo systemctl start nginx
```

Verify service status:

```bash id="t4m8x2"
sudo systemctl status nginx
```

Expected Output:

```text id="p7q3m5"
active (running)
```

#### Explanation

Nginx is a lightweight and high-performance web server widely used in cloud infrastructure.

---

# Step 9: Verify Public Accessibility

Copy the Public IP address from:

```text id="x6m1q9"
Overview
```

Open browser:

```text id="v2p8m4"
http://PUBLIC-IP
```

Expected Result:

# ✅ Welcome to nginx!

#### Explanation

This confirms that:

* Public IP is attached
* NSG rules are configured correctly
* Nginx is installed and running
* VM is accessible from the internet

---

# Issues Faced During Troubleshooting

| Issue                             | Resolution                        |
| --------------------------------- | --------------------------------- |
| VM inaccessible despite Public IP | Verified NIC IP configuration     |
| Internet connectivity blocked     | Removed incorrect Route Table     |
| Port 80 inaccessible              | Added NSG inbound HTTP rule       |
| SSH connection issues             | Verified NSG SSH rules            |
| Nginx installation failed         | Restored outbound internet access |
| Azure Run Command restrictions    | Used direct SSH troubleshooting   |

---

# Final Validation Checklist

✅ Public IP attached successfully
✅ NSG configured with HTTP & SSH rules
✅ Route Table issue resolved
✅ VM accessible from internet
✅ Nginx installed successfully
✅ Nginx service running
✅ HTTP accessible on port 80

---

# Best Practices

* Use NSGs to control inbound traffic
* Validate Route Tables before troubleshooting connectivity
* Keep Public IPs attached only when necessary
* Verify outbound internet connectivity for package installations
* Monitor VM accessibility after networking changes

---

# Key Learnings

* Azure networking issues can impact application accessibility
* Route Tables can block outbound internet traffic
* NSGs are critical for VM security and connectivity
* Public IPs must be correctly attached to NICs
* Troubleshooting cloud networking requires step-by-step validation
* Nginx deployment depends on proper internet routing and firewall configuration
