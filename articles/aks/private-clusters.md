---
title: 私有 Azure Kubernetes 服务群集
description: 了解如何创建私有 Azure Kubernetes 服务（AKS）群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830048"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>公共预览版-私有 Azure Kubernetes 服务群集

在专用群集中，控制平面/API 服务器将具有在[RFC1918](https://tools.ietf.org/html/rfc1918)中定义的内部 IP 地址。  通过使用专用群集，可以确保 API 服务器与节点池之间的网络流量仅保留在专用网络上。

控制平面/API 服务器之间的通信（位于 AKS 管理的 Azure 订阅中）和 customer 群集/节点池（位于客户订阅中）可以通过 API 服务器 VNET 中的[专用链接服务][private-link-service]和在 customer AKS 群集的子网中公开的专用终结点相互通信。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此，这些功能并不用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

* 需要 Azure CLI 版本2.0.77 或更高版本，以及 aks-preview 0.4.18 扩展

## <a name="current-supported-regions"></a>当前支持的区域
* 美国西部
* 美国西部 2
* 美国东部 2
* 加拿大中部
* 欧洲北部
* 欧洲西部
* 澳大利亚东部

## <a name="install-latest-aks-cli-preview-extension"></a>安装最新的 AKS CLI 预览扩展

若要使用专用群集，需要*aks* CLI 扩展版本0.4.18 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 在订阅上注册功能时，当前无法注册该功能。 启用某些预览功能后，默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

状态显示为 "*已注册*" 可能需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

注册状态后，使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>创建专用 AKS 群集

#### <a name="default-basic-networking"></a>默认基本网络 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中--enable-private-群集是专用群集的必需标志 

#### <a name="advanced-networking"></a>高级网络  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
其中--enable-private-群集是专用群集的必需标志 

## <a name="steps-to-connect-to-the-private-cluster"></a>连接到专用群集的步骤
API 服务器终结点没有公共 IP 地址。 因此，用户将需要在虚拟网络中创建 Azure 虚拟机并连接到 API 服务器。 中的步骤

* 获取用于连接到群集的凭据

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* 在 AKS 群集所在的 VNET 中创建一个 VM，或在另一个 VNET 中创建一个 VM，并将此 VNET 与 AKS 群集 VNET 一起使用
* 如果在另一个 VNET 中创建一个 VM，则需要设置此 VNET 与专用 DNS 区域之间的链接
    * 在门户中转到 MC_ * 资源组 
    * 单击专用 DNS 区域 
    * 选择左窗格中的 "虚拟网络" 链接
    * 创建新链接，将 VM 的 VNET 添加到专用 DNS 区域 *（需要几分钟时间才能使 DNS 区域链接变得可用）*
    * 返回到门户中的 MC_ * 资源组
    * 在右侧窗格中选择 "虚拟网络"。 虚拟网络名称的格式为 aks-*。
    * 在左侧窗格中选择 "对等互连"
    * 单击 "添加"，添加 VM 的虚拟网络并创建对等互连。
    * 前往具有 VM 的 Vnet，然后单击 "对等互连" 并选择 "AKS" 虚拟网络并创建对等互连。 如果 AKS 虚拟网络上的地址范围与 VM 的虚拟网络冲突，则对等互连将会失败。 有关虚拟网络对等互连的详细信息，请参阅此[文档][virtual-network-peering]。
* 通过 SSH 连接到 VM
* 安装 Kubectl 工具并运行 Kubectl 命令

## <a name="dependencies"></a>依赖项  
* 仅标准 LB-不支持基本负载均衡器  

## <a name="limitations"></a>限制 
* Azure 专用[链接服务限制][private-link-service]适用于专用群集，Azure 专用终结点和虚拟网络服务终结点当前在同一 VNET 中不受支持
* 不支持专用群集中的虚拟节点在专用 Azure VNET 中旋转专用 ACI 实例
* 不支持具有专用群集的现成的 Azure DevOps 集成
* 如果客户需要启用 ACR 才能使用私有 AKS，则需要将 ACR 的 VNET 与代理群集 VNET 对等互连
* 当前不支持 Azure Dev Spaces
* 不支持将现有的 AKS 群集转换为专用群集  
* 删除或修改客户子网中的专用终结点将导致群集停止运行 
* 容器 Azure Monitor 当前不受支持
* 当前不支持自带 DNS


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

