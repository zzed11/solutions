
# Azure Infrastructure with Autoscaling VMs and Azure Function

This project sets up an Azure infrastructure using Terraform, including two autoscaling VM scale sets and an Azure Function that checks for files in a Blob Storage container and moves them to the VMs. The infrastructure includes:

- Resource Group
- Storage Account
- Storage Containers
- Virtual Network and Subnet
- Load Balancer
- Two VM Scale Sets with Autoscaling
- Azure Function App

## Prerequisites

- Azure CLI
- Terraform
- Python 3.8+
- Azure Subscription

## Directory Structure

```
terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── storage.tf
├── network.tf
├── vmss_goa.tf
├── vmss_opswat.tf
├── function_app/
│   ├── __init__.py
│   └── function.json
└── scripts/
    └── deploy_function.sh
```

## Setup Instructions

### 1. Configure Terraform

1. **Initialize Terraform**:

    ```sh
    terraform init
    ```

2. **Create an Execution Plan**:

    ```sh
    terraform plan
    ```

3. **Apply the Execution Plan**:

    ```sh
    terraform apply
    ```

### 2. Deploy Function App

1. **Function App Deployment Script**:

    ```sh
    # Navigate to the terraform directory
    cd terraform

    # Ensure deploy_function.sh is executable
    chmod +x scripts/deploy_function.sh

    # Run the deployment script (this is executed as part of the null_resource in Terraform)
    ./scripts/deploy_function.sh opswat-function-app opswat-resource-group function_app
    ```

## Files and Configuration

### main.tf

Sets up the provider, resource group, and main components of the infrastructure.

### variables.tf

Defines the variables used throughout the Terraform configuration.

### outputs.tf

Specifies the outputs of the Terraform configuration, such as the names of the created resources.

### storage.tf

Configures the storage account and containers.

### network.tf

Sets up the virtual network, subnet, public IP, and load balancer.

### vmss_goa.tf

Creates the first VM scale set named 'goa' with autoscaling settings.

### vmss_opswat.tf

Creates the second VM scale set named 'opswat' with autoscaling settings.

### function_app/__init__.py

Azure Function code that checks for the existence of a file in the `opswat-in` container and moves it to a VM in the `opswat` autoscale set.

### function_app/function.json

Configuration for the Azure Function.

### scripts/deploy_function.sh

Script to deploy the Azure Function code to the Azure Function App.

## Environment Variables

Ensure the following environment variables are set in your Azure Function App settings:

- `AzureWebJobsStorage`: Connection string for the storage account.
- `AZURE_SUBSCRIPTION_ID`: Azure subscription ID.

## Azure Function Usage

The Azure Function can be triggered via an HTTP request to check for a file in the `opswat-in` container and move it to the VMs in the `opswat` autoscale set.

### Trigger the Function

1. **HTTP GET Request**:

    ```sh
    curl -X GET "https://<your-function-app-name>.azurewebsites.net/api/<your-function-name>?file_name=<your-file-name>"
    ```

    Replace `<your-function-app-name>`, `<your-function-name>`, and `<your-file-name>` with appropriate values.

## Notes

- Ensure that the Azure Function App and VM scale sets have the appropriate network and security configurations to allow file transfers.
- Customize the file transfer logic in `__init__.py` to suit your specific requirements.
- Review and handle any potential security implications, such as secure file transfer methods and proper authentication mechanisms.

By following these instructions, you can set up and manage an Azure infrastructure with autoscaling VMs and an Azure Function to handle file operations between Blob Storage and VMs.
