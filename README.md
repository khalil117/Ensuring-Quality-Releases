# Udacity Cloud DevOps using Microsoft Azure Nanodegree Program - Project: Ensuring Quality Releases


## Introduction

This project uses Microsoft Azure and a variety of industry leading tools to create disposable test environments and run a variety of automated tests with the click of a button. 
Additionally it monitors and provides insight into the application's behavior, and determines root causes by querying the application’s custom log files.


## Getting Started

1. Fork this repository
2. Ensure you have all the dependencies
3. Follow the instructions below


## Dependencies

The following are the dependencies of the project you will need:

- Create an [Azure Account](https://portal.azure.com)
- Install the following tools:
  - [Azure command line interface](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
  - [Terraform](https://www.terraform.io/downloads.html)
  - [JMeter](https://jmeter.apache.org/download_jmeter.cgi)
  - [Postman](https://www.postman.com/downloads/)
  - [Python](https://www.python.org/downloads/)
  - [Selenium](https://sites.google.com/a/chromium.org/chromedriver/getting-started)

## Instructions

### Login with Azure CLI

Firstly, login to the Azure CLI using:

```bash
az login
```

### Configure the storage account and state backend

Terraform supports the persisting of state in remote storage. See [Tutorial: Store Terraform state in Azure Storage](https://docs.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage) for details or follow the instructions below.

Firstly, execute the `create-tf-storage.sh` script:

```bash
bash create-tf-storage.sh
```

Update `terraform/terraform.tfvars` with the Terraform storage account and state backend configuration variables:

- `storage_account_name`: The name of the Azure Storage account
- `container_name`: The name of the blob container
- `key`: The name of the state store file to be created

```bash

    resource_group_name  = "tstate"
    storage_account_name = "tstate00000"
    container_name       = "tstate"
    key                  = "terraform.tfstate"

```

We create now our Virtual Machine myLinuxVM and we log into it `ssh devopsagent@20.172.154.102`

### Setting up Azure DevOps

Create a free [Azure DevOps account](https://azure.microsoft.com/en-us/services/devops/) if you haven't already and install the [Terraform Extension for Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks).

Create a new project:
Create a new PAT: myPAT and we cpoy the personal access token
Create a new service connection: 
Create a new Agent Pool: myAgentPool

We will use our VM as an agent 

curl -O https://vstsagentpackage.azureedge.net/agent/2.210.1/vsts-agent-linux-x64-2.210.1
mkdir myagent && cd myagent
tar zxvf ../vsts-agent-linux-x64-2.210.1.tar.gz
./config.sh
sudo ./svc.sh install
sudo ./svc.sh start

Create a new environment: TEST

we copy the registration script and we run it in the VM 

We move to library and we add azsecret as variable group and ssh key on secure files 

sudo apt-get -y install zip 
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash 
sudo apt-get install npm 