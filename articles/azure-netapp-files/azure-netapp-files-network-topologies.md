---
title: 用于 Azure 的 NetApp 文件的指导原则的网络规划 |Microsoft Docs
description: 介绍可帮助你通过使用 Azure 的 NetApp 文件设计的有效网络体系结构的指南。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826434"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp 文件网络规划指南

网络体系结构规划是设计任何应用程序基础结构的关键元素。 本文可帮助您设计您的工作负载能够受益于 Azure 的 NetApp 文件的丰富功能的有效网络体系结构。

Azure 的 NetApp 文件卷用于包含在名为专用子网[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)在 Azure 虚拟网络中。 因此，您可以访问卷直接来自 VNet、 对等 Vnet 中的同一区域，或在本地通过虚拟网络网关 （ExpressRoute 或 VPN 网关） 根据需要。 子网专用于 Azure 的 NetApp 文件并没有到其他 Azure 服务或 internet 连接。

## <a name="considerations"></a>注意事项  

在规划 Azure NetApp 文件网络时，应了解几个注意事项。

### <a name="constraints"></a>约束

是用于 Azure 的 NetApp 文件当前不支持以下功能： 

* 子网的网络安全组 (Nsg)
* 使用作为 Azure NetApp 文件子网的下一跃点的用户定义路由 (Udr)
* Azure NetApp 文件接口上的 azure 策略 （例如，自定义命名策略）
* Azure NetApp 文件流量的的负载均衡器

以下网络限制适用于 Azure 的 NetApp 文件：

* 可以连接到的卷 （与 VNet 或跨对等互连的 Vnet） 的 Vm 数量不能超过 1000年。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。


### <a name="supported-network-topologies"></a>支持的网络拓扑

下表描述 Azure NetApp 文件支持的网络拓扑。  它还介绍的解决方法的不受支持的拓扑。 

|    拓扑    |    支持    |     解决方法    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    连接到本地 VNet 中的卷    |    “是”    |         |
|    连接到对等互连的 VNet （同一区域） 中的卷    |    “是”    |         |
|    与卷 （跨区域或全局对等互连） 对等 VNet 中的连接    |    “否”    |    无    |
|    连接到通过 ExpressRoute 网关的卷    |    “是”    |         |
|    从本地连接到辐射 VNet 通过 ExpressRoute 网关和网关传输与对等互连的 VNet 中的卷    |    “否”    |    在中心 VNet (网关与 Azure VNet) 中创建委派的子网    |
|    从本地连接到 VPN 网关通过辐射 VNet 中的卷    |    “是”    |         |
|    从本地连接到辐射 VNet VPN 网关和网关传输与对等互连的 VNet 中的卷    |    “是”    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>对于 Azure NetApp 文件卷的虚拟网络

此部分介绍了概念，帮助你与虚拟网络规划。

### <a name="azure-virtual-networks"></a>Azure 虚拟网络

