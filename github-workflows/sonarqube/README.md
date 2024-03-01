<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Add tags to SonarQube project

[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly add tags to a SonarQube project via Port Actions with ease.

## Prerequisites
* SonarQube instance URL, SonarQube API token. Check [SonarQube's documentation](https://docs.sonarsource.com/sonarqube/latest/user-guide/user-account/generating-and-using-tokens/) on how to retrieve your API Token
* [Port's GitHub app](https://github.com/apps/getport-io) installed

## Quickstart - Add tag to SonarQube project from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `SONARQUBE_HOST_URL` - SonarQube instance URL. `https://sonarqube.com` if using Sonarcloud.
* `SONARQUBE_API_TOKEN` - SonarQube API token. This can be a Project Analysis token for the specific project, a Global Analysis token or a user token. Requires the following permission: 'Administer' rights on the specified project.
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)

2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Optional - Install Port's SonarQube integration [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/code-quality-security/sonarqube)
>**Note** This step is not required for this example, but it will create all the blueprint boilerplate for you, and also update the catalog in real time with the new incident created.
4. After you installed the integration, the blueprints `sonarQubeProject`, `sonarQubeIssue` and `sonarQubeAnalysis` will appear, create the following action with the following JSON file on the `sonarQubeProject` blueprint:

```json
[
  {
  "identifier": "add_tags_to_sonar_qube_project",
  "title": "Add Tags to SonarQube project",
  "icon": "sonarqube",
  "userInputs": {
    "properties": {
      "tags": {
        "title": "Tags",
        "description": "Comma separated list of tags",
        "icon": "DefaultProperty",
        "type": "string"
      }
    },
    "required": [
      "tags"
    ],
    "order": [
      "tags"
    ]
  },
  "invocationMethod": {
    "type": "GITHUB",
    "org": "<Enter GitHub organization>",
    "repo": "<Enter GitHub repository>",
    "workflow": "add-tags-to-sonarqube-project.yml",
    "omitUserInputs": false,
    "omitPayload": false,
    "reportWorkflowStatus": true
  },
  "trigger": "DAY-2",
  "description": "Adds additional tags to a project in SonarQube",
  "requiredApproval": false
}
]
```
>**Note** Update the `invocationMethod` field with your own repository details.

5. Create a workflow file under .github/workflows/add-tags-to-sonarqube-project.yml with the content in [add-tags-to-sonarqube-project.yml](./add-tags-to-sonarqube-project.yml)

6. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

7. Done! wait for the project tags to be updated in SonarQube

Congrats 🎉 You've added tags to your SonarQube project from Port!

**Note:** Due to SonarQube API's limitation, this action replaces the tags on the project with the new ones specified. If you want to add to the already existing tags, copy the existing tags and add it with the new ones you are adding.
