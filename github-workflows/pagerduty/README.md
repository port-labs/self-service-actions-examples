<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Create Pager Duty Incident Action

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly create incidents in PagerDuty via Port Actions with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| token                | The Pager Duty Token to use to authenticate with the API with permissions to create incidents      | true     | -                  |
| portClientId         | The Port Client ID to use to authenticate with the API                                           | true     | -                  |
| portClientSecret     | The Port Client Secret to use to authenticate with the API                                       | true     | -                  |
| blueprintIdentifier | The blueprint identifier to use to populate the Port relevant entity                              | false    | pagerdutyIncident |
| incidentTitle        | The title of the incident to create                                                                | true     | -                  |
| extraDetails         | Extra details about the incident to create                                                        | false    | -                  |
| service              | The service id to correlate the incident to                                                       | true     | -                  |
| urgency              | The urgency of the incident to create                                                              | false    | high               |
| actorEmail           | The email of the actor to create the incident with                                                | true     | -                  |
| portRunId            | Port run ID to came from triggering the action                                                    | true     | -                  |


## Quickstart - Create a pager duty incident from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `PAGER_TOKEN` - a token with permission to create incidents [learn more](https://support.pagerduty.com/docs/generating-api-keys#section-generating-a-personal-rest-api-key)
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token) 

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Install Port's pager duty integration [learn more][https://github.com/port-labs/Port-Ocean/tree/main/integrations/pagerduty] 
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
4. After you installed the integration, the blueprints `pagerdutyService` and `pagerdutyIncident` will appear, create the following action with the following JSON file on the `pagerdutyService` blueprint:

```json
[
  {
    "identifier": "trigger_incident",
    "title": "Trigger Incident",
    "icon": "pagerduty",
    "userInputs": {
      "properties": {
        "title": {
          "icon": "DefaultProperty",
          "title": "Title",
          "type": "string"
        },
        "extra_details": {
          "title": "Extra Details",
          "type": "string"
        },
        "urgency": {
          "icon": "DefaultProperty",
          "title": "Urgency",
          "type": "string",
          "default": "high",
          "enum": [
            "high",
            "low"
          ],
          "enumColors": {
            "high": "yellow",
            "low": "green"
          }
        },
        "from": {
          "title": "From",
          "icon": "TwoUsers",
          "type": "string",
          "format": "user",
          "default": {
            "jqQuery": ".user.email"
          }
        }
      },
      "required": [
        "title",
        "urgency",
        "from"
      ],
      "order": [
        "title",
        "urgency",
        "from",
        "extra_details"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "omitPayload": false,
      "omitUserInputs": true,
      "reportWorkflowStatus": true,
      "org": "port-pagerduty-example",
      "repo": "test",
      "workflow": "create-an-incident.yaml"
    },
    "trigger": "DAY-2",
    "description": "Notify users and teams about incidents in the service",
    "requiredApproval": false
  }
]
```
>**Note** Replace the invocation method with your own repository details.

5. Create a workflow file under .github/workflows/create-an-incident.yaml with the following content:
```yml
on:
  workflow_dispatch:
    inputs:
      port_payload:
        required: true
        description: "Port's payload, including details for who triggered the action and general context (blueprint, run id, etc...)"
        type: string
    secrets: 
      PAGER_TOKEN: 
        required: true
      PORT_CLIENT_ID:
        required: true
      PORT_CLIENT_SECRET:
        required: true
jobs: 
  trigger:
    runs-on: ubuntu-latest
    steps:
      - uses: port-labs/pagerduty-incident-gha@v1
        with:
          portClientId: ${{ secrets.PORT_CLIENT_ID }}
          portClientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          token: ${{ secrets.PAGER_TOKEN }}
          portRunId: ${{ fromJson(inputs.port_payload).context.runId }}
          incidentTitle: ${{ fromJson(inputs.port_payload).payload.properties.title }}
          extraDetails: ${{ fromJson(inputs.port_payload).payload.properties.extra_details }}
          urgency: ${{ fromJson(inputs.port_payload).payload.properties.urgency }}
          service: ${{ fromJson(inputs.port_payload).context.entity }}
          blueprintIdentifier: 'pagerdutyIncident'
```

6. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)
![image](https://github.com/port-labs/pagerduty-incident-gha/assets/51213812/2cda51d4-4594-4f47-9ef4-3b2419b0351a)

7. Done! wait for the incident to be created in Pager Duty
![image](https://github.com/port-labs/pagerduty-incident-gha/assets/51213812/74cb8aad-e426-4ab1-b388-74b80a5d2eb1)

Congrats 🎉 You've created your first incident in PagerDuty from Port!


# Create PagerDuty Service Action

This GitHub action allows you to quickly create a service in PagerDuty via Port with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| name         | A unique identifier or title used to reference and distinguish the service in PagerDuty     | false    | -                  |
| description              | A brief summary or explanation detailing the purpose or scope of the service in PagerDuty.                               | true     | -                  |
| escalation_policy              | A set of rules in PagerDuty that determines the sequence of notifications to team members in response to an incident, ensuring timely attention and action                                                              | true    | -               |



## Quickstart - Create a `PagerDuty service` from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token) 

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
3. After you installed the integration, the blueprints `pagerdutyService` and `pagerdutyIncident` will appear, create the following action with the following JSON file on the `pagerdutyService` blueprint:

```json

[
  {
    "identifier": "create_pagerduty_service",
    "title": "create_pagerduty_service",
    "icon": "pagerduty",
    "userInputs": {
      "properties": {
        "name": {
          "title": "service_name",
          "description": "Name of the PagerDuty Service",
          "icon": "pagerduty",
          "type": "string"
        },
        "description": {
          "title": "service_description",
          "description": "Description of the PagerDuty Service",
          "icon": "pagerduty",
          "type": "string"
        },
        "escalation_policy": {
          "title": "escalation_policy_id",
          "icon": "pagerduty",
          "type": "object"
        }
      },
      "required": [
        "escalation_policy_id"
      ],
      "order": [
        "service_name",
        "service_description",
        "escalation_policy_id"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "my-org",
      "repo": "my-repo",
      "workflow": "create-a-service.yaml",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "CREATE",
    "description": "Create PagerDuty Service",
    "requiredApproval": false
  }
]

```
>**Note** Replace the invocation method with your own repository details.

4. Create a workflow file under .github/workflows/create-a-service.yaml with the following content:

```yml
name: Create PagerDuty Service
on:
  workflow_dispatch:
    inputs:
      service_name:
        description: 'Name of the PagerDuty Service'
        required: true
        type: string
      service_description:
        description: 'Description of the PagerDuty Service'
        required: true
        type: string
      escalation_policy_id:
        description: 'Escalation Policy ID for the service'
        required: true
        type: string

jobs:
  create-pagerduty-service:
    runs-on: ubuntu-latest
    steps:
      - name: Create Service in PagerDuty
        uses: fjogeleit/http-request-action@v1
        with:
          url: 'https://api.pagerduty.com/services'
          method: 'POST'
          customHeaders: '{"Content-Type": "application/json", "Accept": "application/vnd.pagerduty+json;version=2", "Authorization": "Token token=${{ secrets.PAGERDUTY_API_KEY }}"}'
          data: >-
            {
              "service": {
                "name": "${{ github.event.inputs.service_name }}",
                "description": "${{ github.event.inputs.service_description }}",
                "escalation_policy": ${{ github.event.inputs.escalation_policy }}
              }
            }
            - name: Create a log message

      - name: "Report Status to Port"
        id: log-response
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(inputs.port_payload).context.runId}}
          logMessage: |
             PagerDuty service created! ✅

```

5. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

Congrats 🎉 You've created your first `service` in PagerDuty from Port!

# Trigger A PagerDuty Incident Action

This GitHub action allows you to quickly trigger incidents in PagerDuty via Port Actions with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| payload         | Fields adding additional context about the location, impact, and character of the PagerDuty event.     | false    | -                  |
| routing_key              | The GUID of one of your PagerDuty Events API V2 integrations                                | true     | -                  |
| event_action              | The type of event. Can be trigger, acknowledge or resolve                                                              | false    | -               |
| dedup_key              | Identifies the alert to trigger, acknowledge, or resolve                                                             | true unless the event_type is trigger    | -               | 


## Quickstart - Trigger a PagerDuty incident from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token) 

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
3. After you installed the integration, the blueprints `pagerdutyService` and `pagerdutyIncident` will appear, create the following action with the following JSON file on the `pagerdutyService` blueprint:

```json

[
  {
    "identifier": "trigger_an_incident",
    "title": "trigger_an_incident",
    "icon": "pagerduty",
    "userInputs": {
      "properties": {
        "payload": {
          "title": "payload",
          "icon": "pagerduty",
          "type": "object"
        },
        "event_action": {
          "icon": "pagerduty",
          "title": "event_action",
          "type": "string"
        },
        "routing_key": {
          "icon": "pagerduty",
          "title": "routing_key",
          "type": "string"
        }
      },
      "required": [
        "routing_key",
        "event_action",
        "payload"
      ],
      "order": [
        "routing_key",
        "event_action",
        "payload"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "my-org-name",
      "repo": "my-repo",
      "workflow": "trigger-pagerduty-incident.yaml",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "DAY-2",
    "description": "Trigger a pagerduty incident using self service",
    "requiredApproval": false
  }
]

```
>**Note** Replace the invocation method with your own repository details.

4. Create a workflow file under .github/workflows/create-an-incident.yaml with the following content:

```yml
name: Trigger an Incident In PagerDuty
on:
  workflow_dispatch:
    inputs:
      payload:
        type: string
      event_action:
        type: string
      routing_key:
        type: string
      port_payload:
        required: true
        description: Port's payload, including details for who triggered the action and
          general context (blueprint, run id, etc...)
        type: string
jobs:
  create-entity-in-port-and-update-run:
    runs-on: ubuntu-latest
    steps: 

      - name: Inform starting of PagerDuty trigger 
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          operation: PATCH_RUN
          runId: ${{ fromJson(inputs.port_payload).context.runId }}
          logMessage: |
              About to trigger PagerDuty incident.. ⛴️
      - name: Send Event to PagerDuty
        id: trigger
        uses: fjogeleit/http-request-action@v1
        with:
          url: 'https://events.pagerduty.com/v2/enqueue'
          method: 'POST'
          customHeaders: '{"Content-Type": "application/json", "Accept": "application/json"}'
          data: >-
            {
              "payload": ${{ github.event.inputs.payload }},
              "event_action": "${{ github.event.inputs.event_action }}",
              "routing_key": "${{ github.event.inputs.routing_key }}"
            }  
      - name: Create a log message
        id: log-response
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(inputs.port_payload).context.runId}}
          logMessage: |
             PagerDuty incident triggered! ✅
             "The incident id is: ${{ steps.trigger.outputs}}"
```

5. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

Congrats 🎉 You've created your first incident in PagerDuty from Port!