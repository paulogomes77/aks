# Azure Kubernetes Service - AKS (em elaboração - 15-02-2024)

## Definição de variáveis

Primeiro, costumo gerar um ID para usar em todos os nomes relacionados com o cluster. Há quem use 8 carateres, penso que 6 bastam para não haver sobreposição com outros nomes no Azure. Com este identificador, também se consegue diferenciar este conjunto de Resources de outros que já tenhamos ou que venhamos a criar:
```
resourcesId=$(uuidgen | cut -c1-6)
echo "resourcesId: $resourcesId"
``` 
Definição de variáveis para a rede das VM (Kubernetes) acrescentand o UID gerado no passo anterior:
```
rgName="rg-kube-$resourcesId"
vnetName="vnet-kube-$resourcesId"
subnetName="subnet-kube-$resourcesId"
location="northeurope"
subscription="AzureSubscription1"
```

## Criar o grupo de recursos (Resource Group)
az group create \
  --name $rgName \
  --location $location \
  --subscription $subscription

## Criar a Vnet e Subnet
az network vnet create \
  --resource-group $rgName \
  --name $vnetName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name $subnetName \
  --subnet-prefix 10.0.0.0/24
  --location $location \
  --subscription $subscription




# 1ª Parte - Criação da infraestutura: VM, Rede, etc. (para outro turorial)
## Criar uma VM
vmName="kubecontrol01"
az vm create \
  --location $location \
  --subscription $subscription \
  --resource-group $rgName \
  --name "$vmName-$resourcesId" \
  --ssh-key-values $HOME/.ssh/id_rsa.pub \
  --admin-username paulo \
  --image Ubuntu2204 \
  --nsg-rule SSH \
  --size Standard_B1ms \
  --vnet-name $vnetName \
  --subnet $subnetName \
  --public-ip-address "" \
  --os-disk-delete-option delete \
  --os-disk-name $vmName-osDisk01-$resourcesId \
  --storage-sku Standard_LRS


  



# Parar uma VM e todas as dependências
az vm stop \
  --resource-group $rgName \
  --name kubecontrol01-2c25cc


# Kubernetes Cluster no Azure - The Hard way

**1ª Parte - Criação da infraestutura: VM, Rede, etc.**

Criar um UID para usar em todos os nomes relacionados com o cluster. Há quem use 8 carateres, penso que 6 bastam para não haver sobreposição com outros nomes no Azure. Com este identificador, também conseguimos diferenciar este conjunto de Resources de outros que já tenhamos ou que venhamos a criar.
`RESOURCE_UID=$(uuidgen | cut -c1-6)
echo "RESOURCE_UID: $RESOURCE_UID"`


**Definir variaveis para a rede das VM (Kubernetes). No caso do nome da VNET e do Resrouce Group, estes são completados com o UID gerado acima.**
VNET_NAME="vnet-kube-$RESOURCE_UID"
VNET_RG="rg-kube-$RESOURCE_UID"
VNET_REGION="westeurope"
VNET_SUBSCRIPTION="AzureSubscription1"


# Criar o Resource Group para a VNET
az group create \
  --name $VNET_RG \
  --location $VNET_REGION \
  --subscription $VNET_SUBSCRIPTION


# Criar a VNET para as VM que vão conter o Kubernetes
az network vnet create \
  --name $VNET_NAME \
  --resource-group $VNET_RG \
  --address-prefixes 10.0.0.0/24 \
  --location $VNET_REGION \
  --subscription $VNET_SUBSCRIPTION


# Criar as VM que serão os hosts do cluster Kubernetes (Master e Worker Nodes)

## Obter o VNET ID
VNET_ID=$(az network vnet show --resource-group $VNET_RG --name $VNET_NAME --query id -o tsv)

## Criar a VM
RESOURCE_UID=cebb0c
VM_NAME="kubecontrol01-$RESOURCE_UID"
VM_RG="rg-vm-kube-$RESOURCE_UID"
VM_REGION="westeurope"
VM_SUBSCRIPTION="AzureSubscription1"

az vm create \
  --location $VM_REGION \
  --subscription $VM_SUBSCRIPTION \
  --resource-group $VM_RG \
  --name $VM_NAME \
  --ssh-key-values $HOME/.ssh/id_rsa.pub \
  --admin-username paulo \
  --image Ubuntu2204 \
  --nsg-rule SSH \
  --size Standard_B1ms




# Alguns comandos úteis:
az account list
az account list --output table
az group create --location westeurope --resource-group rg-vm-kube-$RESOURCE_UID
az vm list-sizes --location eastus2 --output table
az vm image list --offer CentOS --publisher OpenLogic --all --output table
az vm list-sizes --location eastus2 --output table


# Criar a placa de rede
nicName="nic01-$resourcesId"
az network nic create --resource-group $rgName --name $nicName --vnet-name $vnetName --subnet $subnetName



Conectar ao AKS na Cloud SHell:
az aks get-credentials –resource-group (ou -g) DEVRG –name webapp-cluster

To get the required credentials to access your cluster, you need to type the
following command:
az aks get-credentials \
--resource-group rg-handsonaks \
--name handsonaks
