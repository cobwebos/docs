---
title: 为 VPN 网关配置 BGP：门户
titleSuffix: Azure VPN Gateway
description: 本文逐步讲解如何使用 PowerShell 通过 Azure VPN 网关配置 BGP。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: f52d684d1e6ef63fdf4287c610608061f30395f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994280"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>如何在 Azure VPN 网关上配置 BGP

本文指导完成使用 Azure 门户在跨界站点到站点 (S2S) VPN 连接和 VNet 到 VNet 连接上启用 BGP 的步骤。

## <a name="about-bgp"></a><a name="about"></a>关于 BGP

BGP 是通常在 Internet 上使用的，用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 BGP 允许 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）交换 "路由"，这些路由将通知这两个网关的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点来允许在多个网络之间传输路由。

有关 BGP 的优点以及如何使用 BGP 的技术要求和注意事项的详细信息，请参阅 [使用 AZURE VPN 网关的 BGP 概述](vpn-gateway-bgp-overview.md)。

## <a name="getting-started"></a>入门

本文的每个部分都可帮助你形成基本构建基块，以便在网络连接中启用 BGP。 如果完成所有这三个部分，将生成拓扑，如图1所示。

**图示 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="显示网络体系结构和设置的图示" border="false":::

可以将这些部分组合在一起，生成更复杂的多跃点传输网络，以满足需求。

### <a name="prerequisites"></a>必备条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>第1部分：在虚拟网络网关上配置 BGP

在本部分中，将创建和配置虚拟网络，创建和配置具有 BGP 参数的虚拟网络网关，并获取 Azure BGP 对等节点 IP 地址。 图2显示了在使用本节中的步骤时要使用的配置设置。

**图示 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="显示网络体系结构和设置的图示" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. 创建并配置 TestVNet1

在此步骤中，你将创建并配置 TestVNet1。 使用 [创建网关教程](vpn-gateway-tutorial-create-gateway-powershell.md) 中的步骤创建和配置 Azure 虚拟网络和 VPN 网关。 使用下面屏幕截图中的引用设置。

* 虚拟网络：

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="显示网络体系结构和设置的图示":::

* 子网：

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="显示网络体系结构和设置的图示":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. 通过 BGP 参数为 TestVNet1 创建 VPN 网关

在此步骤中，将使用相应的 BGP 参数创建 VPN 网关。

1. 在 Azure 门户中，导航到 Marketplace 中的 **虚拟网络网关** 资源，然后选择 " **创建**"。

1. 填写参数，如下所示：

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="显示网络体系结构和设置的图示" 部分中，配置以下设置：

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="显示网络体系结构和设置的图示" 字段是可选的。 如果本地 VPN 设备使用 APIPA 地址作为 BGP，则必须从 **169.254.21.0** 到 **169.254.22.255**的 VPN 的 Azure 保留 APIPA 地址范围中选择一个地址。 此示例使用169.254.21.11。

   * 如果要创建主动-主动 VPN 网关，BGP 部分将显示另一个 **第二个自定义 AZURE APIPA BGP IP 地址**。 指定 (**169.254.21.0** 到 **169.254.22.255**) 的允许的 APIPA 范围内的不同地址。

   > [!IMPORTANT]
   >
   > * 默认情况下，Azure 会将 GatewaySubnet 前缀范围内的专用 IP 地址作为 azure BGP IP 地址自动分配给 Azure VPN 网关。 如果本地 VPN 设备使用 APIPA 地址 (169.254.0.1 到 169.254.255.254) 作为 BGP IP，则需要自定义 Azure APIPA BGP 地址。 如果相应的本地网络网关资源 (本地网络) 具有作为 BGP 对等节点的 APIPA 地址，Azure VPN 网关将选择自定义 APIPA 地址。 如果本地网络网关使用常规 IP 地址 (不是 APIPA) ，则 Azure VPN 网关将恢复为 GatewaySubnet 范围内的专用 IP 地址。
   >
   > * APIPA BGP 地址不得在本地 VPN 设备和所有连接的 Azure VPN 网关之间重叠。
   >

1. 选择“查看 + 创建”  ，运行验证。 验证通过后，选择“创建”  以部署 VPN 网关。 网关可能需要长达 45 分钟才能完全创建和部署。 可以在网关的“概述”页上查看部署状态。

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. 获取 Azure BGP 对等节点 IP 地址

