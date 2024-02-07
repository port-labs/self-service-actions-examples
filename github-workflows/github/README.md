## Workflows affecting GitHub resources

This folder contains a series of workflows that can be triggered from Port to modify GitHub resources.

### Pre-requisites

The list of variables required to run the scripts are:
- `PORT_CLIENT_ID`
- `PORT_CLIENT_SECRET`

Find your port client id and secret from [this guide](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#find-your-port-credentials)


### Port Actions

| Action | Workflow | Description |
|----------|----------|----------|
| `delete-repo-action.json` | `delete-repo.yml` | A github action that deletes a github repo  |
