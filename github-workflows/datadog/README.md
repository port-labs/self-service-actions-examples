<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Trigger Datadog Incident

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly trigger incidents in Datadog via Port Actions with ease.

## Prerequisites
* Datadog API Key. Head over to [your account's API and application keys](https://app.datadoghq.com/account/settings?_gl=1*47zn4y*_gcl_au*MTg2NDEzODgwMC4xNzA1NjQyMDUz*_ga*MTk2MjcxMTc2OC4xNzA1NjQyMDU0*_ga_KN80RDFSQK*MTcwNjAwNzI0MS40LjEuMTcwNjAwNzI0Mi4wLjAuMA..*_fplc*dHFWa3VWZ1YwQVM3QWIlMkZrbmFSWE5OdVFnMWJVRzFFeVhkRnJCVTN6JTJGNEx3Nkc5bmsyVW1VY2locW96ZzB4ekVlcWIyJTJGZnlxc3lpYWlLSzJjYzdpODdTTVZBRzkyUnh2c1NKUVhWR3VoZERnN1R5dVJabjRmSDVMeWIzeklnJTNEJTNE#api) page to create a new API key. The API key should have the `incident_write` permission scope.
* Datadog Application Key. It is available on the same page as the Datadog API key.
* [Port's GitHub app](https://github.com/apps/getport-io) installed

## Quickstart - Trigger Datadog incident from the service catalog

1. Create the following GitHub action secrets
* `DD_API_URL` - Datadog API URL by default should be [https://api.datadoghq.com](https://api.datadoghq.com). However, if you are on the Datadog EU site, set the secret to `https://api.datadoghq.eu`. If you have your region information you use `https://api.<region>.datadoghq.com` or `https://api.<region>.datadoghq.eu`.
* `DD_API_KEY` - Datadog API Key
* `DD_APPLICATION_KEY` - Datadog Application Key
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Create a Datadog incident blueprint in Port using the provided [datadogIncident.json](./datadogIncident.json) file
>**Note** This step will ensure the `datadogIncident` blueprint is available, and also update the catalog in real time with the new incident created.
4. After creating the blueprint, create the following action with the following JSON file on the `datadogIncident` blueprint:

```json
[
  {
    "identifier": "trigger_datadog_incident",
    "title": "Trigger Datadog Incident",
    "icon": "Datadog",
    "userInputs": {
      "properties": {
        "customerImpactScope": {
          "title": "Customer Impact Scope",
          "description": "A summary of the impact customers experienced during the incident.",
          "type": "string"
        },
        "customerImpacted": {
          "title": "Customer Impacted",
          "description": "A flag indicating whether the incident caused customer impact.",
          "type": "boolean"
        },
        "title": {
          "title": "Title",
          "description": "The title of the incident, which summarizes what happened.",
          "type": "string"
        },
        "notificationHandleName": {
          "title": "Notification Handle Name",
          "type": "string",
          "description": "The name of the notified handle."
        },
        "notificationHandleEmail": {
          "title": "Notification Handle Email",
          "description": "The email address used for the notification.",
          "type": "string",
          "pattern": "^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,4}$"
        }
      },
      "required": [
        "customerImpacted",
        "title"
      ],
      "order": [
        "title",
        "customerImpacted",
        "customerImpactScope",
        "notificationHandleName",
        "notificationHandleEmail"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "<Enter GitHub organization>",
      "repo": "<Enter GitHub repository>",
      "workflow": "trigger-datadog-incident.yml",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "CREATE",
    "description": "Triggers Datadog incident",
    "requiredApproval": false
  }
]
```
>**Note** Replace the invocation method with your own repository details.

5. Create a workflow file under .github/workflows/trigger-datadog-incident.yml with the content in [trigger-datadog-incident.yml](./trigger-datadog-incident.yml)

6. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

7. Done! wait for the incident to be trigger in Datadog

Congrats 🎉 You've triggered your first incident in Datadog from Port!
