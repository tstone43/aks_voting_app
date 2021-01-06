## How to Run the Voting App Locally in Docker Desktop

Original documentation for this process is here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app

1.  Clone repository to local computer with git clone
2.  Make sure you have Docker Desktop installed on your PC or Mac
3.  ***cd*** into aks_voting_app in your favorite command line interface
4.  Next step is running ***docker-compose up -d***
5.  After running the above command, 3 docker images will be downloaded on your PC or Mac.  It appears that the azure-vote-front uses a container image based on       Nginx that has Python Flask.  The backend container for this example uses Redis.  Run **docker images** in your local CLI to see the 3 different images that       were downloaded.
6.  The containers are now running in detached mode in Docker and you can verify they're running with ***docker ps***
7.  Also make sure app is working by going to http://localhost:8080
8.  The app has now been verified locally and now we can upload the containers for our app into the Azure Container Registry.
9.  Before starting the step above we should bring down our app that's running locally with this command: ***docker-compose down***

## How to Upload the Voting App Container Image to ACR

Original documentation for this process is here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-acr

1.  The Azure CLI is necessary for working with ACR, so be sure to install it.
2.  First step is to log onto your Azure tenant with ***az login***
3.  Run ***az account list*** to see your available subscriptions
4.  Run ***az account set --subscription [subscription name]*** to switch to the subscription you would like to create the new resources in.
5.  Create a resource group running a command like this: ***az group create --name AKSVotingApp --location westus2***
6.  Create an Azure Container Registry (ACR) instance using the basic SKU and make sure to use a globally unique name for the ACR: ***az acr create --resource-       group AKSVotingApp --name [acrName] --sku Basic***
7.  Next we log into container registry that we just created using the unique name we setup in previous step: ***az acr login --name [acrName]***
8.  Verify again the docker images you have running locally with ***docker images*** command since we will be working with these images shortly.
9.  Next we need to obtain the login server address for our ACR instance, so we can upload container images to this instance.  Here is the command to retrieve the     address: ***az acr list --resource-group AKSVotingApp --query "[].{acrLoginServer:loginServer}" --output table***
10. Next we're tagging the azure-vote-front image with the ACR Login Address.  Add v1 for the version like shown in this example: 
    ***docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 [acrLoginServer]/azure-vote-front:v1***
11. To verify that you have the azure-vote-front image with the login server address for ACR run: ***docker images***
12. Next we push the image we just tagged to ACR.  You do this with this command: ***docker push [acrLoginServer]/azure-vote-front:v1***
13. To verify the azure-vote-front image is in ACR run this command: ***az acr repository list --name [acrName] --output table***
14. If you want to see specific tags for your container images in ACR run this command: ***az acr repository show-tags --name [acrName] --repository azure-vote-       front --output table***

## How to Deploy an AKS Cluster

Original documentation for this process is here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-cluster

1.  We're now ready to create the AKS cluster by running this command: ***az aks create --resource-group AKSVotingApp --name [aksClusterName] --node-count 2 --       generate-ssh-keys --attach-acr [acrName]***
2.  After the cluster is deployed we now need to install kubectl with this command ***az aks install-cli***
3.  We can now connect to the AKS cluster with this command: ***az aks get-credentials --resource-group AKSVotingApp --name [aksClusterName]***
4.  Verify that your 2 nodes are running in AKS with this command: ***kubectl get nodes***

## How to Deploy an App to your AKS Cluster

Original documentation for this process is here: https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-application

1.  We need to update the Kubernetes manifest file, azure-vote-all-in-one-redis.yaml, to include the container image we have uploaded in ACR.  Make sure
    you are in the directory with the source files for this repo.  Run ***vi azure-vote-all-ine-one-redis.yaml***
2.  In the VI editor we now want to replace the line with **image:** for azure-vote-front with our path to our uploaded container 
    ***image:[acrName.azurecr.io/azure-vote-front:v1]***
3.  To deploy the app to our AKS Cluster we're now running this: ***kubectl apply -f azure-vote-all-in-one-redis.yaml***
4.  If you want to monitor the progress of the app coming up you can run this: ***kubectl get service azure-vote-front --watch***
5.  Eventually you should see the public IP address for your new app in the output, should be under **EXTERNAL-IP**.  HTTP to the public address and you should       reach the application.
6.  I ended up entering the name of the ACR incorrectly in the manifest file.  These commands helped me work through the issue: 
    - ***kubectl get pods*** (helped me determine name of pod that was incorrectly deployed)
    - ***kubectl describe pod [pod name from previous command]*** (helped me see I had the ACR name configured incorrectly)
    - ***kubectl delete -f azure-vote-all-in-one-redis.yaml*** (removed the resources I created with kubectl apply -f)
7.  After updating the ACR with the correct name in the manifest file, and rerunning kubectl apply -f azure-vote-all-in-one-redis.yaml the Azure Voting app was       now accessible.






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


