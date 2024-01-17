<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Create An AWS EC2 Instance Action
[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly create an EC2 Instance in AWS via Port with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| ec2-name                | The name of the ec2 instance      | true     | -                  |


## Quickstart - Create a pager duty incident from the service catalog

Follow these steps to get started with the Golang template

1. Create the following GitHub action secrets
* `TF_USER_AWS_KEY` - an aws access key with the right iam permission to create an ec2 instance [learn more](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
* `TF_USER_AWS_SECRET` - an aws access key secret with permission to create an ec2 instance [learn more](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token) 
2. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
3. Create a blueprint for EC2 Instance [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/cloud-providers/aws/examples/#:~:text=EC2%20instance%20blueprint])
4. Create the following action with the following JSON file on the `EC2 Instance` blueprint:

```json
[
  {
    "identifier": "create_an_ec_2_instance",
    "title": "Create An EC2 Instance",
    "icon": "EC2",
    "userInputs": {
      "properties": {
        "ec_2_name": {
          "title": "ec2-name",
          "description": "Name of the instance",
          "icon": "EC2",
          "type": "string"
        }
      },
      "required": [
        "ec_2_name"
      ],
      "order": [
        "ec_2_name"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "my-org",
      "repo": "my-repo",
      "workflow": "create-an-ec2-instance",
      "omitUserInputs": false,
      "omitPayload": false,
      "reportWorkflowStatus": true
    },
    "trigger": "CREATE",
    "description": "Create An EC2 Instance from Port",
    "requiredApproval": false
  }
]
```
>**Note** Replace the invocation method with your own repository details.

5. Create a workflow file under .github/workflows/create-an-ec2-instance.yaml with the following content:
```yml

name: Provision t3.micro EC2

on:
  workflow_dispatch:
    inputs:
      ec2-name:
        description: EC2 name
        required: true
        default: 'App Server'
        type: string
jobs:
  provision-ec2:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '14'

      - name: Log starting of EC2 Instance creation 
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          operation: PATCH_RUN
          runId: ${{ fromJson(inputs.port_payload).context.runId }}
          logMessage: |
              About to create ec2 instance ${{ github.event.inputs.ec2-name }} .. ⛴️

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: '${{ secrets.TF_USER_AWS_KEY }}'
          aws-secret-access-key: '${{ secrets.TF_USER_AWS_SECRET }}'
          aws-region: us-east-1

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_wrapper: false
          
      - name: Terraform Apply
        id:   apply
        env:
          TF_VAR_ec2_name:  "${{ github.event.inputs.ec2-name }}"
        run: |
          cd tf-example/ec2/
          terraform init
          terraform validate
          terraform plan 
          terraform apply -auto-approve

      - name: Create a log message
        uses: port-labs/port-github-action@v1
        with:
          clientId: ${{ secrets.PORT_CLIENT_ID }}
          clientSecret: ${{ secrets.PORT_CLIENT_SECRET }}
          operation: PATCH_RUN
          runId: ${{ fromJson(inputs.port_payload).context.runId }}
          logMessage: |
              EC2 Instance created successfully ✅

```

5. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

Congrats 🎉 You've created your instance in EC2 from Port!
