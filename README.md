🚀 Deploy Terraform Files using Azure DevOps

## Project Overview
In this project demonstrates we will implement a continuous integration and continuous deployment (CI/CD) pipeline using Azure DevOps and Terraform. This pipeline automates the deployment of infrastructure, making it consistent and repeatable.

## Prerequisites

- **Azure DevOps Account:** Ensure you have an Azure DevOps organization set up.
- **Azure Subscription:** Required for deploying the application to Azure App Service.
- **IDE:** Use Visual Studio Code (VSCode) or any other preferred IDE.
- **Git:** Ensure Git is installed and configured.

## Steps to Set Up the Infrastructure

### 1. Write the infrastructure code in visual studio or download the files from my repository.

  ```
  mkdir terraform_project; cd terraform_project
  git init
  git clone (https://github.com/nikhilgthakur/Terraform_Azure_DevOps_CICD_Pipeline.git)
  ```

### 2. Push Code to Azure DevOps
Create a new project in Azure DevOps for this build pipeline and push the code using the following commands:

  ```
  git remote add origin $YOURAZUREREPO
  git push -u origin all
  ```

### 3. Implement the Build Pipeline
- Use the Azure DevOps pipeline the build pipeline.
- Set up service connections and configure service principals as needed.

## Pipeline Code

Below is the pipeline YAML used in the project:

```yaml
trigger: 
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: TerraformTaskV4@4
      displayName: Tf init
      inputs:
        provider: 'azurerm'
        command: 'init'
        backendServiceArm: 'Pay-As-You-Go(a859831e-dbb1-4d73-8b50-a82ac0fcc7d5)'
        backendAzureRmResourceGroupName: 'Nikhil_Project'
        backendAzureRmStorageAccountName: 'nikhilteststorage'
        backendAzureRmContainerName: 'prod-tfstate'
        backendAzureRmKey: 'prod.terraform.tfstate'
    - task: TerraformTaskV4@4
      displayName: Tf Validate
      inputs:
        provider: 'azurerm'
        command: 'validate'
    - task: TerraformTaskV4@4
      displayName: Tf Format
      inputs:
        provider: 'azurerm'
        command: 'custom'
        outputTo: 'console'
        customCommand: 'fmt'
        environmentServiceNameAzureRM: 'Pay-As-You-Go(a859831e-dbb1-4d73-8b50-a82ac0fcc7d5)'
    - task: TerraformTaskV4@4
      displayName: Tf Plan
      inputs:
        provider: 'azurerm'
        command: 'plan'
        commandOptions: '-out $(Build.SourcesDirectory)/tfplanfile'
        environmentServiceNameAzureRM: 'Pay-As-You-Go(a859831e-dbb1-4d73-8b50-a82ac0fcc7d5)'
    - task: ArchiveFiles@2
      displayName: Archive files
      inputs:
        rootFolderOrFile: '$(Build.SourcesDirectory)/'
        includeRootFolder: false
        archiveType: 'zip'
        archiveFile: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
        replaceExistingArchive: true
    - task: PublishBuildArtifacts@1
      displayName: Publish Artifact
      inputs:
        PathtoPublish: '$(System.DefaultWorkingDirectory)'
        ArtifactName: '$(Build.BuildId)-build'
        publishLocation: 'Container'
```


<img width="748" alt="image" src="https://github.com/user-attachments/assets/8f18ee0f-0961-45cc-a4de-d5fc168e0462" />



<img width="958" alt="image" src="https://github.com/user-attachments/assets/ae2f3736-ef6a-41cf-a593-3346689acaa7" />


### 4. Implement the Release Pipeline

Add artifact


<img width="743" alt="image" src="https://github.com/user-attachments/assets/18163127-4c86-4027-89c4-a94c3a143379" />


Enable continuous deployment trigger


<img width="739" alt="image" src="https://github.com/user-attachments/assets/f7cd35d6-5b0c-464b-9ec9-2be4f06cd6b2" />


Add stage for deployment tasks


<img width="744" alt="image" src="https://github.com/user-attachments/assets/3e05e1ac-b3bf-45d6-a781-ca27668df0f0" />


<img width="746" alt="image" src="https://github.com/user-attachments/assets/0c6142ca-c4c0-40ab-aff9-84fa4a24a7fe" />


<img width="726" alt="image" src="https://github.com/user-attachments/assets/e9f9723e-dd21-4260-8527-0f8787003a8e" />


![screencapture-dev-azure-nikhilgtcloud-Terraform-releaseDefinition-2025-01-29-10_42_08](https://github.com/user-attachments/assets/cc15645c-3ec6-4bd7-acb4-509ba2473986)


![screencapture-dev-azure-nikhilgtcloud-Terraform-releaseDefinition-2025-01-29-10_46_48](https://github.com/user-attachments/assets/987e6ca2-3963-424f-8c22-fd7c5e5deacd)


Add pre-deployment condition


<img width="683" alt="image" src="https://github.com/user-attachments/assets/f1edad36-45f8-4530-a653-c9875490b3af" />


Add another stage for destroy tasks


![screencapture-dev-azure-nikhilgtcloud-Terraform-releaseDefinition-2025-01-29-10_51_22](https://github.com/user-attachments/assets/d6e77de3-5510-454c-81e4-c5753ed5d024)


Note: Except for the terraform apply task, all tasks will be the same as the deployment stage.


Add pre-deployment condition for destroy stage


<img width="718" alt="image" src="https://github.com/user-attachments/assets/28ddf538-da37-4952-9a5e-adde799d4ac0" />


Final release pipeline should look like this:


<img width="698" alt="image" src="https://github.com/user-attachments/assets/ccf04d40-e692-4e2d-b73e-2e7fdab33640" />


### 5. Infrastructure created successfully in Azure


After any commit in the repository, the build and release pipelines will trigger. Once approved, the destroy stage will run.


<img width="668" alt="image" src="https://github.com/user-attachments/assets/8f47e76a-28cb-488d-a197-ca859c286de7" />


<img width="698" alt="image" src="https://github.com/user-attachments/assets/5953fea3-5c6f-41be-a6cb-1048a0762d63" />


<img width="694" alt="image" src="https://github.com/user-attachments/assets/f0150c1f-8451-4918-88b2-27093f2eae8a" />
