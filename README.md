# Azure MLOps (v2) Training

This repo is practice repo for Community IP MLOps In a Day workshop. The goal is to have the participants to build a traing and a deployment pipeline
## Repo structure 

```bash
├── ci-cd - examples of using Azure DevOps Pipeline and Github Action to orchastrate CI CD pipelines
│   ├── azure-pipelines - code samples for using Azure DevOps Pipeline
│   │   ├── cli - cli code samples
│   │   ├── sdk - sdk code samples
│   ├── dev-requirements.txt - Conda requirement file
│   ├── setup-cli.sh - Command to prepare Azure Pipeline for Azure ML CLI
│   ├── setup-sdk.sh - Command to prepare Azure Pipeline for Azure ML SDK (Testing)

├── data
│   ├── taxi-data.csv - data used for training & testing ML model
│   ├── taxi-batch.csv - data used as input for testing batch endpoint
│   ├── taxi-request.json - data used as input for testing online endpoint

├── component - python scripts of ML workload
│   ├── prep
│   |   ├── prep.py - python script that reads raw data and prepare train, val and test datasets
│   |   ├── test_prep.py - python script used when unit testing `prep.py` 
│   ├── train
│   |   ├── train.py - python script that reads train data, trains and saves an ML model
│   |   ├── test_train.py - python script used when unit testing `train.py` 
│   ├── evaluate
│   |   ├── evaluate.py - python script that reads test data and trained model and evaluates model performance
│   |   ├── test_evaluate.py - python script used when unit testing `evaluate.py` 
│   ├── register
│   |   ├── register.py - python script that register trained model in AzureML Model Registry
│   |   ├── test_register.py - python script used when unit testing `register.py` 
├── environment - environments needed execute ML workload
│   ├── train-conda.yml - environment conda specification needed to execute python scripts in ML workload
├── components - AzureML Components definitions
│   ├── prep.yml - AzureML Component definition for `prep.py` 
│   ├── train.yml - AzureML Component definition for `train.py` 
│   ├── evaluate.yml - AzureML Component definition for `evaluate.py` 
│   ├── register.yml - AzureML Component definition for `register.py` 
├── ml-pipelines
│   ├── cli - CLI training/deployment pipeline exercise
│   │   ├── deploy - deploy pipeline assets
│   │   │   ├── batch - deploy batch pipeline assets
│   │   │   │   ├── batch-deployment.yml - deploy batch deployment template
│   │   │   │   ├── batch-endpoint.yml - deploy batch endpoint template
│   │   │   ├── online - deploy online pipeline assets
│   │   │   │   ├── online-deployment.yml - deploy online deployment template
│   │   │   │   ├── online-endpoint.yml - deploy online endpoint template
│   │   ├── train - training pipeline assets
│   │   |   ├── compute.yml - yaml configuration of AzureML training compute cluster
│   │   |   ├── data.yml - yaml configuration of AzureML training (input) data asset
│   │   |   ├── environment.yml - AzureML yaml configuration of training environment
│   │   |   ├── pipeline.yml - AzureML yaml configuration of training pipeline
│   │   |   ├── pipeline_automl.yml - AzureML yaml configuration of training pipeline
│   │   ├── azureml-cliv2.ipynb - CLI training/deployment pipeline notebook code sample
│   │   ├── train.sh - training pipeline CLI (Lab 01)
│   │   ├── deploy-batch-endpoint.ipynb - deploy batch pipeline notebook (Lab 02)
│   │   ├── deploy-online-endpoint.ipynb - deployment online pipeline notebook (Lab 02)
│   ├── sdk - SDK training/deployment pipeline exercise
│   │   ├── train-sdkv2.ipynb - training pipeline SDK (Lab 01)
│   │   ├── deploy-batch-endpoint-sdkv2.ipynb - deploy batch pipeline notebook (Lab 02)
│   │   ├── deploy-online-endpoint0sdkv2.ipynb - deployment online pipeline notebook (Lab 02)
```