创建网关后，可以在 Azure VPN 网关上获取 BGP 对等节点 IP 地址。 需要这些地址来配置本地 VPN 设备，以便与 Azure VPN 网关建立 BGP 会话。

1. 导航到 "虚拟网络网关" 资源，然后选择 " **配置** " 页以查看 BGP 配置信息，如以下屏幕截图所示。 在此页上，可以查看 Azure VPN 网关上的所有 BGP 配置信息： ASN、公共 IP 地址，以及 Azure 端 (默认和 APIPA) 的相应 BGP 对等节点 IP 地址。

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="显示网络体系结构和设置的图示" 以将更改提交到 Azure VPN 网关。

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>第2部分：在跨界 S2S 连接上配置 BGP

要建立跨界连接，需要创建 *本地网关* 来表示本地 VPN 设备，并使用 *连接* 来连接 VPN 网关和本地网络网关，如 [创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)中所述。 本文包含指定 BGP 配置参数所需的其他属性。

**图示 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="显示网络体系结构和设置的图示" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. 在本地网络网关上配置 BGP

在此步骤中，你将在本地网络网关上配置 BGP。 使用以下屏幕截图作为示例。 屏幕截图显示本地网络网关 (Site5) ，其中包含关系图3中指定的参数。

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="显示网络体系结构和设置的图示":::

#### <a name="important-configuration-considerations"></a>重要的配置注意事项

* ASN 和 BGP 对等节点的 IP 地址必须与本地 VPN 路由器配置匹配。
* 仅当使用 BGP 连接到此网络时，才可将 **地址空间** 留空。 Azure VPN 网关会将 BGP 对等节点 IP 地址的路由添加到相应的 IPsec 隧道。 如果 **未** 在 Azure VPN 网关与此特定网络之间使用 BGP，则 **必须** 提供 **地址空间**的有效地址前缀列表。
* 如果需要，你可以选择使用 **APIPA IP 地址** (169.254.) 作为本地 BGP 对等节点 ip。 但对于 Azure VPN 网关，还需要指定 APIPA IP 地址，如本文前面所述，否则无法建立此连接的 BGP 会话。
* 你可以在创建本地网络网关的过程中输入 BGP 配置信息，也可以从本地网络网关资源的 " **配置** " 页添加或更改 bgp 配置。

**示例**

此示例使用 APIPA 地址 (169.254.100.1) 作为本地 BGP 对等节点 IP 地址：

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="显示网络体系结构和设置的图示":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. 配置已启用 BGP 的 S2S 连接

在此步骤中，将创建启用了 BGP 的新连接。 如果已建立连接，并且想要在其上启用 BGP，则可以 [更新现有连接](#update)。

#### <a name="to-create-a-connection-with-bgp-enabled"></a>创建启用了 BGP 的连接

若要创建启用了 BGP 的新连接，请在 " **添加连接** " 页上填写值，然后选中 " **启用 bgp** " 选项以在此连接上启用 bgp。 选择“确定”以创建连接。

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="显示网络体系结构和设置的图示":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>更新现有连接

如果要更改连接上的 BGP 选项，请导航到连接资源的 " **配置** " 页，然后切换 **BGP** 选项，如以下示例中突出显示的那样。 选择“保存”  以保存所有更改。

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="显示网络体系结构和设置的图示":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>第3部分：在 VNet 到 VNet 连接上配置 BGP

在 VNet 到 VNet 连接上启用或禁用 BGP 的步骤与 [第2部分](#crosspremises)中的 S2S 步骤相同。 可以在创建连接时启用 BGP，或更新现有 VNet 到 VNet 连接上的配置。

>[!NOTE] 
>不使用 BGP 的 VNet 到 VNet 连接会将通信限制为仅限两个连接的 Vnet。 启用 BGP，允许传输路由功能连接到这两个 Vnet 的其他 S2S 或 VNet 到 VNet 连接。
>

对于上下文，如果要在 TestVNet2 和 TestVNet1 之间禁用 **BGP，则**TestVNet2 不会获知本地网络的路由，Site5，因此无法与站点5通信。 启用 BGP 后（如图4所示），所有三个网络都将能够通过 IPsec 和 VNet 到 VNet 连接进行通信。

**图4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="显示网络体系结构和设置的图示" border="false":::

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/windows/quick-create-portal.md) 以获取相关步骤。
