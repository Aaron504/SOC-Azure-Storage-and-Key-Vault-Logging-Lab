# SOC Azure Storage and Key Vault Logging Lab
<p align="center">

  ![Screenshot 2024-10-12 133055](https://github.com/user-attachments/assets/2fc48d81-edb1-4846-95b1-b8ee33c9a063)

</p>

In this lab, I configured diagnostic logging for both Azure Storage and Azure Key Vault. For Azure Storage, I enabled logging for blob operations, capturing all logs and metrics, including authorization failures. For Key Vault, I set up logging to monitor key operations, including the creation and access of secrets. I generated logs by interacting with both the storage account and Key Vault, then ran KQL queries to observe blob storage activities (e.g., reading, deleting, and copying blobs) and Key Vault operations (e.g., listing and accessing secrets). This lab demonstrates my ability to monitor and audit sensitive data interactions in cloud environments.




<h2>Environments and Technologies Used</h2>

- Log Analytics Workspaces
- Azure Key Vault
- KQL (Kusto Query Language)
  

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1 - Configure Logging for Azure Storage
- Step 2 - Configure Logging for Key Vault
- Step 3 - Storage Account Test Logs
- Step 4 - Key Vault Test Logs

<h2>Deployment and Configuration Steps</h2>

- Configure Logging for Azure Storage
- Configure logging for your storage account by enabling diagnostic settings for blob storage
- Collect All Logs and Metrics (metrics will record auth failures) and send to your Log Analytics Workspace instance

![Screenshot 2024-10-12 133707](https://github.com/user-attachments/assets/d5f6107c-11e9-45b6-9c79-92a91ff788fe)

- Configure Logging for Key Vault
- Create a Key Vault Instance
- Configure logging for your Key Vault instance by enabling diagnostic settings
- Collect the audit log and send to you Log Analytics Workspace instance
- Add a secret to Key Vault called “Tenant-Global-Admin-Password” with a made up password
- Observe the key in key vault
- Generate some Logs for Storage Account and Key Vault
- Generate some Logs for Azure Storage (read some blobs/files)
- Observe the Logs (they may take a moment to appear)

![Screenshot 2024-10-12 134315](https://github.com/user-attachments/assets/e00acc6d-3928-4a5a-b83a-8bfd5dc252c2)
![Screenshot 2024-10-12 134910](https://github.com/user-attachments/assets/e5c3896c-a67f-4cd7-974c-0c828388c51f)
![Screenshot 2024-10-12 135159](https://github.com/user-attachments/assets/99e0f177-5bad-4a18-8304-e0b41eb7a03e)
![Screenshot 2024-10-12 135603](https://github.com/user-attachments/assets/9efab5e6-47cf-43f1-8ca3-1e1185c314b4)
![Screenshot 2024-10-12 140425](https://github.com/user-attachments/assets/55790dac-6e87-4872-9c1d-81dbdedd330f)


<h2>Storage Account Test Logs</h2>

// Authorization Error
StorageBlobLogs 
| where MetricResponseType endswith "Error" 
| where StatusText == "AuthorizationPermissionMismatch"
| order by TimeGenerated asc
![Screenshot 2024-10-12 140818](https://github.com/user-attachments/assets/ea19ee92-fe24-44e4-9006-422e2467198e)

//Deleting a bunch of blobs (in a short time period)
StorageBlobLogs | where OperationName == "DeleteBlob"
| where TimeGenerated > ago(24h)
![Screenshot 2024-10-12 141352](https://github.com/user-attachments/assets/e12c5225-0b89-41d2-a368-0f2b873925e5)

<h2Key Vault Test Logs</h2>

- // List out Secrets
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| where OperationName == "SecretList"

![Screenshot 2024-10-12 142006](https://github.com/user-attachments/assets/3dd41498-5a8b-4200-892e-2476f943c22a)

- // Attempt to view passwords that don't exist
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| where OperationName == "SecretGet"
| where ResultSignature == "Not Found"

![Screenshot 2024-10-12 142430](https://github.com/user-attachments/assets/995ce1d3-bc50-4488-903b-3e66e11d3f01)

- // Viewing a specific existing password
let CRITICAL_PASSWORD_NAME = "Tenant-Global-Admin-Password";
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| where OperationName == "SecretGet"
| where id_s contains CRITICAL_PASSWORD_NAME

![Screenshot 2024-10-12 142730](https://github.com/user-attachments/assets/4015db76-d06d-42eb-851b-172196cd2e1b)












