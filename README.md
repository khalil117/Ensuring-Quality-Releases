# Udacity Cloud DevOps using Microsoft Azure Nanodegree Program - Project: Ensuring Quality Releases

## Status

![Badge](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/badget.PNG?raw=true)

## Introduction

This project uses Microsoft Azure and a variety of industry leading tools to create disposable test environments and run a variety of automated tests with the click of a button. 
Additionally it monitors and provides insight into the application's behavior, and determines root causes by querying the application’s custom log files.

![Structure](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/structure.PNG?raw=true)

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


### Login with Azure CLI

Firstly, login to the Azure CLI using:

```bash
az login
```

### Configure the storage account and state backend

Terraform supports the persisting of state in remote storage. See [Tutorial: Store Terraform state in Azure Storage](https://docs.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage) for details or follow the instructions below.

Firstly, execute the `config.sh` script:

```bash
bash config.sh
```
We will get 3 outputs:

- `storage_account_name`: The name of the Azure Storage account
- `container_name`: The name of the blob container
- `key`: The name of the state store file to be created
Update `terraform/terraform.tfvars` with the Terraform storage account and state backend configuration variables:

```bash

    resource_group_name  = "tstate"
    storage_account_name = "tstate00000"
    container_name       = "tstate"
    key                  = "terraform.tfstate"

```
Update `terraform/main.tf` with the Terraform storage account and state backend configuration variables:  

### Create a Service Principal for Terraform
In the ```main.tf``` file we need the following data:
- tenant_id
- subscription_id
- client_id
- client_secret


For this we have to obtain our subscription_id with the followging command:

```Bash
az account show
```

Copy the "id" field. Now it is time to create the service principal, input the following command:

```Bash
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/your-subscription-id"
```

We will get an output similar to this:
```Bash
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-cli-2017-06-05-10-41-15",
  "name": "9d778b04-cfbe-4f86-947e-000000000000",
  "password": "0000-0000-0000-0000-000000000000",
  "tenant": "00000000-0000-0000-0000-000000000000"
}
```

These values map to the Terraform variables like so:

- appId is the client_id defined above.
- password is the client_secret defined above.
- tenant is the tenant_id defined above.
- `storage_account_name`: The name of the Azure Storage account
- `container_name`: The name of the blob container
- `key`: The name of the state store file to be created

We will add this values to our ```azurecreds.conf``` file, so at the end we will have data similar to this in our conf file:
```Bash
subscription_id = "12345678-b866-4328-925f-123456789"
client_id = "00000000-0000-0000-0000-000000000000"
client_secret = "0000-0000-0000-0000-000000000000"
tenant_id = "00000000-0000-0000-0000-000000000000"

storage_account_name = "tfstate32602"
container_name = "tstate"
key = "test.terraform.tfstate"
access_key = "bGCCY94tbnC8CMouMJq4+at1Q8s+mHaDQlfsdeWKZHsboOGAm9DXiV0lRWxW5hJVyLME3VhPuBvI+AStdzgrtg=="
```

We are now ready to configure an Azure DevOps Pipeline.



Before we setting up our Pipeline , We create  our Virtual Machine `myLinuxVM` and we log into it `ssh devopsagent@20.172.154.102`

### Setting up Azure DevOps

Create a free [Azure DevOps account](https://azure.microsoft.com/en-us/services/devops/) if you haven't already and install the [Terraform Extension for Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks).

Create a new project:

Create a new PAT: myPAT and we cpoy the personal access token

Create a new service connection: 

Create a new Agent Pool: myAgentPool

We will need to install Terraform extension from Microsoft DevLabs to use terraform in our DevOps Project, install it from the following URL: https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks

We will use our VM as an agent 

```Bash
curl -O https://vstsagentpackage.azureedge.net/agent/2.210.1/vsts-agent-linux-x64-2.210.1.tar.gz

mkdir myagent && cd myagent

tar zxvf ../vsts-agent-linux-x64-2.210.1.tar.gz

./config.sh

sudo ./svc.sh install

sudo ./svc.sh start
```

Create a new environment: TEST

we copy the registration script and we run it in the VM 

We will also need a variables group, we will add the following data in a variable group named ```azsecret```:
- client_id: 'your-client-id'
- client_secret: 'your-client-secret' (click on the lock to change it to a secret variable)
- subscription_id: 'your-subscription-id'
- tenant_id: 'your-tenant-id'
- public_key: 'your-public-key'

The next step is to upload our azsecret.conf to Azure Devops as a Secure File, to do this we have to navigate to Pipelines -> Library -> Secure Files -> + Secure File -> Upload File. Now the file should be uploaded.

Same steps will be done with  ```az_eqr_id_rsa``` , ```az_eqr_id_rsa.pub``` and ```known_hosts```

We run the following command on our VM

```sudo apt-get -y install zip```

```curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash ```

```sudo apt-get install npm ```

We are ready to run the Provision stage of our pipeline.

If all the configuration was correct, then the terraform apply command should be successful, and our resources should be deployed to the cloud.

![terraformApply](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/terraform_apply.PNG?raw=true)


## Instructions


### Regression test

![regression_test](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/regression_test.PNG?raw=true)

### Validation test

![regression_test](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/validation_tests.PNG?raw=true)

### Publish Test Results

![Junit_regression_test](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/Junit_regression_test.PNG?raw=true)

![Junit_validation_test](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/Junit%20_validation_test.PNG?raw=true)

### Selenium Test Result

![selenium_test](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/Selenium_test.PNG?raw=true)

## Enable Monitoring & Observability

In this final section, we will enable Monitoring & Observability in our Virtual Machine and App Service to observe the effects of our tests.

To run the Deploy stage of our pipeline we must configure an Azure Log Analytics Workspace before running the Deploy Virtual Machine task. To do this run the ```setup-log-analytics.sh``` file in the deployments directory, modify as needed and refer to the official Microsoft documentation if needed: https://docs.microsoft.com/en-us/azure/azure-monitor/logs/resource-manager-workspace

After that, navigate to the Azure Portal, go to the resource group where the Workspace was created, click on the resource and navigate to Settings -> Agents Management.


Navigate to Linux Servers and there will be the script to install the Linux Agent in our Virtual Machine.

For security, copy the Workspace ID and the Primary Key, set them up in our variables group, and reference them as an Environment variable in the pipeline.

We are ready to run the Deploy stage of the pipeline!

If everything worked as intented, we should see "1 Linux computers connected" in the Agents Management in the Log Analytics Workspace.

![1 Linux Server Connected](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/LAW.PNG?raw=true)

### Azure Monitor

Screenshots of the email received when the alert is triggered (including timestamps):

![email_alert](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/alert_email.PNG?raw=true)

The graphs of the resource that the alert was triggered for (including timestamps):

![metric](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/metric.PNG?raw=true)

### Azure Log Analytics

Screenshot of log analytics query and the result sets which shows specific output of the Azure resource:

![rules](https://github.com/khalil117/Ensuring-Quality-Releases/blob/main/images/selenium_log.PNG?raw=true)

The result set include the output of the execution of the Selenium Test Suite.

## Future Work
- We could cause errors or other scenarios for the AppService/VM and demonstrate those behaviors in the test suites as well as in Azure Monitor and Log Analytics.
- We could create a VM Scale Set in Terraform and complete each of the steps with the VM Scale set.

## References
- [Udacity Project Starter Files](https://video.udacity-data.com/topher/2020/June/5ed815bf_project-starter-resources/project-starter-resources.zip)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Outlook](https://outlook.live.com/)
- [Azure](https://azure.microsoft.com/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Command Line Interface](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli#install-terraform)
- [Terraform Azure Documentation](https://learn.hashicorp.com/collections/terraform/azure-get-started)
- [Java Development Kit](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
- [Jmeter](https://jmeter.apache.org/download_jmeter.cgi)
- [Postman](https://www.postman.com/downloads/)
- [Python](https://www.python.org/downloads/)
- [Selenium for Python](https://pypi.org/project/selenium/)
- [Chromedriver](https://sites.google.com/a/chromium.org/chromedriver/downloads)
- [Tutorial: Store Terraform state in Azure Storage](https://docs.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage)
- [Get subscription id with Azure CLI](https://yourazurecoach.com/2020/07/14/get-subscription-id-with-azure-cli/)
- [Azure Provider: Authenticating using a Service Principal with a Client Secret](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_client_secret)
- [Terraform - Microsoft DevLabs](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks)
- [Install SSH Key Task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/install-ssh-key?view=azure-devops)
- [Azure CLI Authentication does not work when using the Azure CLI task from Azure DevOps](https://github.com/terraform-providers/terraform-provider-azurerm/issues/3814)
- [Resources in YAML](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/resources?view=azure-devops&tabs=schema)
- [Terraform on Azure Pipelines Best Practices](https://julie.io/writing/terraform-on-azure-pipelines-best-practices/)
- [Use secure files](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/secure-files?view=azure-devops)
- [Create a Log Analytics workspace with Azure CLI 2.0](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Install Log Analytics agent on Linux computers](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/agent-linux)
- [Sauce Demo](https://www.saucedemo.com/)
- [Running collections on the command line with Newman](https://learning.postman.com/docs/running-collections/using-newman-cli/command-line-integration-with-newman/)
- [Dummy Rest API Example](http://dummy.restapiexample.com)
- [Collect custom logs with Log Analytics agent in Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/data-sources-custom-logs)
