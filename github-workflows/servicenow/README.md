<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Trigger ServiceNow Incident

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly trigger incidents in ServiceNow via Port Actions with ease.

## Prerequisites
* ServiceNow instance URL, username and password. Head over to [ServiceNow](https://signon.service-now.com/x_snc_sso_auth.do?pageId=username) to get your credentials.
* [Port's GitHub app](https://github.com/apps/getport-io) installed

## Quickstart - Trigger ServiceNow incident from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `SERVICENOW_USERNAME` - ServiceNow instance username
* `SERVICENOW_PASSWORD` - ServiceNow instance password
* `SERVICENOW_INSTANCE_URL` - ServiceNow instance URL
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Optional - Install Port's ServiceNow integration [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/itsm/servicenow)
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
4. After you installed the integration, the blueprints `servicenowGroup`, `servicenowCatalog` and `servicenowIncident` will appear, create the following action with the following JSON file on the `servicenowIncident` blueprint:

```json
[
  {
    "identifier": "trigger_servicenow_incident",
    "title": "Trigger ServiceNow incident",
    "icon": "Servicenow",
    "userInputs": {
      "properties": {
        "short_description": {
          "icon": "DefaultProperty",
          "title": "Short Description",
          "description": "Description of the event",
          "type": "string"
        },
        "sysparm_input_display_value": {
          "title": "Sysparm Input Display Value",
          "description": "Flag that indicates whether to set field values using the display value or the actual value.",
          "type": "boolean",
          "default": false
        },
        "urgency": {
          "title": "Urgency",
          "icon": "DefaultProperty",
          "type": "string",
          "default": "2",
          "enum": [
            "1",
            "2",
            "3"
          ],
          "enumColors": {
            "1": "lightGray",
            "2": "lightGray",
            "3": "lightGray"
          }
        },
        "assigned_to": {
          "icon": "DefaultProperty",
          "title": "Assigned To",
          "description": "User this incident is assigned to",
          "type": "string"
        },
        "sysparm_display_value": {
          "title": "Sysparm Display Value",
          "description": "Determines the type of data returned, either the actual values from the database or the display values of the fields.",
          "icon": "DefaultProperty",
          "type": "string",
          "default": "all",
          "enum": [
            "true",
            "false",
            "all"
          ],
          "enumColors": {
            "true": "lightGray",
            "false": "lightGray",
            "all": "lightGray"
          }
        }
      },
      "required": [
        "assigned_to",
        "short_description"
      ],
      "order": [
        "short_description",
        "assigned_to",
        "urgency",
        "sysparm_display_value",
        "sysparm_input_display_value"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "<Enter GitHub organization>",
      "repo": "<Enter GitHub repository>",
      "workflow": "trigger-servicenow-incident.yml",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "CREATE",
    "description": "Triggers an incident in ServiceNow",
    "requiredApproval": false
  }
]
```
>**Note** Replace the invocation method with your own repository details.

5. Create a workflow file under .github/workflows/trigger-servicenow-incident.yml with the content in [trigger-servicenow-incident.yml](./trigger-servicenow-incident.yml)

6. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

7. Done! wait for the incident to be trigger in ServiceNow

Congrats 🎉 You've triggered your first incident in ServiceNow from Port!
