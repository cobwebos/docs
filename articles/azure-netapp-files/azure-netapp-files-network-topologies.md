---
title: Azure NetApp 文件网络规划指南 |Microsoft Docs
description: 介绍可帮助你使用 Azure NetApp 文件设计有效的网络体系结构的指南。
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
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
ms.openlocfilehash: 02852b325a22f274b4aa6e793b03c733c38bb9aa
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984124"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp 文件网络规划指南

网络体系结构规划是设计应用程序基础结构的关键元素。 本文可帮助你为工作负荷设计有效的网络体系结构，使其受益于 Azure NetApp 文件的丰富功能。

Azure NetApp 文件卷设计为包含在 Azure 虚拟网络中名为[委托子](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)网的特殊用途子网中。 因此，你可以根据需要通过虚拟网络网关（ExpressRoute 或 VPN 网关）在虚拟网络网关（ExpressRoute 或 VPN 网关）上直接从 VNet、对等互连 Vnet 或本地访问卷。 此子网专用于 Azure NetApp 文件，并且没有与其他 Azure 服务或 Internet 建立连接。

## <a name="considerations"></a>注意事项  

在规划 Azure NetApp 文件网络时，应了解一些注意事项。

### <a name="constraints"></a>约束

Azure NetApp 文件当前不支持以下功能： 

* 应用于委托子网的网络安全组（Nsg）
* 用户定义的路由（Udr），其地址前缀为 Azure NetApp 文件子网
* Azure NetApp 文件接口上的 azure 策略（例如，自定义命名策略）
* Azure NetApp 文件流量的负载均衡器

以下网络限制适用于 Azure NetApp 文件：

* VNet 中使用 Azure NetApp 文件（包括对等互连 Vnet）的 Ip 数不能超过1000。 我们正在努力提高此限制，以满足客户的规模要求。 在这种情况下，如果需要更多的 Ip，请使用用例和所需的限制与我们的支持团队联系。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。


### <a name="supported-network-topologies"></a>支持的网络拓扑

下表描述了 Azure NetApp 文件支持的网络拓扑。  它还描述了不受支持的拓扑的解决方法。 

|    拓扑    |    支持    |     解决方法    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    与本地 VNet 中的卷的连接    |    是    |         |
|    连接到对等互连 VNet 中的卷（相同区域）    |    是    |         |
|    连接到对等互连 VNet 中的卷（跨区域或全局对等互连）    |    否    |    无    |
|    通过 ExpressRoute 网关连接到卷    |    是    |         |
|    通过 ExpressRoute 网关从本地连接到分支 VNet 中的卷，并使用网关传输连接到 VNet 对等互连    |    是    |        |
|    通过 VPN 网关从本地连接到分支 VNet 中的卷    |    是    |         |
|    通过 VPN 网关从本地连接到分支 VNet 中的卷，并使用网关传输连接到 VNet 对等互连    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的虚拟网络

本部分介绍可帮助你进行虚拟网络规划的概念。

### <a name="azure-virtual-networks"></a>Azure 虚拟网络

