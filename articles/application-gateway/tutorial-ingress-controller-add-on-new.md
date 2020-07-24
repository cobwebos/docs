---
title: 使用新的 Azure 应用程序网关实例为新的 AKS 群集启用入口控制器外接程序
description: 了解如何使用 Azure CLI 为新的 AKS 群集启用入口控制器外接程序，并提供新的应用程序网关实例。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 4634421829cf71c0c5b9476f8ff3d08b9caa7dbd
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117334"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>教程：使用新的应用程序网关实例为新的 AKS 群集启用入口控制器外接程序（预览版）

可以使用 Azure CLI 为[Azure Kubernetes Services （AKS）](https://azure.microsoft.com/services/kubernetes-service/)群集启用[应用程序网关入口控制器（AGIC）](ingress-controller-overview.md)加载项。 外接程序目前处于预览阶段。

在本教程中，你将创建一个启用了 AGIC 外接程序的 AKS 群集。 创建群集时，将自动创建要使用的 Azure 应用程序网关实例。 然后，将部署一个示例应用程序，该应用程序将使用该外接程序通过应用程序网关公开应用程序。 

外接程序提供了一种更快的方法来部署 AKS 群集的 AGIC，而不是[以前的 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)。 它还提供完全托管的体验。    

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建资源组。 
> * 使用启用 AGIC 外接程序创建新的 AKS 群集。 
> * 使用 AGIC 在 AKS 群集上部署示例应用程序。
> * 检查是否可以通过应用程序网关访问应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

如果选择在本地安装并使用 CLI，本教程需要运行 Azure CLI 版本2.0.4 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

使用[az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register)命令注册*AKS-IngressApplicationGatewayAddon*功能标志，如以下示例中所示。 对于每个订阅，只需执行此操作一次，同时外接程序仍处于预览阶段。
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

显示状态可能需要几分钟时间 `Registered` 。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令来检查注册状态：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register)命令刷新 ContainerService 资源提供程序的注册：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

安装或更新本教程的 aks-preview 扩展。 使用以下 Azure CLI 命令：
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 以下示例在*canadacentral*位置（区域）中创建名为*myResourceGroup*的资源组： 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>使用启用外接程序部署 AKS 群集

现在，你将在启用 AGIC 加载项的情况中部署新的 AKS 群集。 如果未提供要在此过程中使用的现有应用程序网关实例，我们将自动创建并设置一个新的应用程序网关实例，以提供到 AKS 群集的流量。  

> [!NOTE]
> 应用程序网关入口控制器加载项*仅*支持应用程序网关 v2 Sku （STANDARD 和 WAF），而*不*支持应用程序网关 v1 sku。 通过 AGIC 外接程序部署新的应用程序网关实例时，只能 Standard_v2 SKU 中部署应用程序网关。 如果要为应用程序网关启用外接程序 WAF_v2 SKU，请使用以下方法之一：
>
> - 通过门户在应用程序网关上启用 WAF。 
> - 首先创建 WAF_v2 应用程序网关实例，然后按照如何[使用现有 AKS 群集和现有应用程序网关实例启用 AGIC 外接](tutorial-ingress-controller-add-on-existing.md)程序的说明进行操作。 

在以下示例中，你将使用[AZURE CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking)和[托管标识](https://docs.microsoft.com/azure/aks/use-managed-identity)部署名为*myCluster*的新 AKS 群集。 AGIC 外接程序将在创建的资源组*myResourceGroup*中启用。 

如果在未指定现有应用程序网关实例的情况下启用 AGIC 外接程序部署新的 AKS 群集，则表示自动创建 Standard_v2 SKU 应用程序网关实例。 因此，你还将指定应用程序网关实例的名称和子网地址空间。 应用程序网关实例的名称将为*myApplicationGateway*，使用的子网地址空间为 10.2.0.0/16。 请确保在本教程开头添加或更新 aks-preview 扩展。 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

若要配置命令的其他参数 `az aks create` ，请参阅[这些引用](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)。 

> [!NOTE]
> 你创建的 AKS 群集将显示在创建的资源组*myResourceGroup*中。 但是，自动创建的应用程序网关实例将位于代理池所在的节点资源组中。 节点资源组按默认命名为*MC_resource 组 name_cluster name_location* ，但可以修改。 

## <a name="deploy-a-sample-application-by-using-agic"></a>使用 AGIC 部署示例应用程序

现在会将示例应用程序部署到创建的 AKS 群集。 应用程序将为入口使用 AGIC 外接程序，并将应用程序网关实例连接到 AKS 群集。 

首先，通过运行以下命令获取 AKS 群集的凭据 `az aks get-credentials` ： 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

现在，你已有凭据，请运行以下命令来设置一个示例应用程序，该应用程序使用 AGIC 来进入群集。 AGIC 会将先前设置的应用程序网关实例与所部署的新示例应用程序的路由规则进行更新。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>检查应用程序是否可供访问

现在，已将应用程序网关实例设置为向 AKS 群集提供流量，接下来我们验证你的应用程序是否可访问。 首先，获取入口的 IP 地址： 

```azurecli-interactive
kubectl get ingress
```

检查您创建的示例应用程序是否正在运行：

- 访问运行前面的命令所获取的应用程序网关实例的 IP 地址。
- 使用 `curl` 。 

应用程序网关可能需要一分钟才能获取更新。 如果应用程序网关仍处于门户上的 "**正在更新**" 状态，请先完成该操作，然后再尝试访问 IP 地址。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、应用程序网关实例和所有相关资源，请将其删除：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤
* [了解如何禁用 AGIC 外接程序](./ingress-controller-disable-addon.md)
* [了解 AGIC 支持哪些批注](./ingress-controller-annotations.md)
* [排查 AGIC 的问题](./ingress-controller-troubleshoot.md)

