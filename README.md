## How to Run the Voting App Locally in Docker Desktop

Original documentation for this process here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app

1.  Clone repository to local computer with git clone
2.  Make sure you have Docker Desktop installed on your PC or Mac
3.  ***cd*** into aks_voting_app in your favorite command line interface
4.  Next step is running ***docker-compose up -d***
5.  After running the above command, 3 docker images will be downloaded on your PC or Mac.  It appears that the azure-vote-front uses a container image based on       Nginx that has Python Flask.  The backend container for this example uses Redis.  Run **docker images** in your local CLI to see the 3 different images that       were downloaded.
6.  The containers are now running in detached mode in Docker and you can verify they're running with ***docker ps***
7.  Also make sure app is working by going to http://localhost:8080
8.  The app has now been verified locally and now we can upload the containers for our app into the Azure Container Registry.
9.  Before starting the step above we should bring down our app that's running locally with this command: ***docker-compose down***

## How to Upload the Voting App Container Images to ACR

Original documentation for this process here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-acr

1.  The Azure CLI is necessary for working with ACR, so be sure to install it.
2.  First step is to log onto your Azure tenant with ***az login***
3.  Run ***az account list*** to see your available subscriptions
4.  Run ***az account set --subscription [subscription name]*** to switch to the subscription you would like to create the new resources in.
5.  Create a resource group running a command like this: ***az group create --name AKSVotingApp --location westus2***
6.  Create an Azure Container Registry (ACR) instance using the basic SKU: ***az acr create --resource-group AKSVotingApp --name [acrName] --sku Basic***




---
page_type: sample
languages:
  - python
products:
  - azure
  - azure-redis-cache
description: "This sample creates a multi-container application in an Azure Kubernetes Service (AKS) cluster."
---

# Azure Voting App

This sample creates a multi-container application in an Azure Kubernetes Service (AKS) cluster. The application interface has been built using Python / Flask. The data component is using Redis.

To walk through a quick deployment of this application, see the AKS [quick start](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough?WT.mc_id=none-github-nepeters).

To walk through a complete experience where this code is packaged into container images, uploaded to Azure Container Registry, and then run in and AKS cluster, see the [AKS tutorials](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app?WT.mc_id=none-github-nepeters).

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.


