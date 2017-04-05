---
title: "如何为 ExpressRoute 线路配置路由（对等互连）：Resource Manager：Azure | Microsoft Docs"
description: "本文将指导你完成创建和预配 ExpressRoute 线路的专用、公共和 Microsoft 对等互连的步骤。 本文还介绍了如何检查状态，以及如何更新或删除线路的对等互连。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5e33ffc313e7edfcf7243f1113e07b215dc5b10b
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>创建和修改 ExpressRoute 线路的对等互连
> [!div class="op_single_selector"]
> * [资源管理器 - Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [经典 - PowerShell](expressroute-howto-routing-classic.md)
> * [视频 - 专用对等互连](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [视频 - 公共对等互连](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [视频 - Microsoft 对等互连](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

本文将指导你执行相关步骤，以便使用 Azure 门户和 Resource Manager 部署模型创建和管理 ExpressRoute 线路的路由配置。

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>配置先决条件
* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)页、[路由要求](expressroute-routing.md)页和[工作流](expressroute-workflows.md)页。
* 你必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md) ，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配和已启用状态，你才能运行下述 cmdlet。
* 如果计划使用共享密钥/MD5 哈希，请确保在隧道两端都使用该哈希，并将最大字符数限制为 25。

这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果你的服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商将为你设置和管理路由。 

> [!IMPORTANT]
> 我们目前无法通过服务管理门户播发服务提供商配置的对等互连。 我们正在努力不久就实现这一功能。 请在配置 BGP 对等互连之前与服务提供商核对。
> 
> 

你可以为 ExpressRoute 线路配置一到三个对等互连（Azure 专用、Azure 公共和 Microsoft）。 可以按照所选的任意顺序配置对等互连。 但是，你必须确保一次只完成一个对等互连的配置。 

## <a name="azure-private-peering"></a>Azure 专用对等互连
本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。 

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连
1. 配置 ExpressRoute 线路。 在继续之前，请确保线路完全由连接提供商设置。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，请确保已准备好以下各项：
   
   * 主链路的 /30 子网。 它不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 辅助链路的 /30 子网。 它不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以将专用 AS 编号用于此对等互连。 请务必不要使用 65515。
   * MD5 哈希（如果选择使用）。 **这是可选的**。
3. 选择 Azure 专用对等行，如下所示。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. 配置专用对等互连。 下图显示了一个配置示例。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. 指定所有参数后，请保存配置。 成功接受配置后，你将看到类似于下面示例的内容。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>查看 Azure 专用对等互连详细信息
可以通过选择对等互连查看 Azure 专用对等互连的属性。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>更新 Azure 专用对等互连配置
你可以选择用于对等互连的行并修改对等互连属性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>删除 Azure 专用对等互连
可以通过选择删除图标来删除对等互连配置，如下所示。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Azure 公共对等互连
本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 公共对等互连配置。 

### <a name="to-create-azure-public-peering"></a>创建 Azure 公共对等互连
1. 配置 ExpressRoute 线路。 在进一步继续之前，请确保线路完全由连接提供商设置。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 配置线路的 Azure 公共对等互连。 在继续执行后续步骤之前，请确保已准备好以下各项：
   
   * 主链路的 /30 子网。 
   * 辅助链路的 /30 子网。 
   * 用于设置此对等互连的所有 IP 地址必须都是有效的公共 IPv4 地址。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * MD5 哈希（如果选择使用）。 **这是可选的**。
3. 选择 Azure 公共对等互连行，如下所示。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. 配置公共对等互连。 下图显示了一个配置示例。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. 指定所有参数后，请保存配置。 成功接受配置后，你将看到类似于下面示例的内容。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>查看 Azure 公共对等互连详细信息
可以通过选择对等互连查看 Azure 公共对等互连的属性。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>更新 Azure 公共对等互连配置
你可以选择用于对等互连的行并修改对等互连属性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>删除 Azure 公共对等互连
可以通过选择删除图标来删除对等互连配置，如下所示。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Microsoft 对等互连
本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Microsoft 对等互连配置。 

### <a name="to-create-microsoft-peering"></a>创建 Microsoft 对等互连
1. 配置 ExpressRoute 线路。 在进一步继续之前，请确保线路完全由连接提供商设置。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 配置线路的 Microsoft 对等互连。 在继续下一步之前，请确保已准备好以下信息。
   
   * 主链路的 /30 子网。 这必须是你拥有且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是你拥有且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * **播发的前缀：** 你必须提供要通过 BGP 会话播发的所有前缀列表。 只接受公共 IP 地址前缀。 如果你打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中为你注册。
   * **客户 ASN：** 如果要播发的前缀未注册到对等互连 AS 编号，你可以指定它们要注册到的 AS 编号。 **这是可选的**。
   * **路由注册表名称：** 可以指定 AS 编号和前缀要注册到的 RIR/IRR。 **这是可选的。**
   * MD5 哈希（如果选择使用）。 **这是可选的。**
3. 可以选择想要配置的对等互连，如下所示。 选择 Microsoft 对等互连行。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. 配置 Microsoft 对等互连。 下图显示了一个配置示例。
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. 指定所有参数后，请保存配置。 
   
    如果你的线路到达需要验证的状态（如下所示），则必须打开支持票证以向我们的支持团队显示前缀所有权的证明。    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    你可以如下所示直接从门户打开支持票证     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. 成功接受配置后，你将看到类似于下面示例的内容。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>查看 Microsoft 对等互连详细信息
可以通过选择对等互连查看 Azure 公共对等互连的属性。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>更新 Microsoft 对等互连配置
你可以选择用于对等互连的行并修改对等互连属性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>删除 Microsoft 对等互连
可以通过选择删除图标来删除对等互连配置，如下所示。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>后续步骤
下一步， [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
* 有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。


