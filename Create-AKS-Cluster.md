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



