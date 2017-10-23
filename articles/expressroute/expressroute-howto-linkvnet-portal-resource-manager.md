---
title: "将虚拟网络链接到 ExpressRoute 线路：Azure 门户 | Microsoft Docs"
description: "本文档概述了如何将虚拟网络 (VNet) 链接到 ExpressRoute 线路。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
ms.openlocfilehash: 34caed9323ae3067d1dc17ab9c62ebf7a9be855b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>将虚拟网络连接到 ExpressRoute 线路
> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
> 

本文有助于使用资源管理器部署模型和 Azure 门户将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 虚拟网络可以在同一个订阅中，也可以属于另一个订阅。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请先查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 必须有一个活动的 ExpressRoute 线路。
  
  * 请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，并通过连接提供商启用该线路。
  * 请确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](expressroute-howto-routing-portal-resource-manager.md)一文。
  * 确保配置 Azure 专用对等互连并运行网络和 Microsoft 之间的 BGP 对等互连，以便启用端到端连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 按照说明[创建 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 虚拟网络网关使用的 GatewayType 是“ExpressRoute”而非 VPN。

* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。 
* 如果已启用 ExpressRoute 高级外接程序，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络，或者将更多虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题](expressroute-faqs.md)。
* 为了更好地了解这些步骤，可以在开始之前[观看视频](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路

### <a name="to-create-a-connection"></a>创建连接

> [!NOTE]
> 如果第 3 层提供商配置了对等互连，则 BGP 配置信息将不会显示。 如果线路处于已预配状态，应该能够创建连接。
>

1. 确保已成功配置 ExpressRoute 线路和 Azure 专用对等互连。 请遵循[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)和[配置路由](expressroute-howto-routing-arm.md)中的说明。 ExpressRoute 线路应如下图所示：

    ![ExpressRoute 线路屏幕截图](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 单击“连接” > “添加”以打开“添加连接”边栏选项卡，并配置值。

    ![添加连接屏幕截图](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. 成功配置连接之后，连接对象会显示连接的信息。

     ![连接对象屏幕截图](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>删除连接
可以通过选择连接的边栏选项卡上的“删除”图标来删除连接。

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将另一订阅中的虚拟网络连接到线路
用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

![跨订阅连接](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- 大型云中的每个较小云用于表示属于组织中不同部门的订阅。
- 组织中的每个部门可以使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。
- 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路和关联到该线路的授权，包括链接到其他 Azure Active Directory 租户和企业协议合约的订阅。 

    > [!NOTE]
    > 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
    > 
    >

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由线路用户兑换的授权。 线路用户是虚拟网络网关的所有者（这些网关与 ExpressRoute 线路位于不同的订阅中）。 线路用户可以兑现授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤消授权。 撤消授权将导致从撤消了访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**若要创建连接授权**

线路所有者创建授权。 这样即可创建授权密钥，供线路用户用来将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

1. 在“ExpressRoute”边栏选项卡中单击“授权”，键入授权的**名称**并单击“保存”。

    ![授权](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. 保存配置后，复制“资源 ID”和“授权密钥”。

    ![授权密钥](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**若要删除连接授权**

可以通过选择连接的边栏选项卡上的“删除”图标来删除连接。

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需有资源 ID 以及线路所有者提供的授权密钥。 

**若要兑换连接授权**

1.  单击“+新建”按钮。

    ![单击“新建”](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.  在 Marketplace 中搜索“连接”，选择它并单击“创建”。

    ![搜索连接](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.  确保“连接类型”设置为“ExpressRoute”。


4.  在“基本信息”边栏选项卡中填写详细信息，并单击“确定”。

    ![“基本信息”边栏选项卡](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.  在“设置”边栏选项卡中，选择“虚拟网络网关”并选中“兑换授权”复选框。

6.  输入“授权密钥”和“对等线路 URI”，并为连接命名。 单击 **“确定”**。

    ![设置边栏选项卡](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. 在“摘要”边栏选项卡中检查信息，并单击“确定”。


**若要释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="next-steps"></a>后续步骤
有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
