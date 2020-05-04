------------------------------------------------------------------------------------
# Deploy-ElasticSearch-on-Azure-Kubernetes-with-Beats
------------------------------------------------------------------------------------

# Kuberntes cluster by using AKS Engine 
------------------------------------------------------------------------------------
The AKS engine provides a command-line tool to bootstrap Kubernetes clusters on Azure and Azure Stack Hub. By using the Azure Resource Manager, the AKS engine helps you create and maintain clusters running on VMs, virtual networks and other infrastructure-as-a-service.The input to aks-engine is a cluster definition file which describes the desired cluster, including orchestrator, features, and agents. The structure of the input files is very similar to the public API for Azure Kubernetes Service.


## About The Aks Cluster Deploy by using Aks-engine
------------------------------------------------------------------------------------

Aks-engine reads a cluster definition which describes the size, shape, and configuration of your cluster. This guide takes the default configuration of one master and two Linux agents. If you would like to change the configuration, edit kubernetes.json before continuing.

## Getting Started

Purpose of this document is to deploy Aks Cluster on azure Cloud and by using the aks-engine utility and we can deploy application on the k8s cluster .

# Goals

* Deploy Aks-engine on Linux & Windows
* Deploy Aks Cluster (Self Managed Cluster) on VM 
* Deploy Application on Aks Cluster
* Deploy Elasticsearch Cluster on Aks with beats 

### Prerequisites Installation

* Run a server with Ubuntu 16.04/18.04 ( Please login as root user )
* Aks Engine version >= v0.43.0
* Kubernetes Version >= v1.13.12
* Required azure subscription id, clientid, client-secret.


------------------------------------------------------------------------------------
### Install the AKS engine and deploy a Kubernetes cluster
------------------------------------------------------------------------------------
1.  mkdir aks_engine

2.  cd aks_engine

3. Login on Ubuntu Server & Install the AKS engine

   curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh

4. Change the permission  

   chmod +x get-akse.sh 

5. Run the script to insatll the aks-engine utility 

   ./ get-akse.sh

6.  Run ssh-keygen -f akskey


------------------------------------------------------------------------------------
5- Install azure-cli
----------------------------------------------------------------------------------
sudo su
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
apt-key adv --keyserver packages.microsoft.com --recv-keys 417A0893
apt-get install -y apt-transport-https
apt-get update
apt-get install -y azure-cli

or
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash


----------------------------------------------------------------------------------
6 -Azure App Registration
----------------------------------------------------------------------------------
 Register a App on Azure Portal "https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app"
 Api Access to App
-Azure Active Directory Graph
-Azure Service Management 

------------------------------------------------------------------------------------------------
7- Create a Service Principle
-------------------------------------------------------------------------------------------------
1. Login on Azure using AD access
az login

2. Set the subscription id in command line server 
az account set --subscription XXXX-YYYY-ZZZZ

3. Create Service Principal 
az ad sp create-for-rbac -n RBAC_NAME --role="Contributor" --scopes="/subscriptions/{subs-id}"

------------------------------------------------------------------------------------------------
### Provision K8s Cluster by using aks-engine utility and deploy application on k8s cluster
------------------------------------------------------------------------------------------------

1. Create the resource group

az group create --name testing-RG --location East US

2. Run the below command to Deploy K8s Cluster

aks-engine deploy --subscription-id XXXX-YYYY-ZZZZ \
    --dns-prefix ssidepgroup \
    --resource-group XXXX \
	--api-model kubernetes.json-updated \
    --location  ukwest \
    --client-id XXXX-YYYY-ZZZZ \
    --client-secret XXXX-YYYY-ZZZZ \
    --set servicePrincipalProfile.clientId="XXXX-YYYY-ZZZZ" \
    --set servicePrincipalProfile.secret="XXXX-YYYY-ZZZZ"

---------------------------------------------------------------------------------------------------
### Verify your cluster configuration 
---------------------------------------------------------------------------------------------------

kubectl cluster-info

kubectl get node
