---
title: 使用现有的 Azure 应用程序网关为现有的 AKS 群集启用入口控制器外接程序
description: 本教程介绍如何使用现有的应用程序网关为现有的 AKS 群集启用入口控制器外接程序
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 6cbfac4794a685e5858e689c20d6603807edcedf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987984"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>教程：使用现有应用程序网关通过 Azure CLI （预览版）为现有 AKS 群集启用应用程序网关入口控制器外接程序

你可以使用 Azure CLI 为[Azure Kubernetes Services （AKS）](https://azure.microsoft.com/services/kubernetes-service/)群集启用当前为预览版的[应用程序网关入口控制器（AGIC）](ingress-controller-overview.md)加载项。 在本教程中，你将了解如何使用 AGIC 外接程序通过部署在不同虚拟网络中的现有应用程序网关，在现有 AKS 群集中公开 Kubernetes 应用程序。 首先，在一个虚拟网络中创建 AKS 群集，在单独的虚拟网络中创建一个应用程序网关，以模拟现有资源。 然后，将启用 AGIC 外接程序，将两个虚拟网络对等互连，并部署一个示例应用程序，该应用程序将使用 AGIC 外接程序通过应用程序网关公开。 如果要为现有应用程序网关和同一虚拟网络中的现有 AKS 群集启用 AGIC 外接程序，则可以跳过下面的对等互连步骤。 外接程序提供了一种更快的方法来部署 AKS 群集的 AGIC，而不是[以前的 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) ，还提供完全托管的体验。  

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建资源组 
> * 创建新的 AKS 群集 
> * 创建新的应用程序网关 
> * 使用现有的应用程序网关在现有的 AKS 群集中启用 AGIC 外接程序 
> * 将应用程序网关虚拟网络与 AKS 群集虚拟网络对等
> * 使用 AGIC 在 AKS 群集上部署用于入口的示例应用程序
> * 检查是否可以通过应用程序网关访问应用程序

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

使用[az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register)命令注册*AKS-IngressApplicationGatewayAddon*功能标志，如以下示例中所示：只需对每个订阅执行此操作一次，同时外接程序仍处于预览阶段：
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

状态显示为 "已注册" 可能需要几分钟时间。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令检查注册状态：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

请确保安装/更新本教程的 aks-preview 扩展;使用以下 Azure CLI 命令
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 以下示例在*canadacentral*位置（区域）中创建名为*myResourceGroup*的资源组。 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>部署新的 AKS 群集

现在，你将部署新的 AKS 群集，以模拟要为其启用 AGIC 外接程序的现有 AKS 群集。  

在以下示例中，将使用[AZURE CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking)和创建的资源组*MyResourceGroup*中的[托管标识](https://docs.microsoft.com/azure/aks/use-managed-identity)部署名为*myCluster*的新 AKS 群集。    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

若要配置命令的其他参数 `az aks create` ，请访问[此处](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)的引用。 

## <a name="deploy-a-new-application-gateway"></a>部署新的应用程序网关 

现在，你将部署新的应用程序网关，以模拟要用于对流量进行负载均衡的现有应用程序网关， *myCluster*。 应用程序网关的名称将为*myApplicationGateway*，但你需要首先创建一个名为*MYPUBLICIP*的公共 IP 资源，以及一个名为*myVnet*且地址空间为 11.0.0.0/8 的新虚拟网络，以及一个名为 11.1.0.0/16 的子*网，并*使用*mySubnet*在*mySubnet*中部署应用程序网关。 

在单独的虚拟网络中使用 AKS 群集和应用程序网关时，两个虚拟网络的地址空间不得重叠。 AKS 群集部署的默认地址空间为 10.0.0.0/8，因此，我们将应用程序网关虚拟网络地址前缀设置为 11.0.0.0/8。 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 应用程序网关入口控制器（AGIC）加载项**仅**支持应用程序网关 v2 Sku （STANDARD 和 WAF），而**不**支持应用程序网关 v1 sku。 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>使用现有的应用程序网关在现有的 AKS 群集中启用 AGIC 外接程序 

现在，你将在创建的 AKS 群集*myCluster*中启用 AGIC 外接程序，并指定 AGIC 外接程序以使用创建的现有应用程序网关*myApplicationGateway*。 请确保已在本教程开头添加/更新 aks-preview 扩展。 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>同时将两个虚拟网络对等互连

由于我们将 AKS 群集部署在其自己的虚拟网络中，并将应用程序网关部署在另一个虚拟网络中，因此需要将这两个虚拟网络一起建立在一起，以便流量从应用程序网关流向群集中的 pod。 对等互连两个虚拟网络需要同时运行 Azure CLI 命令两次，以确保连接是双向的。 第一个命令将创建从应用程序网关虚拟网络到 AKS 虚拟网络的对等互连。第二个命令将按其他方向创建对等互连连接。 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>使用 AGIC 部署示例应用程序 

现在，将示例应用程序部署到创建的 AKS 群集，该群集将使用入口的 AGIC 外接程序，并将应用程序网关连接到 AKS 群集。 首先，你将获得通过运行命令部署的 AKS 群集的凭据 `az aks get-credentials` 。 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

获取所创建的群集的凭据后，运行以下命令以设置一个示例应用程序，该应用程序使用 AGIC 来进入群集。 AGIC 会将之前设置的应用程序网关更新为部署的新示例应用程序。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>检查是否可访问应用程序

现在，已将应用程序网关设置为向 AKS 群集提供流量，接下来我们验证你的应用程序是否可访问。 首先获取入口的 IP 地址。 

```azurecli-interactive
kubectl get ingress
```

通过访问运行上述命令的应用程序网关的 IP 地址或使用进行检查，检查创建的示例应用程序是否已启动并正在运行 `curl` 。 它可能会让应用程序网关一分钟的时间来获取更新，因此，如果应用程序网关仍处于门户上的 "正在更新" 状态，则在尝试访问 IP 地址之前，请先完成该操作。 

## <a name="clean-up-resources"></a>清理资源

当不再需要资源组、应用程序网关以及所有相关资源时，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>后续步骤
* [了解有关禁用 AGIC 外接程序的详细信息](./ingress-controller-disable-addon.md)
* [了解有关 AGIC 支持的批注的详细信息](./ingress-controller-annotations.md)
* [解决 AGIC 问题](./ingress-controller-troubleshoot.md)

