<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Trigger Opsgenie Incident

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly trigger incidents in Opsgenie via Port Actions with ease.

## Prerequisites
* Opsgenie API Key. Head over to [API Key Management tab](https://getport-test.app.opsgenie.com/settings/api-key-management) on your Opsgenie account to create a new API key. The API key should have the `Create and Update` permission.
* [Port's GitHub app](https://github.com/apps/getport-io) installed

## Quickstart - Trigger Opsgenie incident from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `OPSGENIE_API_KEY` - Opsgenie API Key
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Optional - Install Port's Opsgenie integration [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/incident-management/opsgenie)
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
4. After you installed the integration, the blueprints `opsGenieService`, `opsGenieIncident` and `opsGenieAlert` will appear, create the following action with the following JSON file on the `opsGenieIncident` blueprint:

```json
[
  {
    "identifier": "trigger_opsgenie_incident",
    "title": "Trigger Opsgenie incident",
    "icon": "OpsGenie",
    "userInputs": {
      "properties": {
        "message": {
          "title": "Message",
          "description": "Message of the incident",
          "type": "string",
          "maxLength": 130
        },
        "description": {
          "title": "Description",
          "description": "escription field of the incident that is generally used to provide a detailed information about the incident.",
          "type": "string",
          "maxLength": 15000
        },
        "responders": {
          "items": {
            "type": "object"
          },
          "title": "Responders",
          "description": "Teams/users that the incident is routed to via notifications. type field is mandatory for each item, where possible values are team, user.",
          "type": "array"
        },
        "tags": {
          "items": {
            "type": "string",
            "maxLength": 50
          },
          "title": "Tags",
          "description": "Tags of the incident.",
          "type": "array"
        },
        "details": {
          "title": "Details",
          "description": "Map of key-value pairs to use as custom properties of the incident.",
          "type": "object"
        },
        "priority": {
          "title": "Priority",
          "description": "Priority level of the incident. Possible values are P1, P2, P3, P4 and P5. Default value is P3.",
          "type": "string",
          "default": "P3",
          "enum": [
            "P1",
            "P2",
            "P3",
            "P4",
            "P5"
          ],
          "enumColors": {
            "P1": "lightGray",
            "P2": "lightGray",
            "P3": "lightGray",
            "P4": "lightGray",
            "P5": "lightGray"
          }
        },
        "note": {
          "icon": "DefaultProperty",
          "title": "Note",
          "description": "Additional note that is added while creating the incident.",
          "type": "string",
          "maxLength": 25000
        },
        "impactedServices": {
          "title": "Impacted Services",
          "description": "Services on which incident will be created.",
          "icon": "OpsGenie",
          "type": "array",
          "items": {
            "type": "string",
            "format": "entity",
            "blueprint": "opsGenieService"
          }
        },
        "notifyStakeholders": {
          "title": "Notify Stakeholders",
          "description": "Indicate whether stakeholders are notified or not. Default value is false.",
          "type": "boolean",
          "default": false
        }
      },
      "required": [
        "message"
      ],
      "order": [
        "message",
        "description",
        "responders",
        "tags",
        "details",
        "priority",
        "note",
        "impactedServices",
        "notifyStakeholders"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "repo": "<Enter GitHub repository>",
      "org": "<Enter GitHub organization>",
      "workflow": "trigger-opsgenie-incident.yml",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "CREATE",
    "description": "Triggers Opsgenie incident",
    "requiredApproval": false
  }
]
```
>**Note** Replace the invocation method with your own repository details.

5. Create a workflow file under .github/workflows/trigger-opsgenie-incident.yml with the content in [trigger-opsgenie-incident.yml](./trigger-opsgenie-incident.yml)

6. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

7. Done! wait for the incident to be trigger in Opsgenie

Congrats 🎉 You've triggered your first incident in Opsgenie from Port!