## MLOps v2 Lab

In this workshop, we provided both CLI and SDK exercise. You can choose your preferred option. The exercise starts from ml-pipeline folder. 

There are 2 labs in this workshop:

## Lab 1. Building a training pipeline. It can be a custom model pipeline or automl pipeline.
  - Experiment with notebooks in `notebooks` folder to explore data and model 
  - Build pipeline by navigating to `ml-pipelines` cli or sdk and running corresponding notebooks
  - Review components of the pipeline and fill in missing parts and references 
  - Once pipeline is run, Model will be trained and registered in the WS registry

## Lab 2. Building a deployment pipeline, it could either a online endpoint or batch online. 
  - Deploy Model by navigating to `ml-pipelines` cli or sdk and running corresponding notebooks
  - In this lab, we also provided ci-cd folder to demonstrate Azure Pipeline or Github action orchestration of CI/CD pipeline. 
    - For GH Actions follow https://github.com/lenisha/mlops-v2-workshop/blob/main/ci-cd/README-GH.md

## Lab 3. CICD
# GasUni MLOps v2 Project

This project demonstrates an end-to-end MLOps implementation using Azure Machine Learning and Azure DevOps pipelines. It includes infrastructure setup, model training, and deployment pipelines for both batch and online endpoints.

## Project Structure

```
├── config-infra-dev.yml         # Infrastructure configuration for dev environment
├── config-infra-prod.yml        # Infrastructure configuration for prod environment
├── deploy-azure-resources.ps1   # Script to deploy Azure resources
├── environment.yml              # Conda environment file
├── requirements.txt             # Python package requirements
├── data/                        # Sample data for model training and testing
├── data-science/                # Data science code (prep, train, evaluate, register)
└── mlops/                       # MLOps code for Azure ML and DevOps pipelines
```

## Getting Started

### Prerequisites

- Azure subscription
- Azure DevOps organization and project
- Git
- Python 3.8+
- PowerShell

## Setup Instructions

### 1. Local Environment Setup

```powershell
# Create and activate a virtual environment
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install required packages
pip install -r requirements.txt
```

### 2. Azure Setup

#### 2.1 Authenticate with Azure

```powershell
# Log in to Azure CLI
az login
az account set --subscription <your-subscription-id>
```

#### 2.2 Configure Infrastructure Files (if needed)

Review and update the config files (config-infra-dev.yml and config-infra-prod.yml) if needed. Default settings are:

```yaml
# Default settings
ap_vm_image: ubuntu-20.04
namespace: gasunimlopsv2
postfix: 0001
location: eastus
enable_aml_computecluster: true
enable_monitoring: false
```

#### 2.3 Deploy Azure Resources

```powershell
# Deploy production environment
.\deploy-azure-resources.ps1 -Environment prod

# Or deploy both dev and prod environments
.\deploy-azure-resources.ps1 -Environment both
```

#### 2.4 Configure Azure ML Workspace Permissions

After deployment, set up appropriate role assignments in your Azure ML workspaces:

1. Navigate to your Azure ML workspace in the Azure portal
2. Go to Access control (IAM)
3. Assign the following roles to your user/service principal:
   - **AzureML Data Scientist**
   - **AzureML Compute Operator**

> **Note:** For more information on Azure ML roles, see [Manage roles in your workspace](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-assign-roles)

#### 2.5 Configure Container Registry and Storage Account