在之前预配 Azure NetApp 文件卷，必须创建 Azure 虚拟网络 (VNet) 或使用你的订阅中已存在。 VNet 定义的数据量的网络边界。  创建虚拟网络的详细信息，请参阅[Azure 虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子网

子网的虚拟网络划分为可由它们中的 Azure 资源使用单独的地址空间。  调用的专用子网中包含 azure 的 NetApp 文件卷[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 

子网委派为提供对 Azure NetApp 文件服务的子网中创建特定于服务的资源的显式权限。  它使用在部署服务的唯一标识符。 在这种情况下，将创建一个网络接口以便连接到 Azure 的 NetApp 文件。

如果使用新的 VNet，可以创建一个子网和委托到 Azure NetApp 文件中的子网中的以下说明[委托到 Azure NetApp 文件中的子网](azure-netapp-files-delegate-subnet.md)。 您也可以将委托现有一个空子网，不已委派给其他服务。

如果在 VNet 与另一个 VNet 对等互连，不能扩展 VNet 地址空间。 为此，需要在 VNet 地址空间中创建新的委派子网。 如果你需要扩展的地址空间，则必须删除然后再扩展的地址空间的对等互连的 VNet。

### <a name="udrs-and-nsgs"></a>Udr 和 Nsg

使用下一跃点的网络安全组 (Nsg) 不能用作 Azure NetApp 文件委派子网。 同样，用户定义的路由 (Udr) 也不支持。 

解决此问题，可将 Nsg 应用到其他子网的允许或拒绝传入和从 Azure NetApp 文件委派子网的流量。  

## <a name="azure-native-environments"></a>Azure 本机环境

下图说明了 Azure 原生环境：

![Azure 本机网络环境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是创建或连接到 Azure 的 NetApp 文件卷从同一 VNet 中的虚拟机 (VM)。 对于上图中的 VNet 2，卷 1 创建委派的子网中，可以装载在 VM 1 中的默认子网。

### <a name="vnet-peering"></a>VNet 对等

如果需要访问彼此的资源在同一区域中的其他 Vnet，可以使用连接 Vnet [VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)若要启用通过 Azure 基础结构的安全连接。 

上图中，请考虑 VNet 2 和 VNet 3。 如果 VM 1 需要连接到 VM 2 和第 2 卷，或者 VM 2 需要连接到 VM 1 或第 1 卷，然后您需要启用 VNet 对等互连 VNet 2 和 VNet 3 之间。 

此外，请考虑了 VNet 1 VNet 2 建立对等互连和 VNet 2 在同一区域中的 VNet 3 与对等互连的方案。 可从 VNet 1 资源连接到 VNet 2 中的资源，但它无法连接到 VNet 3 中的资源，除非 VNet 1 和 VNet 3 对等互连。 

在上图中，虽然 VM 3 可以连接到第 1 卷，但 VM 4 无法连接到第 2 卷中。  原因是，辐射 Vnet 不对等互连，并且_通过 VNet 对等互连不支持转换性路由_。

## <a name="hybrid-environments"></a>混合环境

下图说明了混合环境： 

![混合网络环境](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

在混合方案中，从本地数据中心的应用程序需要在 Azure 中资源的访问权限。  这种情况，你想要将你的数据中心扩展到 Azure，还是想要使用 Azure 的本机服务或用于灾难恢复。 请参阅[VPN 网关规划选项](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)有关如何连接到通过站点到站点 VPN 或 ExpressRoute 的 Azure 中的资源的多个本地资源。

在混合中心辐射型拓扑中，中心在 Azure 中的 VNet 可作为与你的本地网络的连接的中心点。 辐射 Vnet 对等互连到中心，并且它们可以用于隔离工作负荷。

具体取决于配置。 可从内部部署的资源连接到中心和辐射中的资源。

在如上所示的拓扑，在本地网络连接到中心 VNet 在 Azure 中，并有 2 个辐射 Vnet 与中心 VNet 对等互连。  在此方案中，对于 Azure NetApp 文件卷支持的连接选项如下所示：

* VM 1 和 VM 2 的本地资源可以通过连接到卷 1 在中心站点到站点 VPN 或 ExpressRoute。 
* VM 1 和 VM 2 的本地资源可以连接到第 2 卷或卷 3。
* VM 3 在中心 VNet 可以连接到卷中辐射 VNet 1 2 和第三卷在辐射 VNet 2。
* VM 4 从辐射 VNet 1 和辐射 VNet 2 中为 VM 5 在中心 VNet 中可以连接到卷 1。

辐射 VNet 2 不能连接到第三卷辐射 VNet 1 中的 VM 4。 此外，在分支中为 VM 5 VNet2 无法连接到第 2 卷在辐射 VNet 1。 这种情况是因为辐射 Vnet 不对等互连和_通过 VNet 对等互连不支持转换性路由_。

## <a name="next-steps"></a>后续步骤

[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
