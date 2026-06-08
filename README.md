# Seeq Application Key Rotation with Azure Key Vault

This repository demonstrates how to automate the rotation of Seeq Application access keys and securely store them in Azure Key Vault.

## Overview

This example shows how to:

1. Authenticate to a Seeq server
2. Retrieve an existing Seeq Application by ID
3. Generate a new access key for the application (rotating the credential)
4. Store both the access key ID and password securely in Azure Key Vault
5. Optionally clean up old/expired keys

## Use Cases

- **Automated Key Rotation**: Schedule this notebook to run periodically (e.g., every 90 days) to rotate application credentials before they expire
- **Secret Management**: Centralize credential storage in Azure Key Vault for secure access by other applications and services
- **Compliance**: Meet security requirements for regular credential rotation
- **Multi-Application Management**: Extend the pattern to manage multiple Seeq applications from a single automation

## Prerequisites

### Software Requirements

- Python 3.8 or higher
- Jupyter Notebook or JupyterLab
- Azure CLI installed and configured

### Azure Requirements

1. **Azure Key Vault**: You need access to an Azure Key Vault where secrets will be stored
2. **Azure Authentication**: You must be logged in via Azure CLI with permissions to:
   - Read/Write secrets to the target Key Vault
   - Example role: `Key Vault Secrets Officer` or `Key Vault Administrator`

### Seeq Requirements

1. **Seeq Server**: Access to a Seeq server (R60.0 or later for Application API support)
2. **Seeq Credentials**: Username and password with permissions to:
   - View and manage Applications
   - Generate access keys for Applications
3. **Seeq Application**: The ID of the Seeq Application you want to rotate keys for

## Installation

1. Clone or download this repository to your local machine

2. Install the required Python packages:

```bash
pip install -r requirements.txt
```

3. Ensure you're logged into Azure CLI:

```bash
az login
```

4. Verify access to your Key Vault:

```bash
az keyvault secret list --vault-name <your-keyvault-name>
```

## Usage

### Option 1: Interactive Jupyter Notebook

1. Start Jupyter:

```bash
jupyter notebook
```

2. Open `seeq_app_key_rotation.ipynb`

3. Update the configuration variables in the first cell:
   - `SEEQ_SERVER_URL`: Your Seeq server URL
   - `APPLICATION_ID`: The Seeq Application ID to rotate keys for
   - `KEYVAULT_NAME`: Your Azure Key Vault name
   - `SECRET_NAME_PREFIX`: Prefix for secrets stored in Key Vault

4. Run all cells in order

### Option 2: Automated Execution

You can run this notebook as part of an automated pipeline:

```bash
# Convert notebook to Python script
jupyter nbconvert --to script seeq_app_key_rotation.ipynb

# Run the script
python seeq_app_key_rotation.py
```

For production use, consider:
- Using Azure Automation Runbooks
- Azure Functions with timer triggers
- GitHub Actions with scheduled workflows
- Jenkins or other CI/CD platforms

## Configuration

### Key Expiration

The example sets a 90-day expiration for new keys:

```python
expiry_date = datetime.utcnow() + timedelta(days=90)
```

Adjust this value based on your security policies.

### Key Vault Secret Naming

Secrets are stored in Key Vault with the following naming convention:

- Access Key ID: `{prefix}-{application_id}-key-id`
- Access Key Password: `{prefix}-{application_id}-key-password`

Example:
- `seeq-app-ABC123-key-id`
- `seeq-app-ABC123-key-password`

### Old Key Cleanup

The notebook includes an optional section to archive old/expired access keys. Review and uncomment this section if you want automatic cleanup.

## Security Considerations

1. **Never commit credentials**: Do not hardcode usernames, passwords, or secrets in the notebook
2. **Use environment variables**: For production use, store sensitive configuration in environment variables or Azure Key Vault
3. **Limit access**: Ensure only authorized users/services can read the Key Vault secrets
4. **Audit logging**: Enable Azure Key Vault audit logs to track secret access
5. **Key rotation schedule**: Rotate keys before they expire to prevent service disruptions
6. **Test in non-production**: Always test key rotation in a development/test environment first

## Troubleshooting

### Azure CLI Not Authenticated

**Error**: `ERROR: No subscription found. Run 'az account set' to select a subscription.`

**Solution**: Run `az login` and ensure you have access to the subscription containing your Key Vault

### Insufficient Key Vault Permissions

**Error**: `The user, group or application ... does not have secrets set permission on key vault ...`

**Solution**: Your Azure account needs `Key Vault Secrets Officer` role or equivalent permissions

### Seeq Application Not Found

**Error**: `404 Not Found` when retrieving application

**Solution**: 
- Verify the `APPLICATION_ID` is correct
- Ensure your Seeq user has permission to view the application
- Check that the application hasn't been archived

### Key Generation Fails

**Error**: `403 Forbidden` when generating access key

**Solution**: Your Seeq user needs `Manage` permission on the application to generate new access keys

## Example Output

When successful, you'll see output similar to:

```
Authenticated to Seeq server: https://your-seeq-server.com
Retrieved application: My Application Name
Generated new access key with ID: 0123456789ABCDEF
Stored access key ID in Azure Key Vault as: seeq-app-ABC123-key-id
Stored access key password in Azure Key Vault as: seeq-app-ABC123-key-password
Key rotation completed successfully!
```

## Additional Resources

- [Seeq Applications Documentation](https://support.seeq.com/kb/latest/cloud/applications)
- [Azure Key Vault Documentation](https://docs.microsoft.com/azure/key-vault/)
- [Azure SDK for Python](https://docs.microsoft.com/python/api/overview/azure/keyvault-secrets-readme)
- [Seeq Python SDK Documentation](https://python-docs.seeq.com/)

## License

This example is provided as-is for demonstration purposes.

## Contributing

This is a reference implementation. Feel free to adapt it to your specific needs and environment.
