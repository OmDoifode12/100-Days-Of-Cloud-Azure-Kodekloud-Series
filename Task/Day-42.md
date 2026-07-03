# Day 42 – Backup and Delete Azure Storage Blob Container

---

## Task Overview

As part of an Azure environment cleanup activity, the Nautilus DevOps Team needed to back up the contents of an existing Azure Blob Storage container before permanently deleting it. This ensured that important data was preserved while removing temporary cloud resources created during migration.

The following requirements were completed:

* Access the existing Storage Account `nautilusst30415`
* Verify the private Blob container `nautilus-blob-24114`
* Download all blob contents to the `/opt` directory on the Azure client host
* Verify that the backup completed successfully
* Delete the Blob container from the Storage Account
* Confirm that the container was successfully removed

---

# Architecture Overview

```text
Azure Storage Account
nautilusst30415
        │
        ▼
Private Blob Container
nautilus-blob-24114
        │
        ▼
Download Blob Files
        │
        ▼
Azure Client Host
/opt
        │
        ▼
Delete Blob Container
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

# Step 3: Retrieve Storage Account Access Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name nautilusst30415 \
  --query "[0].value" \
  -o tsv)

echo $ACCOUNT_KEY
```

#### Explanation

Retrieves the Storage Account access key required to access Blob Storage.

---

# Step 4: Verify the Blob Container

Run:

```bash
az storage container list \
  --account-name nautilusst30415 \
  --account-key "$ACCOUNT_KEY" \
  -o table
```

Expected Output:

```text
nautilus-blob-24114
```

#### Explanation

Confirms that the Blob container exists.

---

# Step 5: List Blob Files

Run:

```bash
az storage blob list \
  --account-name nautilusst30415 \
  --account-key "$ACCOUNT_KEY" \
  --container-name nautilus-blob-24114 \
  -o table
```

Expected Output:

```text
nautilus.txt
```

*(The filename may vary depending on the lab.)*

#### Explanation

Displays all blobs stored inside the container.

---

# Step 6: Download Blob Files to /opt

Run:

```bash
az storage blob download-batch \
  --destination /opt \
  --source nautilus-blob-24114 \
  --account-name nautilusst30415 \
  --account-key "$ACCOUNT_KEY"
```

#### Explanation

Downloads every blob from the container directly into the `/opt` directory on the Azure client host.

---

# Step 7: Verify Backup

Run:

```bash
ls -l /opt
```

#### Explanation

Verifies that the blob files were successfully downloaded.

---

# Step 8: Delete the Blob Container

Run:

```bash
az storage container delete \
  --account-name nautilusst30415 \
  --account-key "$ACCOUNT_KEY" \
  --name nautilus-blob-24114
```

Expected Output:

```json
{
  "deleted": true
}
```

#### Explanation

Deletes the Blob container after confirming the backup.

---

# Step 9: Verify Container Deletion

Run:

```bash
az storage container list \
  --account-name nautilusst30415 \
  --account-key "$ACCOUNT_KEY" \
  -o table
```

#### Explanation

Confirms that the Blob container no longer exists.

---

# Final Validation Checklist

✅ Storage Account verified

✅ Blob container verified

✅ Blob files downloaded successfully

✅ Backup stored in `/opt`

✅ Blob container deleted

✅ Container deletion verified

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|--------|------------|
| Backup validation initially failed | Downloaded blobs directly into `/opt` instead of a subdirectory |
| Authentication required | Retrieved the Storage Account access key before performing Blob operations |
| Needed to verify backup | Listed files in `/opt` before deleting the container |
| Cleanup validation | Verified the container no longer existed after deletion |

---

# Best Practices

* Always back up Blob data before deleting containers.
* Store backups in the exact location required by automation or validation scripts.
* Verify downloaded files before removing cloud resources.
* Confirm container deletion after cleanup.
* Use Azure CLI to automate backup and cleanup tasks.

---

# Key Learnings

* Managing Azure Blob Storage using Azure CLI
* Retrieving Storage Account access keys
* Listing Blob containers and blob files
* Downloading blobs using `az storage blob download-batch`
* Performing safe cloud resource cleanup
* Verifying backups before deletion
* Automating Azure Storage maintenance tasks
* Following validation requirements accurately
