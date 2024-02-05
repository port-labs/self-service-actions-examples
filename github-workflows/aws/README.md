<img align="right" width="100" height="74" src="https://user-images.githubusercontent.com/8277210/183290025-d7b24277-dfb4-4ce1-bece-7fe0ecd5efd4.svg" />

# Create An AWS EC2 Instance Action
[![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)](https://join.slack.com/t/devex-community/shared_invite/zt-1bmf5621e-GGfuJdMPK2D8UN58qL4E_g)

This GitHub action allows you to quickly create an EC2 Instance in AWS via Port with ease.

## Inputs
| Name                 | Description                                                                                          | Required | Default            |
|----------------------|------------------------------------------------------------------------------------------------------|----------|--------------------|
| ec2_name                | The name of the ec2 instance      | true     | -                  |
| ec2_instance_type               | The name of the ec2 instance type     | false     | t3.micro                  |
| pem_key_name                | The name of the .pem key pair      | true     | -                  |


## Quickstart - Create an EC2 Instance from the service catalog

Follow these steps to get started with the Golang template

1. Create an SSH key pair to connect with the provisioned instance. [learn more](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html#having-ec2-create-your-key-pair)
2. Create the following GitHub action secrets
* `TF_USER_AWS_KEY` - an aws access key with the right iam permission to create an ec2 instance [learn more](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
* `TF_USER_AWS_SECRET` - an aws access key secret with permission to create an ec2 instance [learn more](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
* `TF_USER_AWS_REGION` - the aws region where you would like to provision your ec2 instance.
* `PORT_CLIENT_ID` - Port Client ID [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
* `PORT_CLIENT_SECRET` - Port Client Secret [learn more](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#get-api-token)
3. Install the Ports GitHub app from [here](https://github.com/apps/getport-io/installations/new).
4. Create a blueprint for EC2 Instance

```json

{
  "identifier": "ec2Instance",
  "description": "This blueprint represents an AWS EC2 instance in our software catalog",
  "title": "EC2 Instance",
  "icon": "EC2",
  "schema": {
    "properties": {
      "architecture": {
        "type": "string",
        "title": "Architecture",
        "enum": ["i386", "x86_64", "arm64", "x86_64_mac", "arm64_mac"]
      },
      "availabilityZone": {
        "type": "string",
        "title": "Availability Zone"
      },
      "link": {
        "type": "string",
        "title": "Link",
        "format": "url"
      },
      "platform": {
        "type": "string",
        "title": "Platform"
      },
      "state": {
        "type": "string",
        "title": "State",
        "enum": [
          "pending",
          "running",
          "shutting-down",
          "terminated",
          "stopping",
          "stopped"
        ],
        "enumColors": {
          "pending": "yellow",
          "running": "green",
          "shutting-down": "pink",
          "stopped": "purple",
          "stopping": "orange",
          "terminated": "red"
        }
      },
      "tags": {
        "type": "array",
        "title": "Tags"
      },
      "type": {
        "type": "string",
        "title": "Instance Type"
      },
      "vpcId": {
        "type": "string",
        "title": "VPC ID"
      }
    },
    "required": []
  },
  "mirrorProperties": {},
  "calculationProperties": {},
  "relations": {}
}
```

5. Create the following action with the following JSON file on the `EC2 Instance` blueprint:

```json
[
  {
    "identifier": "create_an_ec_2_instance",
    "title": "Create An EC2 Instance",
    "icon": "EC2",
    "userInputs": {
      "properties": {
        "pem_key_name": {
          "title": "pem_key_name",
          "description": "EC2 .pem key pair name",
          "icon": "EC2",
          "type": "string"
        },
        "ec2_name": {
          "icon": "EC2",
          "title": "ec2_name",
          "description": "Name of the instance",
          "type": "string"
        },
        "ec2_instance_type": {
          "icon": "EC2",
          "title": "ec2_instance_type",
          "description": "EC2 instance type",
          "type": "string",
          "default": "t3.micro"
        }
      },
      "required": [
        "ec2_name",
        "pem_key_name"
      ],
      "order": [
        "ec2_name",
        "ec2_instance_type",
        "pem_key_name"
      ]
    },
    "invocationMethod": {
      "type": "GITHUB",
      "org": "mk-armah",
      "repo": "my-repo",
      "workflow": "create-ec2-instance.yaml",
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

6. Create a workflow file under .github/workflows/create-an-ec2-instance.yaml with the following content:
```yml

name: Provision AN EC2 Instance

on:
  workflow_dispatch:
    inputs:
      ec2_name:
        description: EC2 name
        required: true
        default: 'App Server'
        type: string
      ec2_instance_type:
        description: EC2 instance type
        required: false
        default: "t3.micro"
        type: string
      pem_key_name:
        description: EC2 pem key
        required: true
        type: string
      port_payload:
        required: true
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
              About to create ec2 instance ${{ github.event.inputs.ec2_name }} .. ⛴️

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: '${{ secrets.TF_USER_AWS_KEY }}'
          aws-secret-access-key: '${{ secrets.TF_USER_AWS_SECRET }}'
          aws-region: '${{ secrets.TF_USER_AWS_REGION }}'

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_wrapper: false
          
      - name: Terraform Apply
        id:   apply
        env:
          TF_VAR_ec2_name:  "${{ github.event.inputs.ec2_name }}"
          TF_VAR_pem_key_name: "${{ github.event.inputs.pem_key_name}}"
          TF_VAR_aws_region: "${{ secrets.TF_USER_AWS_REGION }}"
          TF_VAR_ec2_instance_type: "${{ github.event.inputs.ec2_instance_type}}"
        run: |
          cd github-workflows/aws/terraform-examples/ec2/
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

7. Create terraform files ( main.tf and variables.tf ) for provisioning the instance


```hcl title="main.tf"
data "aws_ami" "ubuntu" {
    most_recent = true

    filter {
        name   = "name"
        values = ["ubuntu/images/hvm-ssd/*20.04-amd64-server-*"]
    }

    filter {
        name   = "virtualization-type"
        values = ["hvm"]
    }
    
    owners = ["099720109477"] # Canonical
}

provider "aws" {
  region  = var.aws_region
}

resource "aws_instance" "app_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.ec2_instance_type
  key_name      = var.pem_key_name

  tags = {
    Name = var.ec2_name
  }
}

```

```hcl title = "variables.tf"
variable "ec2_name" {
  type = string
}

variable "pem_key_name" {
  type = string
}

variable "aws_region" {
  type = string
}

variable "ec2_instance_type" {
  type = string
}
```

8. Trigger the action from Port's [Self Serve](https://app.getport.io/self-serve)

Congrats 🎉 You've created your instance in EC2 from Port!