1. **Container Registry:** Grant ACRPull role to your compute resources
   Inside your AML Workspace, go to compute and confidure a system assigned managed identity for your compute and grant it the ACRPull role in your compute resource.
   ![image](https://github.com/user-attachments/assets/87934887-8ec2-417f-a95f-fddac308f14c)

3. **Storage Account:** Configure appropriate access settings (for this POC set everything to public)
   ![image](https://github.com/user-attachments/assets/294486db-0e0d-4524-9054-b29ea8b8062d)

   Make sure that storage account key access is enabled!!
   ![image](https://github.com/user-attachments/assets/1717882a-ff24-4aa4-93e9-465d860b59ce)


### 3. Azure DevOps Setup

#### 3.1 Set up Azure DevOps Repository

1. Create an organization in Azure DevOps and add a project
2. Initialize a Git repository and clone it to your local machine
3. Add this code to your repository:

```powershell
# Initialize git repository (if not already done)
git init
git add .
git commit -m "Initial commit"

# Create and switch to dev branch
git checkout -b dev

# Push to remote repository (replace with your repository URL)
git remote add origin <your-repository-url>
git push -u origin dev
```

#### 3.2 Configure Azure DevOps Service Connections

1. In Azure DevOps, go to **Project Settings > Service connections**
2. Create service connections for both environments:
   - Resource Group service connections (dev and prod)
   - Azure ML Workspace service connections (dev and prod)
3. Use the following naming convention (matching the config files):
   - `gasuni-mlopsv2-rg-sc-dev`
   - `gasuni-mlopsv2-aml-sc-dev`
   - `gasuni-mlopsv2-rg-sc-prod`
   - `gasuni-mlopsv2-aml-sc-prod`

![image](https://github.com/user-attachments/assets/f6fe84b7-9af5-4e3e-8761-efd9c74f2c2f)

#### 3.3 Set up Variable Groups

Go to Library inside Pipelines. Create variable groups DevVars and ProdVars with the following variables:
- resource_group
- workspace_name
  
![image](https://github.com/user-attachments/assets/8d48f94c-2f3c-48cb-9668-b9b6284cc20b)

#### 3.4 Create Azure DevOps Pipelines

To set up the CI/CD pipelines in Azure DevOps:

1. In Azure DevOps, navigate to **Pipelines** in the left sidebar
2. Click on **New pipeline**
3. Select **Azure Repos Git** as your code repository source
4. Select your repository from the list
5. Choose **Existing Azure Pipelines YAML file**
6. Select the branch (typically `dev` or `main`)
7. In the path dropdown, select one of the following pipeline files:
   - `/mlops/devops-pipelines/deploy-model-training-pipeline.yml` (for model training)
   - `/mlops/devops-pipelines/deploy-batch-endpoint-pipeline.yml` (for batch endpoint)
   - `/mlops/devops-pipelines/deploy-online-endpoint-pipeline.yml` (for online endpoint)
8. Click **Continue**
9. Review the pipeline YAML file
10. Click **Run** to save and run the pipeline, or **Save** to just save it

Repeat these steps for each pipeline you want to create. You'll typically want to set up the model training pipeline first, followed by the endpoint deployment pipelines.

#### 3.3 Set up Pipeline Variable

Edit the pipeline you just created. Add the following variables to the pipeline:
- subscription_id
- tenant_id
- location

![image](https://github.com/user-attachments/assets/060b2a00-9be2-4ec4-bfec-48436c050fe6)


## CI/CD Pipeline Workflow

### Development Workflow

1. Create feature branches from `dev` branch
2. Make changes and push to the feature branch
3. Create a pull request to merge into `dev`
4. After PR approval, merge to `dev` which triggers the dev pipeline
5. Resources are deployed to the dev environment

### Production Deployment

1. Create a pull request from `dev` to `main`
2. After thorough testing and PR approval, merge to `main`
3. This triggers the production pipeline and deploys to the production environment

## Common Issues and Troubleshooting

### Self-hosted Agent Requirements

You may need to set up a self-hosted agent for your Azure DevOps pipelines. Follow these steps:

1. Go to **Project Settings > Agent pools > Add pool**
2. Select "Self-hosted" and create a new pool
3. Download and configure the agent on your machine
4. Update your pipeline YAML files to use this agent pool







