# az_arm_checkov_tagging

## Overview

`az_arm_checkov_tagging` is a Terraform-based project aimed at managing and enforcing Azure resource tagging policies using Checkov, a static code analysis tool for infrastructure-as-code (IaC). This project helps ensure that all Azure resources are appropriately tagged, which is crucial for resource management, billing, and compliance.

## Features

- Enforces tagging policies on Azure resources.
- Integrates with Checkov for static code analysis.
- Provides detailed reports for resource tagging compliance.

## Repository Structure

- `.gitignore`: Specifies files and directories to be ignored by Git.
- `azure_arm_vnets_deploy.yml`: An Azure DevOps pipeline file to automate the ARM deployment and testing processes.
- `arm_vnets.json`: Defines Azure Virtual Networks with specific tagging for deployment via ARM.

## Prerequisites

- [Checkov](https://www.checkov.io/) installed.
- Azure CLI installed and configured.
- Azure DevOps project set up with the necessary service connections and secrets.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/shankdub/az_arm_checkov_tagging.git
   cd az_arm_checkov_tagging
   ```

2. Install Checkov:
   ```bash
   pip install checkov
   ```

## Usage

### Running Checkov

To scan the ARM code with Checkov:
```bash
checkov -f arm_vnets.json --output junitxml > checkov-arm-tagging-report.xml --external-checks-dir ./checkov
```

### Azure DevOps Pipeline

The repository includes an Azure DevOps pipeline (`terraform-scan-plan-apply.yml`) that automates the process of validating, planning, and applying Terraform configurations. This pipeline also includes steps for running Checkov to ensure compliance with tagging policies.

### Pipeline Configuration

#### Variables

The pipeline uses a variable group named `checkovtags`.
- `azureResourceManagerConnection`: The name of the Azure Resource Manager service connection in your Azure DevOps project.
- `subscriptionId`: The ID of your Azure subscription.
- `resourceGroupName`: The name of the Azure resource group where your resources will be deployed.
- `location`: The Azure region where your resources will be deployed.
- `templateLocation`: The location of the ARM template. 'Linked artifact' means that the ARM template is located in the build artifacts for this pipeline. This is the default.
- `csmFile`: The path to your ARM template file.
- `deploymentMode`: How Azure should handle existing resources during deployment. 'Incremental' mode means that Azure will leave existing resources that aren't specified in the template unchanged.
- `condition`: When the deployment should be performed. `succeeded()` means that the deployment will only be performed if all previous steps in the pipeline have succeeded.

#### Trigger

The pipeline is triggered on changes to the `main` branch excluding changes to pipeline yaml.

#### Steps
1. **Task** - UsePythonVerson@0
2. **Script**:
    - Install Checkov.
    - Run Checkov on ARM json.
    - Publish Checkov test results.

3. **Script**:
    - Install Checkov.
    - Initialize Terraform.
    - Plan Terraform deployment.
    - Convert the plan to JSON and run Checkov.
    - Publish Checkov test results.


## Configuration

### arm_vnets.json

Schema and parameters:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  }
```

Resources:
```json
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2017-06-01",
      "name": "checkov_vnet_1",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/20"
          ]
        }
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2017-06-01",
      "name": "checkov_vnet_2",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.16.0/20"
          ]
        }
      },
      "tags": {
        "Environment": "Development"
      }
    }
```

## Azure DevOps Pipeline Sequence Diagram

The following sequence diagram illustrates the Azure DevOps pipeline used in this project:

![Azure DevOps Pipeline](./docs/checkov-diagram-ados2.png)
## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a new branch: `git checkout -b my-feature-branch`
3. Make your changes and commit them: `git commit -m 'Add new feature'`
4. Push to the branch: `git push origin my-feature-branch`
5. Create a pull request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact

For any questions or suggestions, please open an issue or reach out to 
