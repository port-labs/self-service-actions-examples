## Workflows affecting Azure resources

This folder contains a series of workflows that can be triggered from Port to modify Azure resources.

### Pre-requisites

1. Port variables required to run the scripts are:
   - `PORT_CLIENT_ID`
   - `PORT_CLIENT_SECRET`

Find your port client id and secret from [this guide](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#find-your-port-credentials)

2. Azure Credentials

Before using this action, you need to create a GitHub Action secret named `AZURE_CREDENTIALS`. The secret should contain Azure credentials in JSON format, including the clientSecret, subscriptionId, tenantId, and clientId.

```yaml
AZURE_CREDENTIALS = {
    "clientSecret": "******",
    "subscriptionId": "******",
    "tenantId": "******",
    "clientId": "******"
}
```

### Port Actions

| Action                            | Workflow                  | Description                                                                              |
| --------------------------------- | ------------------------- | ---------------------------------------------------------------------------------------- |
| Tag Azure Resource CLI            | `tag-azure-cli.yml`       | This action utilizes the Azure CLI to tag an Azure resource based on the provided inputs |
| Tag Azure Resource with Terraform | `tag-azure-terraform.yml` | This action leverages Terraform to add tags to an Azure resource.                        |
