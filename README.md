# Azure MLOps (v2) Training

This project demonstrates an end-to-end MLOps implementation using Azure Machine Learning and Azure DevOps pipelines. It includes infrastructure setup, model training, and deployment pipelines for both batch and online endpoints.

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
  - Build pipeline by navigating to `ml-pipelines` and run ml-pipelines\sdk\train-sdkv2.ipynb
  - Review components of the pipeline and fill in missing parts and references 
  - Once pipeline is run, Model will be trained and registered in the WS registry

## Lab 2. Building a deployment pipeline, it could either a online endpoint or batch online. 
  - Deploy Model by navigating to `ml-pipelines` and running corresponding notebooks for batch deployment ml-pipelines\sdk\deploy-batch-endpoint-sdkv2.ipynb or online deployment ml-pipelines\sdk\deploy-online-endpoint-sdkv2.ipynb
  - In this lab, we also provided ci-cd folder to demonstrate Azure Pipeline or Github action orchestration of CI/CD pipeline. 
    - For GH Actions follow https://github.com/lenisha/mlops-v2-workshop/blob/main/ci-cd/README-GH.md

## Lab 3. CICD

#### 3.1 Set up Variable Groups
In Azure DevOps , Within **Project** Go to Library inside Pipelines. Create variable groups DevVars and ProdVars with the following variables:
- resource_group
- workspace_name
  
![image](https://github.com/user-attachments/assets/8d48f94c-2f3c-48cb-9668-b9b6284cc20b)

#### 3.2 Create Azure DevOps Pipelines
To set up the CI/CD pipelines in Azure DevOps:
1. In Azure DevOps, navigate to **Pipelines** in the left sidebar
2. Click on **New pipeline**
3. Select **Azure Repos Git** as your code repository source
4. Select your repository from the list
5. Choose **Existing Azure Pipelines YAML file**
6. Select the branch (typically `dev` or `main`)
7. In the path dropdown, select one of the following pipeline files:
   - `/devops-pipelines/deploy-model-training-pipeline.yml` (for model training)
   - `/devops-pipelines/deploy-batch-endpoint-pipeline.yml` (for batch endpoint)
   - `/devops-pipelines/deploy-online-endpoint-pipeline.yml` (for online endpoint)
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









