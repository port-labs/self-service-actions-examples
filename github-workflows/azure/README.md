## Workflows affecting Azure resources

This folder contains a series of workflows that can be triggered from Port to modify Azure resources.

### Pre-requisites

1. Port variables required to run the scripts are:
   - `PORT_CLIENT_ID`
   - `PORT_CLIENT_SECRET`

Find your port client id and secret from [this guide](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#find-your-port-credentials)

2. Azure Credentials.

  - `AZURE_CLIENT_ID`: specifies the login client id. It could be the client id of a service principal or a user-assigned managed identity.
  - `AZURE_CLIENT_SECRET`: specifies the login client secret. It could be the client id of a service principal or a user-assigned managed identity.
  - `AZURE_TENANT_ID`: specifies the login tenant id.
  - `AZURE_SUBSCRIPTION_ID`:specifies the login subscription id.

To use an azure backend to store your terrraform state, add the following credentials:
  - `AZURE_RESOURCE_GROUP`
  - `TF_STORAGE_ACCOUNT`

### Port Actions

| Action                            | Workflow                  | Description                                                                              |
| --------------------------------- | ------------------------- | ---------------------------------------------------------------------------------------- |
| Tag Azure Resource CLI            | `tag-azure-cli.yml`       | This action utilizes the Azure CLI to tag an Azure resource based on the provided inputs |
| Tag Azure Resource with Terraform | `tag-azure-terraform.yml` | This action leverages Terraform to add tags to an Azure resource.                        |
