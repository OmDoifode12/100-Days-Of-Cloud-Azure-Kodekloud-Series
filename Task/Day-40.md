# Day 40 – Managing Secrets with Azure Key Vault

---

## Task Overview

As part of improving cloud security, the Nautilus DevOps Team implemented Azure Key Vault to securely manage encryption keys and protect sensitive data. The objective was to create a Key Vault, generate a 4096-bit RSA key, encrypt a sensitive file using Azure Key Vault, decrypt it back, and verify the integrity of the decrypted data.

The following requirements were completed:

* Create an Azure Key Vault named `devops-1676`
* Configure the Key Vault in the `East US` region
* Use the Standard pricing tier
* Enable Soft Delete with a retention period of 7 days
* Use the Vault Access Policy permission model
* Configure access policies with Get, List, Encrypt, Decrypt, and Create permissions
* Create a 4096-bit RSA key named `devops-key`
* Base64 encode the sensitive file before encryption
* Encrypt the file using the RSA-OAEP algorithm
* Decrypt the encrypted file
* Base64 decode the decrypted output
* Verify that the decrypted file matches the original file

---

# Architecture Overview

```text
SensitiveData.txt
        │
        ▼
Base64 Encoding
        │
        ▼
Azure Key Vault
devops-1676
        │
        ▼
RSA 4096 Key
devops-key
        │
 ┌──────┴──────┐
 ▼             ▼
Encrypt     Decrypt
 │             │
 ▼             ▼
EncryptedData.bin
        │
        ▼
Base64 Decode
        │
        ▼
DecryptedData.txt
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

# Step 2: Retrieve the Resource Group

Run:

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default Resource Group created for the lab.

---

# Step 3: Create Azure Key Vault

Run:

```bash
az keyvault create \
  --name devops-1676 \
  --resource-group $RG_NAME \
  --location eastus \
  --sku standard \
  --retention-days 7 \
  --enable-rbac-authorization false
```

#### Explanation

Creates an Azure Key Vault using the Vault Access Policy permission model.

| Setting | Value |
|----------|-------|
| Key Vault | devops-1676 |
| Region | East US |
| SKU | Standard |
| Soft Delete | 7 Days |

---

# Step 4: Configure Access Policy

Run:

```bash
az keyvault set-policy \
  --name devops-1676 \
  --spn $(az account show --query user.name -o tsv) \
  --key-permissions get list encrypt decrypt create
```

#### Explanation

Grants the required cryptographic permissions to the current lab identity.

---

# Step 5: Create RSA Key

Run:

```bash
az keyvault key create \
  --vault-name devops-1676 \
  --name devops-key \
  --kty RSA \
  --size 4096
```

#### Explanation

Creates a 4096-bit RSA encryption key inside Azure Key Vault.

---

# Step 6: Base64 Encode the Sensitive File

Run:

```bash
base64 /root/SensitiveData.txt > /root/plaintext.b64
```

#### Explanation

Encodes the sensitive file before encryption as required by the lab.

---

# Step 7: Encrypt the File

Run:

```bash
PLAINTEXT=$(cat /root/plaintext.b64)

az keyvault key encrypt \
  --vault-name devops-1676 \
  --name devops-key \
  --algorithm RSA-OAEP \
  --value "$PLAINTEXT" \
  --query result \
  -o tsv > /root/EncryptedData.bin
```

#### Explanation

Encrypts the Base64-encoded content using the RSA-OAEP algorithm.

---

# Step 8: Decrypt the File

Run:

```bash
CIPHER=$(cat /root/EncryptedData.bin)

az keyvault key decrypt \
  --vault-name devops-1676 \
  --name devops-key \
  --algorithm RSA-OAEP \
  --value "$CIPHER" \
  --query result \
  -o tsv > /root/decrypted.b64
```

#### Explanation

Decrypts the encrypted content using the same RSA key.

---

# Step 9: Decode the Decrypted Output

Run:

```bash
base64 -d /root/decrypted.b64 > /root/DecryptedData.txt
```

#### Explanation

Restores the decrypted Base64 content back into the original file.

---

# Step 10: Verify Data Integrity

Run:

```bash
diff /root/SensitiveData.txt /root/DecryptedData.txt
```

Expected Output:

```text
(no output)
```

#### Explanation

No output indicates that both files are identical and encryption/decryption was successful.

---

# Step 11: Verify the RSA Key

Run:

```bash
az keyvault key show \
  --vault-name devops-1676 \
  --name devops-key
```

#### Explanation

Confirms that the RSA key exists and is correctly configured.

---

# Final Validation Checklist

✅ Azure Key Vault created successfully

✅ Standard pricing tier configured

✅ Soft Delete retention set to 7 days

✅ Vault Access Policy permission model used

✅ Required Key Vault permissions assigned

✅ RSA 4096-bit key created

✅ SensitiveData.txt Base64 encoded

✅ EncryptedData.bin generated successfully

✅ DecryptedData.txt restored successfully

✅ File integrity verified using `diff`

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| Azure AD commands returned permission errors | Used the Service Principal ID from `az account show` |
| Access denied while managing keys | Configured the required Key Vault access policy |
| Encryption commands required Base64 input | Encoded the file before encryption |
| Decryption validation | Compared original and decrypted files using `diff` |
| Azure CLI preview warnings | Continued as the commands executed successfully |

---

# Best Practices

* Store encryption keys inside Azure Key Vault instead of applications.
* Use least-privilege access policies.
* Enable Soft Delete to prevent accidental key deletion.
* Never expose encryption keys in application code.
* Always verify decrypted data after cryptographic operations.

---

# Key Learnings

* Creating and configuring Azure Key Vault
* Managing cryptographic keys securely
* Configuring Vault Access Policies
* Working with 4096-bit RSA keys
* Encrypting and decrypting data using Azure CLI
* Understanding RSA-OAEP encryption
* Verifying data integrity after decryption
* Applying cloud security best practices with Azure Key Vault
