# Day 37 – Setting Up MySQL on a Virtual Machine in Azure

---

## Task Overview

As part of a cloud application integration project, the Nautilus DevOps Team was tasked with deploying a MySQL server on Azure and connecting it to an existing PHP application hosted on a separate Azure Virtual Machine. The objective was to establish secure communication between the application and database layers and validate the connection through a web-based PHP application.

The following requirements were completed:

* Create a MySQL VM named `xfusion-mysql-vm`
* Deploy the VM using the Percona Server for MySQL image
* Configure the VM in the `Central US` region
* Enable password-based authentication
* Create a MySQL database named `xfusion_db`
* Create a MySQL user named `xfusion_user`
* Grant remote access privileges on the database
* Open MySQL port `3306` in the Network Security Group
* Update the PHP application connection settings
* Validate connectivity through `db_test.php`

---

# Architecture Overview

```text
Azure PHP VM
xfusion-php-vm
      │
      │ Port 3306
      ▼
Azure MySQL VM
xfusion-mysql-vm
      │
      ▼
Percona MySQL Server
      │
      ▼
Database: xfusion_db
      │
      ▼
User: xfusion_user
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

Retrieves the default resource group created for the lab.

---

# Step 3: Create MySQL Virtual Machine

Run:

```bash
az vm create \
  --resource-group $RG_NAME \
  --name xfusion-mysql-vm \
  --image jetware-srl:percona_mysql:percona_mysql57-ubuntu-1604:latest \
  --location centralus \
  --size Standard_B1s \
  --admin-username xfusion_admin \
  --admin-password 'Namin@123456' \
  --authentication-type password \
  --storage-sku Standard_LRS
```

#### Explanation

Creates the MySQL VM using the Percona Marketplace image.

---

# Step 4: Open Required Ports

Run:

```bash
az vm open-port \
  --resource-group $RG_NAME \
  --name xfusion-mysql-vm \
  --port 22
```

```bash
az vm open-port \
  --resource-group $RG_NAME \
  --name xfusion-mysql-vm \
  --port 3306
```

#### Explanation

Allows SSH and MySQL traffic to reach the VM.

---

# Step 5: Connect to MySQL VM

Run:

```bash
ssh xfusion_admin@<MYSQL_PUBLIC_IP>
```

Access MySQL:

```bash
sudo /jet/enter mysql
```

#### Explanation

Connects to the Percona MySQL instance.

---

# Step 6: Configure Database

Run:

```sql
CREATE DATABASE xfusion_db;

CREATE USER 'xfusion_user'@'%' IDENTIFIED BY 'password123';

GRANT ALL PRIVILEGES ON xfusion_db.* TO 'xfusion_user'@'%';

FLUSH PRIVILEGES;
```

#### Explanation

Creates the database, user, and grants remote access privileges.

---

# Step 7: Get Public IPs

Run:

```bash
az vm list -d -o table
```

Example:

```text
xfusion-php-vm    20.85.240.178
xfusion-mysql-vm  20.29.96.210
```

---

# Step 8: Connect to PHP VM

Run:

```bash
ssh azureuser@20.85.240.178
```

#### Explanation

Connects to the PHP application server.

---

# Step 9: Update Database Connection

Edit:

```bash
sudo vi /var/www/html/db_test.php
```

Update values:

```php
<?php

$servername = "20.29.96.210";
$username = "xfusion_user";
$password = "password123";
$dbname = "xfusion_db";
$port = 3306;

$conn = new mysqli(
    $servername,
    $username,
    $password,
    $dbname,
    $port
);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

echo "Connected successfully";

$conn->close();

?>
```

#### Explanation

Updates the PHP application to use the new MySQL database.

---

# Step 10: Validate PHP Syntax

Run:

```bash
php -l /var/www/html/db_test.php
```

Expected Output:

```text
No syntax errors detected
```

---

# Step 11: Verify Database Connectivity

Run:

```bash
nc -zv 20.29.96.210 3306
```

Expected Output:

```text
Connection to 20.29.96.210 3306 port [tcp/mysql] succeeded!
```

#### Explanation

Confirms network connectivity between the PHP VM and MySQL VM.

---

# Step 12: Final Validation

Open in browser:

```text
http://20.85.240.178/db_test.php
```

Expected Output:

```text
Connected successfully
```

---

# Final Validation Checklist

✅ MySQL VM deployed successfully

✅ MySQL database created

✅ Remote database user configured

✅ Port 3306 opened

✅ PHP application updated

✅ Database connectivity verified

✅ PHP syntax validated

✅ Web application connected successfully

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| Marketplace image publisher mismatch | Verified correct publisher and offer using Azure CLI |
| Marketplace permission errors | Used available image already provisioned by the lab |
| Database already existed | Reused existing database |
| User creation failed because user already existed | Verified existing user and granted privileges |
| PHP page displayed source code instead of executing | Restored missing PHP tags (`<?php ?>`) |
| MySQL connectivity troubleshooting | Verified NSG rules and tested port 3306 using nc |

---

# Best Practices

* Separate application and database servers
* Restrict database access using NSGs
* Use dedicated application users instead of root
* Verify connectivity before application testing
* Validate application configuration after deployment

---

# Key Learnings

* Deploying database workloads on Azure Virtual Machines
* Configuring Percona MySQL servers
* Managing MySQL users and privileges
* Opening and validating NSG rules
* Connecting PHP applications to remote databases
* Troubleshooting real-world application connectivity issues
* Validating cloud infrastructure end-to-end
