---
title: 将虚拟网络网关连接到 Azure 虚拟 WAN |Microsoft Docs
description: 本文介绍如何将 Azure 虚拟网络网关连接到 Azure 虚拟 WAN VPN 网关
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 1f8e0db9921c305edd2ee34efad22cdcf568f8df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514948"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>将 VPN 网关（虚拟网络网关）连接到虚拟 WAN

本文介绍如何设置从 Azure VPN 网关（虚拟网络网关）连接到 Azure 虚拟 WAN （VPN 网关）。 创建从 VPN 网关（虚拟网络网关）到虚拟 WAN （VPN 网关）的连接类似于设置从分支 VPN 站点连接到虚拟 WAN。

为了最大限度地减少两个功能之间可能出现的混淆，我们将在网关前面加上引用的功能的名称。 例如，VPN 网关虚拟网络网关和虚拟 WAN VPN 网关。

## <a name="before-you-begin"></a>开始之前

在开始之前，请创建以下资源：

Azure 虚拟 WAN

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)。
* [创建中心](virtual-wan-site-to-site-portal.md#hub)。 虚拟中心包含虚拟 WAN VPN 网关。

Azure 虚拟网络

* 创建没有任何虚拟网络网关的虚拟网络。 验证本地网络的任何子网是否与要连接到的虚拟网络重叠。 若要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

## <a name="vnetgw"></a>1. 创建 Azure 虚拟网络网关

为虚拟网络为虚拟网络创建 VPN 网关虚拟网络网关。 创建网关时，可以将现有的公共 IP 地址用于网关的两个实例，也可以创建新的公共 IP 地址。 设置虚拟 WAN 站点时，请使用这些公共 Ip。 有关主动-主动模式的详细信息，请参阅[配置主动-主动连接](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active"></a>主动-主动模式设置

![主动-主动](./media/connect-virtual-network-gateway-vwan/active.png "主动-主动")

### <a name="BGP"></a>BGP 设置

BGP ASN 不得为65515。 66515将由 Azure 虚拟 WAN 使用。

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="pip"></a>公共 IP 地址

创建网关后，导航到 "**属性**" 页。 属性和配置设置将类似于下面的示例。 请注意用于网关的两个公共 IP 地址。

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="vwansite"></a>2. 创建虚拟 WAN VPN 站点

若要创建虚拟 WAN VPN 站点，请导航到虚拟 WAN，然后在 "**连接**" 下，选择 " **VPN 站点**"。 在本部分中，你将创建两个与在上一节中创建的虚拟网络网关对应的虚拟 WAN VPN 站点。

1. 选择 " **+ 创建网站**"。
2. 在 "**创建 VPN 站点**" 页上，键入以下值：

   * **区域**-（与 Azure VPN 网关虚拟网络网关相同的区域）
   * **设备供应商**-输入设备供应商（任何名称）
   * **专用地址空间**-（输入一个值，或在启用 BGP 时保留为空）
   * **边界网关协议**-（如果 Azure VPN 网关虚拟网络网关已启用 BGP，则设置为**启用**）
   * **连接到中心**（从下拉列表中选择在系统必备组件中创建的中心）
3. 在 "**链接**" 下，输入以下值：

   * **提供程序名称**-输入链接名称和提供程序名称（任意名称）
   * **速度**-速度（任意数字）
   * **Ip 地址**-输入 ip 地址（与（VPN 网关）虚拟网络网关属性下显示的第一个公共 IP 地址相同）
   * **Bgp 地址**和**asn** -bgp 地址和 asn。 它们必须与某个 BGP 对等 IP 地址和在[步骤 1](#vnetgw)中配置的 VPN 网关虚拟网络网关的 ASN 相同。
4. 查看并选择 "**确认**" 以创建站点。
5. 重复前面的步骤，创建第二个站点以与 VPN 网关虚拟网络网关的第二个实例匹配。 保留相同的设置，但使用 VPN 网关配置中的第二个公共 IP 地址和第二个 BGP 对等 IP 地址除外。
6. 你现在已成功预配了两个站点，可以进入下一节来下载配置文件。

## <a name="downloadconfig"></a>3. 下载 VPN 配置文件

在本部分中，将为你在上一节中创建的每个站点下载 VPN 配置文件。

1. 在 "虚拟 WAN **VPN 站点**" 页的顶部，选择**站点**，然后选择 "**下载站点到站点 VPN 配置**"。 Azure 会使用设置创建配置文件。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/download.png "下载")
2. 下载并打开配置文件。
3. 对于第二个站点，请重复这些步骤。 打开两个配置文件后，可以继续阅读下一节。

## <a name="createlocalgateways"></a>4. 创建本地网络网关

在本部分中，将创建两个 Azure VPN 网关本地网络网关。 上一步中的配置文件包含网关配置设置。 使用这些设置创建和配置 Azure VPN 网关本地网络网关。

1. 使用这些设置创建本地网络网关。 有关如何创建 VPN 网关本地网络网关的信息，请参阅 VPN 网关[一文 "创建本地网络网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)"。

   * **IP 地址**-在配置文件中使用为*Gatewayconfiguration*显示的 Instance0 IP 地址。
   * **BGP** -如果通过 bgp 连接，请选择 "**配置 bgp 设置**" 并输入 ASN "65515"。 输入 BGP 对等节点 IP 地址。 使用 "Instance0 BgpPeeringAddresses" 作为配置文件中的*gatewayconfiguration* 。
   * **订阅、资源组和位置**与虚拟 WAN 中心相同。
2. 查看并创建本地网络网关。 本地网络网关应类似于此示例。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. 重复上述步骤以创建另一个本地网络网关，但这次请使用配置文件中的 "Instance1" 值而不是 "Instance0" 值。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="createlocalgateways"></a>5. 创建连接

在本部分中，将创建 VPN 网关本地网络网关与虚拟网络网关之间的连接。 有关如何创建 VPN 网关连接的步骤，请参阅[配置连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在门户中，导航到虚拟网络网关，并单击 "**连接**"。 在“连接”页的顶部，单击“+添加”打开“添加连接”页。
2. 在 "**添加连接**" 页上，为连接配置下列值：

   * “名称”：命名连接。
   * **连接类型：** 选择**站点到站点（IPSec）**
   * “虚拟网络网关”：由于要从此网关连接，因此该值是固定的。
   * **本地网络网关：** 此连接会将虚拟网络网关连接到本地网关。 选择前面创建的一个本地网络网关。
   * **共享密钥：** 输入共享密钥。
   * **IKE 协议：** 选择 "IKE 协议"。
   * **BGP：** 如果通过 BGP 连接，请选择 "**启用 bgp** "。
3. 单击“确定”以创建连接。
4. 可在虚拟网络网关的“连接”页中查看连接。

   ![Connection](./media/connect-virtual-network-gateway-vwan/connect.png "连接")
5. 重复上述步骤以创建第二个连接。 对于第二个连接，请选择你创建的其他本地网络网关。

## <a name="test"></a>6. 测试连接

你可以通过以下方式测试连接：创建两个虚拟机：一个位于 VPN 网关虚拟网络网关，另一个在虚拟网络的虚拟网络中，然后对这两个虚拟机执行 ping 操作。

1. 在 Azure VPN 网关的虚拟网络（Test1-VNet）中创建虚拟机（VNG）。 请勿在 GatewaySubnet 中创建虚拟机。
2. 创建另一个虚拟网络以连接到虚拟 WAN。 在此虚拟网络的子网中创建虚拟机。 此虚拟网络不能包含任何虚拟网络网关。 你可以使用[站点到站点连接一](virtual-wan-site-to-site-portal.md#vnet)文中的 PowerShell 步骤来快速创建一个虚拟网络。 请确保在运行 cmdlet 前更改这些值。
3. 将 VNet 连接到虚拟 WAN 集线器。 在虚拟 WAN 页面上，选择 "**虚拟网络连接**"，然后单击 " **+ 添加连接**"。 在“添加连接”页上填写以下字段：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击 **"确定"** 以创建虚拟网络连接。
5. 现在 Vm 之间设置了连接。 你应该能够从另一个 VM 对其执行 ping 操作，除非有任何防火墙或其他策略阻止通信。

## <a name="next-steps"></a>后续步骤

有关配置自定义 IPsec 策略的步骤，请参阅为[虚拟 WAN 配置自定义 ipsec 策略](virtual-wan-custom-ipsec-portal.md)。
有关虚拟 WAN 的详细信息，请参阅[关于 Azure 虚拟 WAN](virtual-wan-about.md) 和 [Azure 虚拟 WAN 常见问题解答](virtual-wan-faq.md)。