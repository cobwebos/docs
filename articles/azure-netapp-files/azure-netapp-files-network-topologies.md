---
title: Azure NetApp 文件网络规划指南 | Microsoft Docs
description: 介绍可帮助你使用 Azure NetApp 文件设计有效网络体系结构的指南。
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
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: a8d81acc0fcb4afa0f981fca3fd099296a0361df
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569486"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp 文件网络规划指南

网络体系结构规划是设计任何应用程序基础结构的关键要素。 本文可帮助你设计有效的网络体系结构，使你的工作负荷能够受益于 Azure NetApp 文件丰富的功能。

Azure NetApp 文件卷设计为包含在 Azure 虚拟网络中名为[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)这一特殊用途的子网内。 因此，你可以根据需要通过虚拟网络网关（ExpressRoute 或 VPN 网关）直接从你的 VNet、从同一区域中的对等互连 VNet 或从本地访问这些卷。 此子网专用于 Azure NetApp 文件，并且没有与其他 Azure 服务或 Internet 建立连接。

## <a name="considerations"></a>注意事项  

在规划 Azure NetApp 文件网络时应了解一些注意事项。

### <a name="constraints"></a>约束

Azure NetApp 文件目前不支持以下功能： 

* 应用于委派子网的网络安全组 (NSG)
* 应用于委派子网的用户定义的路由 (UDR)
* 针对 Azure NetApp 文件接口的 Azure 策略（例如自定义命名策略）
* Azure NetApp 文件流量的负载均衡器
* Azure 虚拟 WAN 
* 区域冗余虚拟网络网关（使用 Az 的网关 SKU） 
* 主动/主动虚拟网络 GW 
* 用于 IPv4 和 IPv6 的双堆栈 () VNet

以下网络限制适用于 Azure NetApp 文件：

* 含有 Azure NetApp 文件（包括对等互连 VNet）的 VNet 中使用的 IP 数量不能超过 1000。 我们正在努力提高此限制以满足客户缩放需求。 
* 在每个 Azure 虚拟网络 (VNet) 中，只能将一个子网委派给 Azure NetApp 文件。


### <a name="supported-network-topologies"></a>支持的网络拓扑

下表描述了 Azure NetApp 文件支持的网络拓扑。  它还描述了针对不受支持的拓扑的解决方法。 

|    拓扑    |    支持    |     解决方法    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    连接至本地 VNet 中的卷    |    是    |         |
|    连接至对等互连 VNet 中的卷（同一区域）    |    是    |         |
|    连接至对等互连 VNet 中的卷（跨区域或全球对等互连）    |    否    |    无    |
|    通过 ExpressRoute 网关连接至卷    |    是    |         |
|    通过 ExpressRoute 网关和带有网关传输的 VNet 对等互连从本地连接至辐射 VNet 中的卷    |    是    |        |
|    通过 VPN 网关从本地连接至辐射 VNet 中的卷    |    是    |         |
|    通过 VPN 网关和带有网关传输的 VNet 对等互连从本地连接至辐射 VNet 中的卷    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的虚拟网络

本部分介绍可帮助你进行虚拟网络规划的概念。

### <a name="azure-virtual-networks"></a>Azure 虚拟网络

