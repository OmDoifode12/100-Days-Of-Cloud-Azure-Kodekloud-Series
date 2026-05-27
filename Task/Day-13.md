# Day 13 – SSH into an Azure Virtual Machine

---

## Task Overview

The Nautilus DevOps team is working on setting up secure SSH access for their Virtual Machines in Microsoft Azure. One of the requirements is to configure passwordless SSH authentication by adding the root user's SSH public key from the Azure client host to the Azure Virtual Machine.

For this task:

* An existing Virtual Machine named `devops-vm` already exists
* The VM is running in the `centralus` region
* The default SSH user is `azureuser`
* The root user's public key on the Azure client host is located at:

```bash id="6j4e5q"
/root/.ssh/id_rsa.pub
```

Your objective is to:

* Copy the root user's public key to the VM
* Add the key to the root user's `authorized_keys` file
* Configure proper SSH permissions
* Verify passwordless SSH access as the root user

---

# Step-by-Step Implementation

### Step 1: Verify Virtual Machine Status

Run:

```bash id="d8x1kv"
az vm list -d --output table
```

#### Explanation

This command displays the current status of Azure Virtual Machines.

Verify:

| VM Name   | Status     |
| --------- | ---------- |
| devops-vm | VM running |

---

### Step 2: Get VM Public IP Address

Run:

```bash id="b2s7up"
az vm list-ip-addresses \
  --name devops-vm \
  --resource-group kml_rg_main-145620288f634136 \
  --output table
```

#### Explanation

This command retrieves the public IP address of the Virtual Machine.

Example Output:

```text id="z8m3re"
20.9.62.213
```

---

### Step 3: View Root User Public Key

On the Azure client host, run:

```bash id="r1x5df"
cat /root/.ssh/id_rsa.pub
```

#### Explanation

This command displays the root user's SSH public key that will be copied to the VM.

Example Output:

```text id="n5u3cv"
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...
```

Copy the entire key.

---

### Step 4: SSH into the VM

Run:

```bash id="w9k2my"
ssh azureuser@<VM_PUBLIC_IP>
```

Example:

```bash id="p7j6oe"
ssh azureuser@20.9.62.213
```

#### Explanation

This connects to the Azure Virtual Machine using the default Azure user.

---

### Step 5: Switch to Root User

Inside the VM, run:

```bash id="t3q4lx"
sudo su -
```

Verify:

```bash id="h8v1nb"
whoami
```

Expected Output:

```text id="u2r7mz"
root
```

#### Explanation

The task requires adding the public key to the root user's authorized_keys file.

---

### Step 6: Create SSH Directory

Run:

```bash id="m5x9jd"
mkdir -p /root/.ssh
chmod 700 /root/.ssh
```

#### Explanation

Creates the SSH directory and assigns secure permissions.

---

### Step 7: Add Public Key to authorized_keys

Open the file:

```bash id="g4p7kr"
vi /root/.ssh/authorized_keys
```

Paste the copied public key and save the file.

Alternative:

```bash id="f1w3ys"
echo "PASTE_PUBLIC_KEY_HERE" >> /root/.ssh/authorized_keys
```

#### Explanation

The authorized_keys file stores all SSH public keys allowed to authenticate as the root user.

---

### Step 8: Configure File Permissions

Run:

```bash id="n8c5vp"
chmod 600 /root/.ssh/authorized_keys
```

Verify:

```bash id="d7b9ql"
ls -ld /root/.ssh
ls -l /root/.ssh/authorized_keys
```

Expected Output:

```text id="x4m8zt"
drwx------ root root
-rw------- root root
```

#### Explanation

SSH requires strict file permissions to allow key-based authentication.

---

### Step 9: Verify SSH Configuration

Check:

```bash id="k9e2au"
cat /etc/ssh/sshd_config | grep PermitRootLogin
```

Expected:

```text id="y6r1wp"
PermitRootLogin yes
```

Check:

```bash id="a3v8hn"
cat /etc/ssh/sshd_config | grep PubkeyAuthentication
```

Expected:

```text id="q1f7me"
PubkeyAuthentication yes
```

If changes are made:

```bash id="j2k6bc"
systemctl restart ssh
```

or

```bash id="r5x3tv"
systemctl restart sshd
```

#### Explanation

These settings enable root login using SSH keys.

---

### Step 10: Exit the VM

Run:

```bash id="s7v4yb"
exit
exit
```

#### Explanation

Returns to the Azure client host.

---

### Step 11: Verify Passwordless SSH Access

Run:

```bash id="e4p1km"
ssh root@<VM_PUBLIC_IP>
```

Example:

```bash id="t6n8qw"
ssh root@20.9.62.213
```

Expected Output:

```text id="z9m2hx"
root@devops-vm:~#
```

#### Explanation

This confirms successful passwordless SSH authentication using the root user's SSH key.

---

# Best Practices

* Use SSH key authentication instead of passwords
* Restrict permissions on SSH files and directories
* Regularly rotate SSH keys
* Avoid exposing SSH access publicly whenever possible
* Use Bastion Hosts for secure administrative access

---

# Key Learnings

* SSH keys provide secure passwordless authentication
* authorized_keys controls SSH access for users
* Proper file permissions are required for SSH authentication
* Azure Virtual Machines support secure remote administration
* SSH key management is a critical DevOps and cloud security skill
