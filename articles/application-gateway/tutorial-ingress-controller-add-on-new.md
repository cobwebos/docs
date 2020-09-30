---
title: 教程：使用新的 Azure 应用程序网关实例为新的 AKS 群集启用入口控制器加载项
description: 本教程介绍如何通过 Azure CLI 使用新的应用程序网关实例，为新的 AKS 群集启用入口控制器加载项。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: ab917fe476a40eb8ea559bc08e52d4bbf16a8436
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285581"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>教程：使用新的应用程序网关实例为新的 AKS 群集启用入口控制器加载项（预览版）

你可以使用 Azure CLI 为 [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 群集启用[应用程序网关入口控制器 (AGIC)](ingress-controller-overview.md) 加载项。 此加载项目前以预览版提供。

在本教程中，你将创建一个启用 AGIC 加载项的 AKS 群集。 创建群集时，将自动创建要使用的 Azure 应用程序网关实例。 然后将部署一个示例应用程序，该应用程序将利用该加载项通过应用程序网关公开应用程序。 

此加载项为 AKS 群集提供了一种比[之前通过 Helm 进行部署](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)快得多的 AGIC 部署方法。 它还提供了完全托管体验。    

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建资源组。 
> * 创建启用 AGIC 加载项的新 AKS 群集。 
> * 在 AKS 群集上部署将 AGIC 用于入口的示例应用程序。
> * 检查是否可以通过应用程序网关访问应用程序。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要注册 AKS-IngressApplicationGatewayAddon 功能标志，请使用 [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令，如以下示例所示： 当该加载项仍处于预览阶段时，对于每个订阅，只需执行此操作一次。
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

可能需要花费几分钟时间，状态才会显示为“`Registered`”。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令来检查注册状态：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

安装或更新本教程的 aks-preview 扩展。 运行以下 Azure CLI 命令：
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 下面的示例在 canadacentral 位置（区域）创建名为 myResourceGroup 的资源组 ： 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>部署启用了加载项的 AKS 群集

现在将部署启用了 AGIC 加载项的新 AKS 群集。 如果你未提供要在此过程中使用的现有应用程序网关实例，我们将自动创建并设置新的应用程序网关实例，用于向 AKS 群集提供流量。  

> [!NOTE]
> 应用程序网关入口控制器加载项仅支持应用程序网关 v2 SKU（标准版和 WAF 版），不支持应用程序网关 v1 SKU 。 通过 AGIC 加载项部署新的应用程序网关实例时，只能部署应用程序网关 Standard_v2 SKU。 如果要为应用程序网关 WAF_v2 SKU 启用该加载项，请使用以下方法之一：
>
> - 通过门户启用应用程序网关上的 WAF。 
> - 首先创建 WAF_v2 应用程序网关实例，然后按照如何[在现有的 AKS 群集和现有应用程序网关实例中启用 AGIC 加载项](tutorial-ingress-controller-add-on-existing.md)中的说明进行操作。 

下面的示例将使用 [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) 和[托管标识](https://docs.microsoft.com/azure/aks/use-managed-identity)部署名为 myCluster 的新 AKS 群集。 还将在创建的 myResourceGroup 资源组中启用 AGIC 加载项。 

如果在未指定现有应用程序网关实例的情况下部署启用 AGIC 加载项的新 AKS 群集，则将自动创建 Standard_v2 SKU 应用程序网关实例。 因此还需要指定应用程序网关实例的名称和子网地址空间。 应用程序网关实例的名称将是 myApplicationGateway，将要使用的子网地址空间是 10.2.0.0/16。 请确保你已在本教程开头添加或更新了 aks-preview 扩展。 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

若要为 `az aks create` 命令配置其他参数，请参阅[这些参考](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)。 

> [!NOTE]
> 创建的 AKS 群集将显示在创建的 myResourceGroup 资源组中。 但是，自动创建的应用程序网关实例将位于代理池所在的节点资源组中。 默认情况下，节点资源组将命名为 MC_resource-group-name_cluster-name_location，但可以修改。 

## <a name="deploy-a-sample-application-by-using-agic"></a>部署使用 AGIC 的示例应用程序

现在将为创建的 AKS 群集部署示例应用程序。 该应用程序将使用 AGIC 加载项作为入口，并会将应用程序网关实例连接到 AKS 群集。 

首先，通过运行 `az aks get-credentials` 命令获取 AKS 群集的凭据： 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

获取凭据后，运行以下命令来设置一个示例应用程序，该应用程序将 AGIC 用作群集的入口。 AGIC 会使用你部署的新示例应用程序的相应路由规则更新你之前设置的应用程序网关实例。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>检查应用程序是否可供访问

将应用程序网关实例设置为向 AKS 群集提供流量以后，接下来让我们验证是否可以访问你的应用程序。 首先获取入口的 IP 地址： 

```azurecli-interactive
kubectl get ingress
```

通过以下任一方式检查所创建的示例应用程序是否正在运行：

- 访问运行前面的命令所获取的应用程序网关实例的 IP 地址。
- 使用 `curl`。 

应用程序网关可能需要一分钟才能获取更新。 如果应用程序网关在门户上仍处于“正在更新”状态，请等待更新完成，然后再尝试访问该 IP 地址。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、应用程序网关实例以及所有相关资源，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何禁用 AGIC 加载项](./ingress-controller-disable-addon.md)