在预配 Azure NetApp 文件卷之前，需要先创建 Azure 虚拟网络 (VNet) 或使用订阅中已存在的虚拟网络。 VNet 定义卷的网络边界。  有关创建虚拟网络的详细信息，请参阅 [Azure 虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子网

子网将虚拟网络分段成独立的地址空间，这些地址空间可供里面的 Azure 资源使用。  Azure NetApp 文件卷包含在名为[委派子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)这一特殊用途的子网内。 

子网委派为 Azure NetApp 文件服务提供了显式权限，以便在子网中创建服务特定的资源。  它在部署服务时会使用唯一标识符。 在这种情况下，将创建一个网络接口以实现到 Azure NetApp 文件的连接。

如果使用新的 VNet，可以按照[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)中的说明创建子网并将其委派给 Azure NetApp 文件。 还可以将未委派的现有的空子网委派给其他服务。

如果此 VNet 与其他 VNet 对等互连，你则不能扩展此 VNet 地址空间。 因此，需要在此 VNet 地址空间内创建新的委派子网。 如果需要扩展地址空间，则必须在扩展地址空间之前删除 VNet 对等互连。

### <a name="udrs-and-nsgs"></a>UDR 和 NSG

适用于 Azure NetApp 文件的委派子网不支持用户定义的路由 (UDR) 和网络安全组 (NSG)。 但是，你可以将 UDR 和 NSG 应用于其他子网，即使这些子网与委派给 Azure NetApp 文件的子网位于同一 VNet 中也可如此。

* UDR 会定义从其他子网流向 Azure NetApp 文件委派子网的流量。 这有助于确保与使用系统路由使流量从 Azure NetApp 文件流回其他子网保持一致。  
* NSG 会允许或拒绝流量进出 Azure NetApp 文件委派子网。 

## <a name="azure-native-environments"></a>Azure 原生环境

下图展现了 Azure 本机环境：

![Azure 本机网络环境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是从同一 VNet 中的虚拟机 (VM) 创建或连接到 Azure NetApp 文件卷。 对于上图中的 VNet 2 而言，卷 1 是在委派子网中创建的，并且可以装入到默认子网中的 VM 1 上。

### <a name="vnet-peering"></a>VNet 对等互连

如果同一区域中有需要访问彼此资源的其他 VNet，可以使用 [VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)来连接这些 VNet，从而通过 Azure 基础结构实现安全连接。 

请考虑上图中的 VNet 2 和 VNet 3。 如果 VM 1 需要连接到 VM 2 或卷 2，或者 VM 2 需要连接到 VM 1 或卷 1，则需要在 VNet 2 和 VNet 3 之间启用 VNet 对等互连。 

此外，请考虑一种方案，其中 VNet 1 与 VNet 2 对等互连，而 VNet 2 在同一区域中与 VNet 3 对等互连。 VNet 1 中的资源可以连接到 VNet 2 中的资源，但无法连接到 VNet 3 中的资源，除非 VNet 1 和 VNet 3 是对等互连的。 

在上图中，尽管 VM 3 可以连接到卷 1，但 VM 4 无法连接到卷 2。  导致出现这种情况是因为辐射 VNet 不是对等互连的，并且不支持通过 VNet 对等互连实现传输路由。

## <a name="hybrid-environments"></a>混合环境

下图展现了混合环境： 

![混合网络环境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合方案中，本地数据中心内的应用程序需要访问 Azure 中的资源。  无论是要将数据中心扩展到 Azure，还是要使用 Azure 本机服务或进行灾难恢复，都是如此。 有关如何通过站点到站点 VPN 或 ExpressRoute 将多个资源本地连接到 Azure 中的资源的信息，请参阅 [VPN 网关规划选项](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合中心辐射型拓扑中，Azure 中的中心 VNet 充当到本地网络的连接的中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。

根据配置，可以将本地资源连接到中心和辐射中的资源。

在上面所示的拓扑中，本地网络连接到 Azure 中的中心 VNet，同一区域中有 2 个辐射 VNet 与此中心 VNet 对等互连。  在此方案中，Azure NetApp 文件卷支持的连接选项如下所示：

* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 或 ExpressRoute 线路连接到中心中的卷 1。 
* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 和区域 Vnet 对等互连连接到卷 2或卷 3。
* 中心 VNet 中的 VM 3 可以连接到辐射 VNet 1 中的卷 2 以及辐射 VNet 2 中的卷 3。
* 辐射 VNet 1 中的 VM 4 和辐射 VNet 2 中的 VM 5 可以连接到中心 VNet 中的卷 1。
* 辐射 VNet 1 中的 VM 4 无法连接到辐射 VNet 2 中的卷 3。 此外，辐射 VNet2 中的 VM 5 无法连接到辐射 VNet 1 中的卷 2。 这是因为辐射 VNet 不是对等互连的，并且不支持通过 VNet 对等互连实现传输路由。
* 在上述体系结构中，如果辐射 VNET 中还存在一个网关，那么此中心中通过网关从本地到 ANF 卷的连接将丢失。 按照设计，首选连接将提供给辐射 VNet 中的网关，因此只有通过该网关连接的计算机才能连接到 ANF 卷。

## <a name="next-steps"></a>后续步骤

[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
