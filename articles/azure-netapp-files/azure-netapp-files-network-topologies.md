---
title: Azure NetApp 文件网络规划指南 |微软文档
description: 描述使用 Azure NetApp 文件帮助您设计有效网络体系结构的指南。
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
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242481"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp 文件网络规划指南

网络体系结构规划是设计任何应用程序基础结构的关键要素。 本文可帮助您为工作负荷设计有效的网络体系结构，以便从 Azure NetApp 文件的丰富功能中获益。

Azure NetApp 文件卷设计为包含在 Azure 虚拟网络中称为[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)的特殊用途子网中。 因此，您可以根据需要直接从 VNet、同一区域中的对等 VNet 或虚拟网络网关（ExpressRoute 或 VPN 网关）从本地访问卷。 子网专用于 Azure NetApp 文件，并且没有连接到其他 Azure 服务或 Internet。

## <a name="considerations"></a>注意事项  

规划 Azure NetApp 文件网络时，应了解一些注意事项。

### <a name="constraints"></a>约束

Azure NetApp 文件当前不支持以下功能： 

* 应用于委派子网的网络安全组 （NSG）
* 用户定义的路由 （UDR）， 地址前缀为 Azure NetApp 文件子网
* Azure NetApp 文件界面上的 Azure 策略（例如，自定义命名策略）
* Azure NetApp 文件流量的负载均衡器

以下网络限制适用于 Azure NetApp 文件：

* 在 VNet 中使用具有 Azure NetApp 文件（包括对等 VNet）的 IP 数不能超过 1000。 我们正在努力提高这一限额，以满足客户规模需求。 在此期间，如果您需要更多 IP，请使用您的用例和要求的限制联系我们的支持团队。
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。


### <a name="supported-network-topologies"></a>支持的网络拓扑

下表描述了 Azure NetApp 文件支持的网络拓扑。  它还描述了不支持的拓扑的解决方法。 

|    拓扑    |    支持    |     解决方法    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    连接到本地 VNet 中的卷    |    是    |         |
|    与对等 VNet 中的卷的连接（同一区域）    |    是    |         |
|    与对等 VNet 中的卷的连接（跨区域或全局对等互连）    |    否    |    无    |
|    通过 ExpressRoute 网关连接到卷    |    是    |         |
|    通过 ExpressRoute 网关在分支 VNet 中从本地到卷的连接，以及与网关传输的 VNet 对等互连    |    是    |        |
|    通过 VPN 网关从本地连接到分支 VNet 中的卷    |    是    |         |
|    通过 VPN 网关与分支 VNet 中的卷连接，以及网关传输的 VNet 对等互连    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的虚拟网络

本节介绍帮助您进行虚拟网络规划的概念。

### <a name="azure-virtual-networks"></a>Azure 虚拟网络

在预配 Azure NetApp 文件卷之前，需要创建 Azure 虚拟网络 （VNet） 或使用订阅中已存在的虚拟网络。 VNet 定义卷的网络边界。  有关创建虚拟网络的详细信息，请参阅 Azure[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子网

子网将虚拟网络分割成单独的地址空间，其中的 Azure 资源可以使用这些地址空间。  Azure NetApp 文件卷包含在称为[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)的特殊用途子网中。 

子网委派向 Azure NetApp 文件服务授予显式权限，用于在子网中创建特定于服务的资源。  它在部署服务时使用唯一标识符。 在这种情况下，将创建一个网络接口，以启用与 Azure NetApp 文件的连接。

如果使用新的 VNet，则可以创建子网，并通过在[将子网委派到 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)中的说明将子网委派给 Azure NetApp 文件。 您还可以将尚未委派给其他服务的现有空子网委派给其他服务。

如果 VNet 与另一个 VNet 对等，则无法展开 VNet 地址空间。 因此，需要在 VNet 地址空间内创建新的委派子网。 如果需要扩展地址空间，则必须在扩展地址空间之前删除 VNet 对等互连。

### <a name="udrs-and-nsgs"></a>UdRs 和 NSG

Azure NetApp 文件的委派子网不支持用户定义的路由 （UDR） 和网络安全组 （NSG）。

作为解决方法，您可以将 NSG 应用于允许或拒绝往来 Azure NetApp 文件委派子网的其他子网。  

## <a name="azure-native-environments"></a>Azure 本机环境

下图说明了 Azure 本机环境：

![Azure 本机网络环境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是从同一 VNet 中的虚拟机 （VM） 创建或连接到 Azure NetApp 文件卷。 对于上图中的 VNet 2，卷 1 在委派子网中创建，可以安装在默认子网中的 VM 1 上。

### <a name="vnet-peering"></a>VNet 对等互连

如果同一区域中的其他 VNet 需要访问彼此的资源，则可以使用[VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)连接 VNet，以便通过 Azure 基础结构实现安全连接。 

考虑上图中的 VNet 2 和 VNet 3。 如果 VM 1 需要连接到 VM 2 或卷 2，或者如果 VM 2 需要连接到 VM 1 或卷 1，则需要在 VNet 2 和 VNet 3 之间启用 VNet 对等互连。 

此外，请考虑一种方案，其中 VNet 1 与 VNet 2 对等，而 VNet 2 与同一区域中的 VNet 3 对等。 VNet 1 的资源可以连接到 VNet 2 中的资源，但除非对等 VNet 1 和 VNet 3 进行对等，否则无法连接到 VNet 3 中的资源。 

在上图中，尽管 VM 3 可以连接到卷 1，但 VM 4 无法连接到卷 2。  原因是分支 VNet 未对等，并且_通过 VNet 对等互连不支持传输路由_。

## <a name="hybrid-environments"></a>混合环境

下图说明了混合环境： 

![混合网络环境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合方案中，来自本地数据中心的应用程序需要访问 Azure 中的资源。  无论是要将数据中心扩展到 Azure，还是使用 Azure 本机服务还是进行灾难恢复，都是这种情况。 有关如何通过站点到站点 VPN 或 ExpressRoute 将本地多个资源连接到 Azure 中的资源的信息，请参阅[VPN 网关规划选项](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合中心辐条拓扑中，Azure 中的中心 VNet 充当与本地网络的连接中心点。 分支与集线器对等，可用于隔离工作负载。

根据配置，您可以将本地资源连接到集线器和分支中的资源。

在上图所示的拓扑中，本地网络连接到 Azure 中的中心 VNet，并且在同一区域中有 2 个分支 VNet 与中心 VNet 对等互连。  在这种情况下，Azure NetApp 文件卷支持的连接选项如下所示：

* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 或 ExpressRoute 电路连接到集线器中的卷 1。 
* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 和区域 Vnet 对等互连连接到卷 2 或卷 3。
* 中心 VNet 中的 VM 3 可以连接到分支 VNet 1 中的卷 2 和分支 VNet 2 中的卷 3。
* 来自分支 VNet 1 的 VM 4 和分支 VNet 2 的 VM 5 可以连接到中心 VNet 中的卷 1。

分支 VNet 1 中的 VM 4 无法连接到分支 VNet 2 中的卷 3。 此外，分支 VNet2 中的 VM 5 无法连接到分支 VNet 1 中的卷 2。 这是这种情况，因为分支 VNet 不对等，并且_通过 VNet 对等互连不支持传输路由_。

## <a name="next-steps"></a>后续步骤

[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
