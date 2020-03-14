---
title: 创建专用的 Azure Kubernetes 服务群集
description: 了解如何创建私有 Azure Kubernetes 服务（AKS）群集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205173"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>创建专用的 Azure Kubernetes 服务群集

在专用群集中，控制平面或 API 服务器具有内部 IP 地址，这些地址在[专用 intranets 文档的 RFC1918 分配](https://tools.ietf.org/html/rfc1918)中定义。 通过使用专用群集，可以确保 API 服务器与节点池之间的网络流量仅保留在专用网络上。

控制平面或 API 服务器位于 Azure Kubernetes 服务（AKS）管理的 Azure 订阅中。 客户的群集或节点池在客户的订阅中。 服务器和群集或节点池可以通过 API 服务器虚拟网络中的[Azure 专用链接服务][private-link-service]和在客户 AKS 群集的子网中公开的专用终结点相互通信。

## <a name="prerequisites"></a>必备条件

* Azure CLI 版本2.2.0 或更高版本

## <a name="create-a-private-aks-cluster"></a>创建专用 AKS 群集

### <a name="create-a-resource-group"></a>创建资源组

为 AKS 群集创建资源组或使用现有资源组。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>默认基本网络 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中 *--enable-private-群集*是专用群集的必需标志。 

### <a name="advanced-networking"></a>高级网络  

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
其中 *--enable-private-群集*是专用群集的必需标志。 

> [!NOTE]
> 如果 Docker 桥地址 CIDR （172.17.0.1/16）与子网 CIDR 冲突，请相应地更改 Docker 桥地址。

## <a name="options-for-connecting-to-the-private-cluster"></a>用于连接到专用群集的选项

API 服务器终结点没有公共 IP 地址。 若要管理 API 服务器，你将需要使用可访问 AKS 群集的 Azure 虚拟网络（VNet）的 VM。 有几个选项可用于建立与专用群集的网络连接。

* 在与 AKS 群集相同的 Azure 虚拟网络（VNet）中创建 VM。
* 在单独的网络中使用 VM，并设置[虚拟网络对等互连][virtual-network-peering]。  有关此选项的详细信息，请参阅下面的部分。
* 使用[Express Route 或 VPN][express-route-or-VPN]连接。

在与 AKS 群集相同的 VNET 中创建 VM 是最简单的选项。  Express Route 和 Vpn 增加了成本，并且需要额外的网络复杂性。  虚拟网络对等互连要求你计划网络 CIDR 范围，以确保没有重叠范围。

## <a name="virtual-network-peering"></a>虚拟网络对等互连

如前所述，VNet 对等互连是一种访问专用群集的方法。 若要使用 VNet 对等互连，需要设置虚拟网络与专用 DNS 区域之间的链接。
    
1. 中转到 Azure 门户中的 MC_ * 资源组。  
2. 选择专用 DNS 区域。   
3. 在左窗格中，选择 "**虚拟网络**" 链接。  
4. 创建新链接，将 VM 的虚拟网络添加到专用 DNS 区域。 DNS 区域链接将需要几分钟的时间。  
5. 返回到 Azure 门户中的 MC_ * 资源组。  
6. 在右侧窗格中，选择 "虚拟网络"。 虚拟网络名称的格式为 aks- *\** 。  
7. 在左窗格中，选择 "**对等互连**"。  
8. 选择 "**添加**"，添加 VM 的虚拟网络，然后创建对等互连。  
9. 前往具有 VM 的虚拟网络，选择**对等互连**，选择 AKS 虚拟网络，然后创建对等互连。 如果 AKS 虚拟网络上的地址范围和 VM 的虚拟网络冲突，则对等互连失败。 有关详细信息，请参阅[虚拟网络对等互连][virtual-network-peering]。

## <a name="dependencies"></a>依赖项  

* 仅标准 Azure 负载均衡器支持专用链接服务。 不支持基本的 Azure 负载均衡器。  
* 若要使用自定义 DNS 服务器，请将具有 DNS 的 AD 服务器部署到此 IP 168.63.129.16

## <a name="limitations"></a>限制 
* IP 授权范围不能应用于专用 api 服务器终结点，它们仅适用于公共 API 服务器
* 某些区域当前支持可用性区域，请参阅本文档的开头部分 
* [Azure 专用链接服务限制][private-link-service]适用于专用群集、Azure 专用终结点和虚拟网络服务终结点，这些终结点当前在同一虚拟网络中不受支持。
* 不支持专用群集中的虚拟节点在专用的 Azure 虚拟网络中实现专用 Azure 容器实例（ACI）
* 不支持具有专用群集的现成的 Azure DevOps 集成
* 对于需要使 Azure 容器注册表能与专用 AKS 配合使用的客户，容器注册表虚拟网络必须与代理群集虚拟网络对等互连。
* 当前不支持 Azure Dev Spaces
* 不支持将现有的 AKS 群集转换为专用群集
* 删除或修改客户子网中的专用终结点将导致群集停止工作。 
* 容器 Azure Monitor 当前不支持。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

