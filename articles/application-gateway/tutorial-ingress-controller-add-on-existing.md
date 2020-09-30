---
title: 教程：使用现有 Azure 应用程序网关为现有的 AKS 群集启用入口控制器加载项
description: 本教程介绍了如何使用现有的应用程序网关为现有的 AKS 群集启用入口控制器加载项
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: d0ce58c5bb6de4712117959f10b48ae3449f0b97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285615"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>教程：通过 Azure CLI 使用现有的应用程序网关为现有 AKS 群集启用应用程序网关入口控制器加载项（预览版）

你可以使用 Azure CLI 为 [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 群集启用[应用程序网关入口控制器 (AGIC)](ingress-controller-overview.md) 加载项（当前为预览版）。 本教程介绍如何使用 AGIC 加载项通过部署在不同虚拟网络中的现有应用程序网关在现有 AKS 群集中公开你的 Kubernetes 应用程序。 首先，你将在一个虚拟网络中创建一个 AKS 群集，在一个单独的虚拟网络中创建一个应用程序网关，以模拟现有资源。 然后，你将启用 AGIC 加载项，将两个虚拟网络对等互连在一起，并部署一个示例应用程序，系统将使用 AGIC 加载项通过应用程序网关公开该应用程序。 如果要为同一虚拟网络中的现有应用程序网关和现有 AKS 群集启用 AGIC 加载项，则可跳过下面的对等互连步骤。 此加载项为 AKS 群集提供了一种比[之前通过 Helm 进行部署](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)快得多的 AGIC 部署方法，而且还提供了完全托管体验。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建资源组 
> * 创建新的 AKS 群集 
> * 创建新的应用程序网关 
> * 使用现有的应用程序网关在现有的 AKS 群集中启用 AGIC 加载项 
> * 将应用程序网关虚拟网络与 AKS 群集虚拟网络对等互连
> * 在 AKS 群集上部署将 AGIC 用于入口的示例应用程序
> * 检查是否可以通过应用程序网关访问应用程序

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

使用 [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令注册 *AKS-IngressApplicationGatewayAddon* 功能标志，如以下示例所示；当此加载项仍为预览版时，只需为每个订阅执行一次此操作：
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

可能需要花费几分钟时间，状态才会显示为“已注册”。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令检查注册状态：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

对于本教程，请确保安装/更新 aks-preview 扩展；请使用以下 Azure CLI 命令
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 下面的示例在 *canadacentral* 位置（区域）创建名为 *myResourceGroup* 的资源组。 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>部署新的 AKS 群集

现在，你将部署新的 AKS 群集，以模拟你有一个现有 AKS 群集且需要为其启用 AGIC 加载项的情况。  

在下面的示例中，你将在所创建的资源组 myResourceGroup 中使用 [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) 和[托管实例](https://docs.microsoft.com/azure/aks/use-managed-identity)部署名为 myCluster 的新 AKS 群集 。    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

若要为 `az aks create` 命令配置其他参数，请访问[此处](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)的参考信息。 

## <a name="deploy-a-new-application-gateway"></a>部署新的应用程序网关 

现在，你将部署新的应用程序网关，以模拟你有一个现有的应用程序网关且需要使用它对发往 AKS 群集 *myCluster* 的流量进行负载均衡的情况。 应用程序网关的名称将是 *myApplicationGateway*，但你需要首先创建一个名为 *myPublicIp* 的公共 IP 资源、一个名为 *myVnet* 且地址空间为 11.0.0.0/8 的新虚拟网络、一个名为 *mySubnet* 且地址空间为 11.1.0.0/16 的子网，然后使用 *myPublicIp* 在 *mySubnet* 中部署你的应用程序网关。 

在不同虚拟网络中使用 AKS 群集和应用程序网关时，两个虚拟网络的地址空间不得重叠。 AKS 群集在其中进行部署的默认地址空间为 10.0.0.0/8，因此，我们将应用程序网关虚拟网络地址前缀设置为 11.0.0.0/8。 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 应用程序网关入口控制器 (AGIC) 加载项**仅**支持应用程序网关 v2 SKU（标准版和 WAF 版），**不**支持应用程序网关 v1 SKU。 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>使用现有的应用程序网关在现有的 AKS 群集中启用 AGIC 加载项 

现在，你将在所创建的 AKS 群集 *myCluster* 中启用 AGIC 加载项，并指定 AGIC 加载项使用你创建的现有应用程序网关 *myApplicationGateway*。 请确保你已在本教程开头添加/更新了 aks-preview 扩展。 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>将两个虚拟网络对等互连在一起

由于我们已将 AKS 群集部署在其自己的虚拟网络中，并将应用程序网关部署在另一个虚拟网络中，因此你需要将这两个虚拟网络对等互连在一起，以便流量从应用程序网关流向群集中的 Pod。 将两个虚拟网络对等互连需要两次运行 Azure CLI 命令，以确保连接是双向的。 第一个命令将创建从应用程序网关虚拟网络到 AKS 虚拟网络的对等连接；第二个命令将创建另一个方向的对等连接。 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>部署使用 AGIC 的示例应用程序 

现在，你将向所创建的 AKS 群集部署一个示例应用程序，该应用程序将 AGIC 加载项用于入口，并将应用程序网关连接到 AKS 群集。 首先，你将通过运行 `az aks get-credentials` 命令获取所部署的 AKS 群集的凭据。 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

获取你创建的群集的凭据后，运行以下命令来设置一个示例应用程序，该应用程序将 AGIC 用于群集的入口。 AGIC 会使用你部署的新示例应用程序的相应路由规则更新你之前设置的应用程序网关。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>检查应用程序是否可供访问

将应用程序网关设置为向 AKS 群集提供流量以后，接下来让我们验证是否可以访问你的应用程序。 首先获取入口的 IP 地址。 

```azurecli-interactive
kubectl get ingress
```

检查你创建的示例应用程序是否已启动并正在运行，方法是访问通过运行上述命令获取的应用程序网关的 IP 地址或通过 `curl` 进行检查。 应用程序网关可能需要花费一分钟来获取更新，因此，如果应用程序网关在门户上仍然处于“正在更新”状态，则等待其完成更新，然后再尝试访问该 IP 地址。 

## <a name="clean-up-resources"></a>清理资源

当不再需要资源组、应用程序网关以及所有相关资源时，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解如何禁用 AGIC 加载项](./ingress-controller-disable-addon.md)