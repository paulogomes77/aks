# Azure Kubernetes Service - AKS (em elaboração - 15-02-2024)

## Definição de variáveis

Primeiro, costumo gerar um ID para usar em todos os nomes relacionados com o cluster. Há quem use 8 carateres, penso que 6 bastam para não haver sobreposição com outros nomes no Azure. Com este identificador, também se consegue diferenciar este conjunto de Resources de outros que já tenhamos ou que venhamos a criar:
```
resourcesId=$(uuidgen | cut -c1-6)
echo "resourcesId: $resourcesId"
``` 
Definição de variáveis para a rede das VM (Kubernetes) acrescentando o UID gerado no passo anterior:
```
rgName="rg-aks-$resourcesId"
vnetName="vnet-aks-$resourcesId"
subnetName="subnet-aks-$resourcesId"
location="northeurope"
subscription="AzureSubscription1"

vnetId=$(az network vnet show --resource-group $rgName --name $vnetName --query id -o tsv)
subnetId=$(az network vnet subnet show --resource-group $rgName --vnet-name $vnetNameE --name $subnetName --query id -o tsv)
```

## Criar o grupo de recursos (Resource Group)
```
az group create \
  --name $rgName \
  --location $location \
  --subscription $subscription
```

## Criar a Vnet e Subnet
Uma VNet é uma rede para conectar resources, neste caso vamos criar uma rede com o cidr 10.0.0.0/16. No Azure um VNET normalmente tem pelo menos uma subnet, que é uma subdivisão da VNet. Em analogia, podemos pensar num prédio como sendo a VNet e os apartamentos são as Subnet. Neste caso vamos criar uma subnet com cidr 10.0.0.0/24.
```
az network vnet create \
  --resource-group $rgName \
  --name $vnetName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name $subnetName \
  --subnet-prefix 10.0.0.0/24
  --location $location \
  --subscription $subscription
```

## Interagir com o cluster
```
# Instalar o Kubectl:
az aks install-cli

# Obter acesso para usar o kubectl:
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# Testar visualizando os nós:
kubectl get nodes
```

## Outros comandos
```
# Semelhante a um "kubectl get nodes" mas com o az cli:
az aks machine list --resource-group test-rg  --cluster-name aks-cluster --nodepool-name nodepool1 -o table

# Obter acesso para usar o kubectl:
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# Eliminar o RG e seu conteúdo:
az group delete --name myResourceGroup --yes --no-wait

```

## Utilitários para debud
```
# Criar um pod genérico com debian, por exemplo:
kubectl run pod-teste01 --image=debian sleep infinity

# Entrar no pod:
kubectl exec -it pod-teste01 -- /bin/bash

# Instalar ifconfig, ssh, ping:
apt install net-tools ssh inetutils-ping

```
