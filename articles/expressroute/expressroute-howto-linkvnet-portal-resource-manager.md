---
title: 教程：将 VNet 链接到 ExpressRoute 线路-Azure 门户
description: 本教程介绍如何创建一个连接，以便使用 Azure 门户将虚拟网络链接到 Azure ExpressRoute 线路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: bfb358694cbdd214490fb41052e508b94d10baf4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772994"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>教程：使用门户将虚拟网络连接到 ExpressRoute 线路

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
> 

本教程将帮助你创建一个使用 Azure 门户将虚拟网络链接到 Azure ExpressRoute 线路的连接。 连接到 Azure ExpressRoute 线路的虚拟网络可以在同一个订阅中，也可以属于另一个订阅。

在本教程中，了解如何：
> [!div class="checklist"]
> - 将虚拟网络连接到同一订阅中的线路。
> - 将虚拟网络连接到其他订阅中的线路。
> - 删除虚拟网络与 ExpressRoute 线路之间的链接。

## <a name="prerequisites"></a>先决条件

* 在开始配置之前，请先查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。

* 必须有一个活动的 ExpressRoute 线路。
  * 请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，并通过连接提供商启用该线路。
  * 请确保为线路配置 Azure 专用对等互连。 有关对等互连和路由说明，请参阅[为 ExpressRoute 线路创建和修改对等互连](expressroute-howto-routing-portal-resource-manager.md)一文。
  * 请确保已配置 Azure 专用对等互连，并建立网络和 Microsoft 之间的 BGP 对等互连，以便进行端到端连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 按照说明[创建 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 虚拟网络网关使用的 GatewayType 是“ExpressRoute”而非 VPN。

* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。

* 单个 VNet 可最多连接到 4 条 ExpressRoute 线路。 使用以下过程为要连接到的每个 ExpressRoute 线路创建新的连接对象。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。

* 如果启用 ExpressRoute 高级版外接程序，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络。 高级外接程序还允许根据所选带宽将10个以上的虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

* 为了更好地了解这些步骤，可以在开始之前[观看视频](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)。

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>将 VNet 连接到线路 - 同一订阅

> [!NOTE]
> 如果第3层提供商配置了对等互连，则不会显示 BGP 配置信息。 如果线路处于已预配状态，应该能够创建连接。
>

### <a name="to-create-a-connection"></a>创建连接

1. 确保已成功配置 ExpressRoute 线路和 Azure 专用对等互连。 按照 [创建 expressroute 线路](expressroute-howto-circuit-arm.md) 中的说明进行操作，并 [为 ExpressRoute 线路创建和修改对等互连](expressroute-howto-routing-arm.md)。 ExpressRoute 线路应如下图所示：

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**" 页，然后配置这些值。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

3. 成功配置连接之后，连接对象会显示连接的信息。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>将 VNet 连接到线路 - 不同订阅

用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

  > [!NOTE]
  > 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>管理 - 关于线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由线路用户兑换的授权。 线路用户是虚拟网络网关的所有者，它们不在与 ExpressRoute 线路相同的订阅中。 线路用户可以兑换授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤消授权。 撤消授权会导致从已撤消访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**若要创建连接授权**

线路所有者创建授权，该授权将创建线路用户用于将其虚拟网络网关连接到 ExpressRoute 线路的授权密钥。 一个授权只可用于一个连接。

> [!NOTE]
> 每个连接都需要单独授权。
>

1. 在 "ExpressRoute" 页上，选择 " **授权** "，然后键入授权的 **名称** 并选择 " **保存**"。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

2. 保存配置后，复制“资源 ID”和“授权密钥”。********

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

**若要删除连接授权**

可以通过在你的连接的页面上单击“删除”**** 图标来删除连接。

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需有资源 ID 以及线路所有者提供的授权密钥。

**若要兑换连接授权**

1. 选择 " **+ 新建** " 按钮。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**" **创建**"。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**" 页中选择 **"确定"** 。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**":::

5. 在“设置”页面中，选择“虚拟网络网关”并选中“兑换授权”复选框。************
6. 输入“授权密钥”和“对等线路 URI”，并为连接命名。******** 选择“确定”****。 **对等线路 URI** 是 ExpressRoute 线路的资源 ID（可以在 ExpressRoute 线路的“属性设置”窗格下找到）。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="ExpressRoute 线路屏幕截图&quot;:::

2. 现在可以开始预配连接，以便将虚拟网络网关链接到 ExpressRoute 线路。 选择 &quot;**连接**&quot;  >  &quot;**添加**&quot; 以打开 &quot;**添加连接**" 页中的信息，然后选择 **"确定"**。

**若要释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="clean-up-resources"></a>清理资源

可以通过在你的连接的页面上单击“删除”**** 图标来取消 VNet 到 ExpressRoute 的链接。

## <a name="next-steps"></a>后续步骤

在本教程中，已了解如何将虚拟网络连接到同一订阅中的线路和其他订阅。 有关 ExpressRoute 网关的详细信息，请参阅： 

> [!div class="nextstepaction"]
> [关于 ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md)
