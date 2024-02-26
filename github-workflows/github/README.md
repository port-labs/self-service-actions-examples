<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Dora Metrics 

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly ingest dora metrics of each service (repository) via Port Actions with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| name         | A unique identifier or title used to reference and distinguish the service in PagerDuty     | true    | -                  |
| repository              | GitHub repository in owner/repo format eg. port-labs/self-service-actions                               | false     | -                  |
| timeframe              | Time frame in weeks to calculate metrics on                                                              | true    | -               |

## Quickstart - Dora Metrics

1. Create the following GitHub action secrets
* `PATTOKEN` - GitHub PAT token. Ensure that Read access to actions and metadata permission is set. [learn more](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Create a blueprint for Github Repository Instance

```json
{
  "identifier": "githubRepository",
  "title": "Repository",
  "icon": "Microservice",
  "schema": {
    "properties": {
      "readme": {
        "title": "README",
        "type": "string",
        "format": "markdown"
      },
      "url": {
        "title": "Repository URL",
        "type": "string",
        "format": "url"
      },
      "defaultBranch": {
        "title": "Default branch",
        "type": "string"
      }
    },
    "required": []
  },
  "mirrorProperties": {},
  "calculationProperties": {},
  "relations": {}
}
```
4. Create the following action with the following JSON file on the `Repository` blueprint:

```json
{
  "identifier": "dora_metrics",
  "title": "Dora Metrics",
  "icon": "Github",
  "userInputs": {
    "properties": {
      "timeframe": {
        "icon": "Github",a
        "title": "Timeframe",
        "description": "Time frame in weeks to calculate metrics on",
        "type": "number",
        "default": 4,
        "minimum": 1
      },
      "repository": {
        "title": "Repository",
        "type": "string"
      }
    },
    "required": [
      "timeframe",
      "repository"
    ],
    "order": [
      "timeframe"
    ]
  },
  "invocationMethod": {
    "type": "GITHUB",
    "org": "my-org",
    "repo": "my-org",
    "workflow": "dora-metrics.yaml",
    "omitUserInputs": false,
    "omitPayload": false,
    "reportWorkflowStatus": true
  },
  "trigger": "DAY-2",
  "description": "Estimate dora metrics for a service",
  "requiredApproval": false
}
```
>**Note** Replace the invocation method with your own repository details.

5. Create a blueprint for Dora metrics to be ingested

```json
{
  "identifier": "doraMetrics",
  "title": "Dora Metrics",
  "icon": "Github",
  "schema": {
    "properties": {
      "averageOpenToCloseTime": {
        "icon": "DefaultProperty",
        "title": "Average Open To Close Time",
        "type": "number"
      },
      "averageTimeToFirstReview": {
        "title": "Average Time To First Review",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "averageTimeToApproval": {
        "title": "Average Time To Approval",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "prsOpened": {
        "title": "PRs Opened",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "weeklyPrsMerged": {
        "title": "Weekly PRs Merged",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "averageReviewsPerPr": {
        "title": "Average Review Per PR",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "averageCommitsPerPr": {
        "title": "Average Commits Per PR",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "averageLocChangedPerPr": {
        "title": "Average Loc Changed Per Per",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "averagePrsReviewedPerWeek": {
        "title": "Average PRs Review Per Week",
        "type": "number",
        "icon": "DefaultProperty"
      },
      "totalDeployments": {
        "title": "Total Deployments",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "deploymentRating": {
        "title": "Deployment Rating",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "numberOfUniqueDeploymentDays": {
        "title": "Number of Unique Deployments",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "deploymentFrequency": {
        "title": "Deployment Frequency",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "leadTimeForChangesInHours": {
        "title": "Lead Time For Changes In Hours",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "leadTimeRating": {
        "title": "Lead Time Rating",
        "type": "string",
        "icon": "DefaultProperty"
      },
      "workflowAverageTimeDuration": {
        "title": "Workflow Average Time Duration",
        "type": "string",
        "icon": "DefaultProperty"
      }
    },
    "required": []
  },
  "mirrorProperties": {},
  "calculationProperties": {},
  "aggregationProperties": {},
  "relations": {}
}
```

6. Create a workflow file under .github/workflows/dora-metrics.yaml with the content in [dora-metrics.yml](./dora-metrics.yaml)

```yaml
name: Ingest DORA Metrics

on:
  workflow_dispatch:
    inputs:
      repository:
        description: 'GitHub repository in owner/repo format eg. port-labs/self-service-actions'
        required: true
      timeframe:
        description: 'Last X weeks'
        required: true
      port_payload:
        required: true
        description: Port's payload, including details for who triggered the action and
          general context (blueprint, run id, etc...)
        type: string
      
jobs:
  compute-dora-metrics:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Convert Weeks to Days
        run: |
          days=$(( ${{ github.event.inputs.timeframe }} * 7 ))
          echo "TIMEFRAME_IN_DAYS=$days" >> $GITHUB_ENV
        shell: bash

      - name: Log Before Running Deployment Frequency
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(github.event.inputs.port_payload).context.runId}}
          logMessage: "Computing deployment frequency metric ..."
          
      - name: Run DORA Deployment Frequency 
        id: deploymement_frequency
        shell: pwsh
        run: |
           $scriptPath = Join-Path ${{ github.workspace }} "src/deploymentfrequency.ps1"
           chmod +x $scriptPath
           $output = & pwsh -File $scriptPath -ownerRepo "${{ inputs.repository }}" -workflows "CI/CD" -branch "main" -numberOfDays '60' -patToken "${{ secrets.PATTOKEN }}"
           $jsonLine = $output | Select-String "###JSON_START###" -Context 0,1 | ForEach-Object { $_.Context.PostContext }
           echo "::set-output name=jsonResult::$jsonLine"

      - name: Set Deployment Frequency Results
        run: |
          results='${{ steps.lead_time_for_changes.outputs.jsonResult }}'
          echo "Lead Time for Changes Results: $results"
          echo "TOTAL_DEPLOYMENTS=$(echo $results | jq -r '.TotalDeployments')" >> $GITHUB_ENV
          echo "DEPLOYMENT_FREQUENCY_RATING=$(echo $results | jq -r '.Rating')" >> $GITHUB_ENV
          echo "NUMBER_OF_UNIQUE_DEPLOYMENTS=$(echo $results | jq -r '.NumberOfUniqueDeploymentDays')" >> $GITHUB_ENV
          echo "DEPLOYMENT_FREQUENCY=$(echo $results | jq -r '.DeploymentFrequency')" >> $GITHUB_ENV
        shell: bash

      - name: Log Before Running Lead Time for Changes
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(github.event.inputs.port_payload).context.runId}}
          logMessage: "Computing lead time for changes metric ..."
          
      - name: Run DORA Lead Time for Changes
        id: lead_time_for_changes
        shell: pwsh
        run: |
          $scriptPath = Join-Path ${{ github.workspace }} "src/leadtimeforchanges.ps1"
          chmod +x $scriptPath
          $output = & pwsh -File $scriptPath -ownerRepo "${{ github.event.inputs.repository }}" -workflows "CI/CD" -branch "main" -numberOfDays '30' -commitCountingMethod "last" -patToken "${{ secrets.PATTOKEN }}"
          $jsonLine = $output | Select-String "###JSON_START###" -Context 0,1 | ForEach-Object { $_.Context.PostContext }
          echo "::set-output name=jsonResult::$jsonLine"

      - name: Set Lead Time for Changes Results
        run: |
          results='${{ steps.lead_time_for_changes.outputs.jsonResult }}'
          echo "Lead Time for Changes Results: $results"
          echo "LEAD_TIME_FOR_CHANGES_IN_HOURS=$(echo $results | jq -r '.LeadTimeForChangesInHours')" >> $GITHUB_ENV
          echo "LEAD_TIME_RATING=$(echo $results | jq -r '.Rating')" >> $GITHUB_ENV
          echo "WORKFLOW_AVERAGE_TIME_DURATION=$(echo $results | jq -r '.WorkflowAverageTimeDuration')" >> $GITHUB_ENV
          echo "PR_AVERAGE_TIME_DURATION=$(echo $results | jq -r '.PRAverageTimeDuration')" >> $GITHUB_ENV
        shell: bash

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      
      - name: Install Python dependencies
        run: |
          python -m pip install --upgrade pip
          # pip install httpx
          pip install PyGithub datetime

      - name: Log Before Running PR Metrics
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(github.event.inputs.port_payload).context.runId}}
          logMessage: "Computing PR metrics on the repository ..."
          
      - name: Compute PR Metrics
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Use the automatically provided GITHUB_TOKEN
          REPOSITORY: ${{ github.event.inputs.repository }}
        run: python src/calculate_pr_metrics.py

      - name: Log Before Upserting Entity
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(github.event.inputs.port_payload).context.runId}}
          logMessage: "Upserting dora metrics to Port"

      - name: UPSERT Entity
        uses: port-labs/port-github-action@v1
        with:
          identifier: "${{ fromJson(env.metrics).id }}"
          title: ${{ github.event.inputs.repository }}
          blueprint: doraMetrics
          properties: |-
            {
              "totalDeployments": "${{ env.TOTAL_DEPLOYMENTS }}",
              "deploymentRating": "${{ env.DEPLOYMENT_RATING }}",
              "numberOfUniqueDeploymentDays": "${{ env.NUMBER_OF_UNIQUE_DEPLOYMENTS }}",
              "deploymentFrequency": "${{ env.DEPLOYMENT_FREQUENCY }}",
              "leadTimeForChangesInHours": "${{ env.LEAD_TIME_FOR_CHANGES_IN_HOURS }}",
              "leadTimeRating": "${{ env.LEAD_TIME_RATING }}",
              "workflowAverageTimeDuration": "${{ env.WORKFLOW_AVERAGE_TIME_DURATION }}",
              "prAverageTimeDuration": "${{ env.PR_AVERAGE_TIME_DURATION }}",
              "averageOpenToCloseTime": "${{ fromJson(env.metrics).average_open_to_close_time }}",
              "averageTimeToFirstReview": "${{ fromJson(env.metrics).average_time_to_first_review }}",
              "averageTimeToApproval": "${{ fromJson(env.metrics).average_time_to_approval }}",
              "prsOpened": "${{ fromJson(env.metrics).prs_opened }}",
              "weeklyPrsMerged": "${{ fromJson(env.metrics).weekly_prs_merged }}",
              "averageReviewsPerPr": "${{ fromJson(env.metrics).average_reviews_per_pr }}",
              "averageCommitsPerPr": "${{ fromJson(env.metrics).average_commits_per_pr }}",
              "averageLocChangedPerPr": "${{ fromJson(env.metrics).average_loc_changed_per_pr }}",
              "averagePrsReviewedPerWeek": "${{ fromJson(env.metrics).average_prs_reviewed_per_week }}"
            }
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: UPSERT
          runId: ${{ fromJson(inputs.port_payload).context.runId }}

      - name: Log After Upserting Entity
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          baseUrl: https://api.getport.io
          operation: PATCH_RUN
          runId: ${{fromJson(github.event.inputs.port_payload).context.runId}}
          logMessage: "Entity upserting was successful ✅"
```
7. Create a python script under src/calculate_pr_metrics.py with the content in [calculate_pr_metrics.py](./src/calculate_pr_metrics.py)

8. Create a powershell script under src/deploymentfrequency.ps1 with the content in [deploymentfrequency.ps1](./src/deploymentfrequency.ps1)

9. Create a powershell script under src/leadtimeforchanges.ps1 with the content in [leadtimeforchanges.ps1](./src/leadtimeforchanges.ps1)

10. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

Congrats 🎉 You've triggered your first incident in Opsgenie from Port!