预配 Azure NetApp 文件卷之前，需要创建 Azure 虚拟网络（VNet）或使用订阅中已存在的虚拟网络。 VNet 定义卷的网络边界。  有关创建虚拟网络的详细信息，请参阅[Azure 虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子网

子网将虚拟网络细分为单独的地址空间，这些地址空间可供 Azure 资源使用。  Azure NetApp 文件卷包含在一个特殊用途的子网中，称为[委托子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 

子网委派为 Azure NetApp 文件服务提供在子网中创建特定于服务的资源的显式权限。  它使用部署服务中的唯一标识符。 在这种情况下，将创建一个网络接口，以便与 Azure NetApp 文件建立连接。

如果使用新的 VNet，则可以按照将[子网委托给 Azure Netapp 文件](azure-netapp-files-delegate-subnet.md)中的说明，创建子网并将子网委托给 Azure netapp 文件。 你还可以委托尚未委派给其他服务的现有空子网。

如果 VNet 与另一个 VNet 对等互连，则不能扩展 VNet 地址空间。 出于此原因，需要在 VNet 地址空间内创建新的委托子网。 如果需要扩展地址空间，则必须在展开地址空间之前删除 VNet 对等互连。

### <a name="udrs-and-nsgs"></a>Udr 和 Nsg

在 Azure NetApp 文件的委托子网中，不支持用户定义的路由（Udr）和网络安全组（Nsg）。

一种解决方法是，可以将 Nsg 应用到其他子网，以允许或拒绝进出 Azure NetApp 文件委托子网的流量。  

## <a name="azure-native-environments"></a>Azure 本机环境

下图说明了 Azure 本机环境：

![Azure 本机网络环境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是从同一 VNet 中的虚拟机（VM）创建或连接到 Azure NetApp 文件卷。 对于上图中的 VNet 2，卷1在委托子网中创建，并且可以装载到默认子网中的 VM 1。

### <a name="vnet-peering"></a>VNet 对等

如果在同一区域中有需要访问彼此资源的其他 Vnet，则可使用[VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)连接 vnet，以通过 Azure 基础结构实现安全连接。 

请考虑上图中的 VNet 2 和 VNet 3。 如果 VM 2 需要连接到 VM 3 或卷2，或者 VM 3 需要连接到 VM 2 或卷1，则需要在 VNet 2 和 VNet 3 之间启用 VNet 对等互连。 

此外，请考虑一种方案，其中 VNet 1 与 VNet 2 对等互连，VNet 2 在同一区域中使用 VNet 3 进行对等互连。 VNet 1 中的资源可以连接到 VNet 2 中的资源，但无法连接到 VNet 3 中的资源，除非 VNet 1 和 VNet 3 是对等互连。 

在上面的关系图中，尽管 VM 3 可以连接到卷1，但 VM 4 无法连接到卷2。  导致这种情况的原因是：辐射 Vnet 不是对等互连，并且_不支持通过 VNet 对等互连进行传输路由_。

## <a name="hybrid-environments"></a>混合环境

下图演示了混合环境： 

![混合网络环境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合方案中，本地数据中心内的应用程序需要访问 Azure 中的资源。  无论是要将数据中心扩展到 Azure，还是要使用 Azure 本机服务或进行灾难恢复，都是如此。 请参阅[VPN 网关规划选项](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)，了解有关如何通过站点到站点 VPN 或 ExpressRoute 将多个资源本地连接到 Azure 中的资源的信息。

在混合中心辐射型拓扑中，Azure 中的集线器 VNet 充当连接到本地网络的中心点。 辐射与中心 Vnet 对等互连，可用于隔离工作负荷。

根据配置，可以将本地资源连接到中心和辐射中的资源。

在上面所示的拓扑中，本地网络连接到 Azure 中的集线器 VNet，同一区域中有2个分支 Vnet 与中心 VNet 对等互连。  在此方案中，Azure NetApp 文件卷支持的连接选项如下所示：

* 本地资源 VM 1 和 VM 2 可通过站点到站点 VPN 或 ExpressRoute 线路连接到中心的卷1。 
* 本地资源 VM 1 和 VM 2 可通过站点到站点 VPN 和区域 Vnet 对等互连连接到卷2或卷3。
* 中心 VNet 中的 VM 3 可以连接到分支 vnet 1 和分支 VNet 2 中的第3卷。
* 分支 vnet 1 中的 VM 4 和分支 VNet 2 中的 VM 5 可以连接到中心 VNet 中的卷1。

分支 VNet 1 中的 VM 4 无法连接到分支 VNet 2 中的卷3。 此外，轮辐 VNet2 中的 VM 5 无法连接到分支 VNet 1 中的第2卷。 这是因为辐射 Vnet 不对等互连，_不支持通过 VNet 对等互连进行传输路由_。

## <a name="next-steps"></a>后续步骤

[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
