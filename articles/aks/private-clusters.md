---
title: 创建专用 Azure 库伯奈斯服务群集
description: 了解如何创建专用 Azure 库伯奈斯服务 （AKS） 群集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 87f52c5a749b531e5b0656e0b30ff0fe9c1a57bf
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398043"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>创建专用 Azure 库伯奈斯服务群集

在专用群集中，控制平面或 API 服务器具有[在 RFC1918 - 专用 Internet](https://tools.ietf.org/html/rfc1918)地址分配文档中定义的内部 IP 地址。 通过使用专用群集，可以确保 API 服务器和节点池之间的网络流量仅保留在专用网络上。

控制平面或 API 服务器位于 Azure 库伯奈斯服务 （AKS） 管理的 Azure 订阅中。 客户的群集或节点池位于客户的订阅中。 服务器和群集或节点池可以通过 API 服务器虚拟网络中的[Azure 专用链接服务][private-link-service]以及在客户 AKS 群集的子网中公开的专用终结点进行通信。

## <a name="prerequisites"></a>先决条件

* Azure CLI 版本 2.2.0 或更高版本

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
*其中 --启用-私有群集*是专用群集的必填标志。 

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
*其中 --启用-私有群集*是专用群集的必填标志。 

> [!NOTE]
> 如果 Docker 网桥地址 CIDR （172.17.0.1/16） 与子网 CIDR 冲突，则相应地更改 Docker 网桥地址。

## <a name="options-for-connecting-to-the-private-cluster"></a>连接到专用群集的选项

API 服务器终结点没有公共 IP 地址。 要管理 API 服务器，您需要使用有权访问 AKS 群集的 Azure 虚拟网络 （VNet） 的 VM。 建立与专用群集的网络连接有多种选项。

* 在与 AKS 群集相同的 Azure 虚拟网络 （VNet） 中创建 VM。
* 在单独的网络中使用 VM 并设置[虚拟网络对等互连][virtual-network-peering]。  有关此选项的详细信息，请参阅以下部分。
* 使用[快速路由或 VPN][express-route-or-VPN]连接。

在与 AKS 群集相同的 VNET 中创建 VM 是最简单的选择。  快速路由和 VPN 会增加成本，并需要额外的网络复杂性。  虚拟网络对等互连要求您规划网络 CIDR 范围，以确保没有重叠范围。

## <a name="virtual-network-peering"></a>虚拟网络对等互连

如前所述，VNet 对等互连是访问专用群集的一种方式。 要使用 VNet 对等互连，您需要在虚拟网络和专用 DNS 区域之间设置链接。
    
1. 转到 Azure 门户中的MC_* 资源组。  
2. 选择专用 DNS 区域。   
3. 在左侧窗格中，选择**虚拟网络**链接。  
4. 创建新链接以将 VM 的虚拟网络添加到专用 DNS 区域。 DNS 区域链接需要几分钟才能可用。  
5. 返回 Azure 门户中的MC_* 资源组。  
6. 在右侧窗格中，选择虚拟网络。 虚拟网络名称以*aks-vnet- 的形式\**。  
7. 在左侧窗格中，选择**对等互连**。  
8. 选择 **"添加**"，添加 VM 的虚拟网络，然后创建对等互连。  
9. 转到具有 VM 的虚拟网络，选择**对等互连**，选择 AKS 虚拟网络，然后创建对等互连。 如果 AKS 虚拟网络上的地址范围和 VM 的虚拟网络冲突，对等互连将失败。 有关详细信息，请参阅[虚拟网络对等互连][virtual-network-peering]。

## <a name="hub-and-spoke-with-custom-dns"></a>中心，并与自定义 DNS 交谈

[集线器和分支体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)通常用于在 Azure 中部署网络。 在其中许多部署中，分支 VNet 中的 DNS 设置配置为引用中央 DNS 转发器，以允许本地和基于 Azure 的 DNS 解析。 将 AKS 群集部署到此类网络环境中时，必须考虑一些特殊注意事项。

![专用群集集线器和分支](media/private-clusters/aks-private-hub-spoke.png)

1. 默认情况下，在预配专用群集时，在群集托管资源组中创建专用终结点 （1） 和专用 DNS 区域 （2）。 群集使用专用区域中的 A 记录解析专用终结点的 IP 以与 API 服务器通信。

2. 专用 DNS 区域仅链接到群集节点附加到 （3） 的 VNet。 这意味着专用终结点只能由该链接 VNet 中的主机解析。 在 VNet 上未配置自定义 DNS（默认）的情况下，对于 DNS 的主机点 168.63.129.16 时，这一点没有问题，因为 DNS 可以解析由于链接而导致的专用 DNS 区域中的记录。

3. 在包含群集的 VNet 具有自定义 DNS 设置 （4） 的情况下，群集部署将失败，除非专用 DNS 区域链接到包含自定义 DNS 解析器 （5） 的 VNet。 此链接可以在群集预配期间创建专用区域后手动创建，或者在使用 Azure 策略或其他基于事件的部署机制（例如，Azure 事件网格和 Azure 函数）检测到区域创建时通过自动化创建此链接。

## <a name="dependencies"></a>依赖项  

* 专用链路服务仅在标准 Azure 负载均衡器上支持。 不支持基本 Azure 负载均衡器。  
* 要使用自定义 DNS 服务器，请使用 Azure DNS IP 168.63.129.16 作为自定义 DNS 服务器中的上游 DNS 服务器。

## <a name="limitations"></a>限制 
* IP 授权范围不能应用于专用 api 服务器终结点，它们仅适用于公共 API 服务器
* 当前某些区域支持可用性区域，请参阅本文档的开头 
* [Azure 专用链接服务限制][private-link-service]适用于专用群集、Azure 专用终结点和虚拟网络服务终结点，这些终结点当前不受同一虚拟网络的支持。
* 不支持专用群集中的虚拟节点在专用 Azure 虚拟网络中旋转专用 Azure 容器实例 （ACI）
* 不支持与专用群集开箱即用的 Azure DevOps 集成
* 对于需要启用 Azure 容器注册表以使用专用 AKS 的客户，容器注册表虚拟网络必须与代理群集虚拟网络对等。
* 当前不支持 Azure 开发空间
* 不支持将现有 AKS 群集转换为专用群集
* 删除或修改客户子网中的专用终结点将导致群集停止运行。 
* 当前不支持容器实时数据的 Azure 监视器。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

