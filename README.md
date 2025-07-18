# youtube-clone
Deploying a YouTube-clone app using Azure DevOps


# Azure DevOps Build Pipeline - Build and Deploy a YouTube Clone 


## Steps to set the infrastructure
- Login to VSCode or any other IDE of your choice
- Run the below commands to download the application code
  ```
  mkdir youtube-clone; cd youtube-clone
  git init
  git clone https://github.com/piyushsachdeva/Youtube_Clone
  ```
- Create a project in Azure DevOps and push the code by running the below commands on VSCode:
  ```
  git remote add origin $YOURAZUREREPO
  git push -u origin all
  ```
  Note: Make sure to update your Azure repo in the above command. Make sure you are in the correct directory before running the above commands else, the push to Azure DevOps may fail.

- Go to the Azure Portal and Create the Azure App Service.

- Implement the build pipeline using the classic editor. Not recommended in industry. Better way is to use   YAML files for pipeline.

- Understand the use of service connection and service principal


**Note: You must set the app settings WEBSITE_DYNAMIC_CACHE=0 and WEBSITE_LOCAL_CACHE_OPTION=Never to disable all file caching**


## Structure of Azure DevOps build Pipeline


*  A trigger tells a Pipeline to run. It could be CI or Scheduled, manual(if not specified), or after another build finishes.
*  A pipeline is made up of one or more stages. A pipeline can deploy to one or more environments.
*  A stage organizes jobs in a pipeline, and each stage can have one or more jobs.
*  Each job runs on one agent, such as Ubuntu, Windows, macOS, etc. A job can also be agentless.
*  Each agent runs a job that contains one or more steps.
*  A step can be a task or script and is the smallest building block of a pipeline.
*  A task is a pre-packaged script that performs an action, such as invoking a REST API or publishing a build artifact.
*  An artifact is a collection of files or packages published by a run.

## Pipeline code used in the demo

``` YAML
trigger: 
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: Npm@1
      inputs:
        command: 'install'
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build'

    
    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: 'build'
        ArtifactName: 'drop'
        publishLocation: 'Container'

- stage: Deploy 
  jobs:
  - job: Deploy
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: DownloadBuildArtifacts@1
      inputs:
        buildType: 'current'
        downloadType: 'single'
        artifactName: 'drop'
        downloadPath: '$(System.ArtifactsDirectory)'
    - task: AzureRmWebAppDeployment@4
      inputs:
        ConnectionType: 'AzureRM'
        azureSubscription: 'Azure subscription 1 (0b8744a6-e52c-4671-8afc-b21956415ad4)'
        appType: 'webAppLinux'
        WebAppName: 'youtube-clone1'
        packageForLinux: '$(System.ArtifactsDirectory)/drop'
        RuntimeStack: 'STATICSITE|1.0'
```
Successful pipeline in Azure Devops Portal
![Screenshot 2025-06-28 191449](https://github.com/user-attachments/assets/1e96464b-7da8-41d8-8c93-4290ea679f72)

Snapshot of my App service in Azure Portal-
![image](https://github.com/user-attachments/assets/dc490166-aa9f-4840-8bc2-1dfaf69469bf)

Output: Deployed the YouTube-Clone Successfully
![Screenshot 2025-06-28 191614](https://github.com/user-attachments/assets/f75aa42a-38a3-4529-91e2-28deb3829abf)


