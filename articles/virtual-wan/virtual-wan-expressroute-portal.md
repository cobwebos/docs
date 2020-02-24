---
title: 教程 - 使用 Azure 虚拟 WAN 创建 ExpressRoute 连接
description: 在本教程中，你将了解如何使用 Azure 虚拟 WAN 创建与 Azure 和本地环境的 ExpressRoute 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209420"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建 ExpressRoute 关联

本教程演示如何使用虚拟 WAN 通过 ExpressRoute 线路来连接到 Azure 中的资源。 有关虚拟 WAN 和 虚拟 WAN 资源的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟 WAN
> * 创建中心和网关
> * 将 VNet 连接到中心
> * 将线路连接到中心网关
> * 测试连接
> * 更改网关大小
> * 播发默认路由

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* ExpressRoute 线路必须是高级线路，才能连接到中心网关。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="openvwan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”  。 在搜索框中键入“虚拟 WAN”  ，然后选择 Enter。
2. 从结果中选择“虚拟 WAN”  。 在“虚拟 WAN”页上，单击“创建”以打开“创建 WAN”页  。
3. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段   ：

   ![创建 WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型** - 选择“标准”  。 不能使用基本 SKU 创建 ExpressRoute 网关。
4. 填写完字段后，单击“审阅 + 创建”  。
5. 验证通过后，选择“创建”以创建虚拟 WAN  。

## <a name="hub"></a>创建虚拟中心和网关

虚拟中心是虚拟 WAN 创建和使用的虚拟网络。 它可以包含各种网关，如 VPN 和 ExpressRoute。 在本部分中，将为虚拟中心创建 ExpressRoute 网关。 你可以在[创建新虚拟中心](#newhub)时创建网关，或者可以通过编辑[现有中心](#existinghub)，在现有中心创建网关。 

ExpressRoute 网关以 2 Gbps 为单位进行预配。 1 个缩放单元= 2 Gbps，最多支持 10 个缩放单元 = 20 Gbps。 完全创建虚拟中心和网关大约需要 30 分钟。

### <a name="newhub"></a>创建新的虚拟中心和网关

创建新的虚拟中心。 创建中心后，即使你没有附加任何站点，也会对该中心收取费用。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>在现有中心中创建网关

还可以通过编辑现有中心，在现有中心创建网关。

1. 导航到要编辑的虚拟中心，然后选择它。
2. 在“编辑虚拟中心”页上，选中“包括 ExpressRoute 网关”复选框   。
3. 选择“确认”以确认所做的更改  。 完全创建中心和中心资源大约需要 30 分钟。

   ![现有中心](./media/virtual-wan-expressroute-portal/edithub.png "编辑中心")

### <a name="to-view-a-gateway"></a>查看网关

创建 ExpressRoute 网关后，可以查看网关详细信息。 导航到中心，选择“ExpressRoute”，然后查看网关  。

![查看网关](./media/virtual-wan-expressroute-portal/viewgw.png "查看网关")

## <a name="connectvnet"></a>将 VNet 连接到中心

此步骤在中心与 VNet 之间创建对等互连。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。 
2. 在虚拟网络连接页上，单击“+添加连接”。 
3. 在“添加连接”页上填写以下字段  ：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关（既不能是 VPN 也不能是 ExpressRoute）。

## <a name="connectcircuit"></a>将线路连接到中心网关

创建网关后，就可以将 [ExpressRoute 线路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)连接到该网关。 ExpressRoute Global Reach 支持的位置中的 ExpressRoute 高级版线路可以连接到虚拟 WAN ExpressRoute 网关。

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>将线路连接到中心网关

在门户中，转到“虚拟中心”->“连接性”->“ExpressRoute”页面  。 如果可以在订阅访问 ExpressRoute 线路，将在线路列表中看到要使用的线路。 如果没有看到任何线路，但已获得授权密钥和对等线路 URI，则可以兑换并连接线路。 请参阅[通过兑换授权密钥进行连接](#authkey)。

1. 选择线路。
2. 选择“连接线路”  。

   ![连接线路](./media/virtual-wan-expressroute-portal/cktconnect.png "连接线路")

### <a name="authkey"></a>通过兑换授权密钥进行连接

使用提供的授权密钥和线路 URI 进行连接。

1. 在 ExpressRoute 页面上，单击“+ 兑换授权密钥” 

   ![兑换](./media/virtual-wan-expressroute-portal/redeem.png "兑换")
2. 在“兑换授权密钥”页上，填写值。

   ![兑换密钥值](./media/virtual-wan-expressroute-portal/redeemkey2.png "兑换密钥值")
3. 选择“添加”以添加密钥  。
4. 查看线路。 兑换线路只显示名称（不显示类型、提供程序和其他信息），因为它与用户的订阅不同。

## <a name="to-test-connectivity"></a>测试连接

建立线路连接后，中心连接状态将指示“此中心”，这意味着已建立通向中心 ExpressRoute 网关的连接。 等待大约 5 分钟，然后再测试 ExpressRoute 线路后面的客户端（例如，先前创建的 VNet 中的 VM）的连接性。

如果你的站点连接到与 ExpressRoute 网关位于同一中心的虚拟 WAN VPN 网关，则可以在 VPN 和 ExpressRoute 终结点之间进行双向连接。 支持动态路由 (BGP)。 中心网关的 ASN 是固定的，此时无法编辑。

## <a name="to-change-the-size-of-a-gateway"></a>更改网关的大小

如果要更改 ExpressRoute 网关的大小，请在中心内找到 ExpressRoute 网关，然后从下拉列表中选择缩放单元。 保存所做更改。 更新中心网关需要大约 30 分钟。

![更改网关大小](./media/virtual-wan-expressroute-portal/changescale.png "更改网关大小")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>向终结点播发默认路由 0.0.0.0/0

如果想要 Azure 虚拟中心将默认路由 0.0.0.0/0 播发到 ExpressRoute 终结点，则需要启用“传播默认路由”。

1. 选择“线路”->“…”->“编辑连接”  。

   ![编辑连接](./media/virtual-wan-expressroute-portal/defaultroute1.png "编辑连接")

2. 选择“启用”来传播默认路由  。

   ![传播默认路由](./media/virtual-wan-expressroute-portal/defaultroute2.png "传播默认路由")

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
