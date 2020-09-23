---
title: 将虚拟网络网关连接到 Azure 虚拟 WAN
description: 本文介绍如何将 Azure 虚拟网络网关连接到 Azure 虚拟 WAN VPN 网关
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6dac922f1a50dd54973f7eefff7011aab3b49994
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983750"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>将 VPN 网关（虚拟网络网关）连接到虚拟 WAN

本文帮助你设置从 Azure VPN 网关（虚拟网络网关）到 Azure 虚拟 WAN（VPN 网关）的连接。 创建从 VPN 网关（虚拟网络网关）到虚拟 WAN（VPN 网关）的连接类似于设置从分支 VPN 站点到虚拟 WAN 的连接。

为了尽量避免用户混淆这两项功能，我们将在“网关”前面加上所述功能的名称。 例如，VPN 网关虚拟网络网关，以及虚拟 WAN VPN 网关。

## <a name="before-you-begin"></a>准备阶段

在开始之前，请创建以下资源：

Azure 虚拟 WAN

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)。
* [创建中心](virtual-wan-site-to-site-portal.md#hub)。 虚拟中心包含虚拟 WAN VPN 网关。

Azure 虚拟网络

* 创建不带任何虚拟网络网关的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. 创建 VPN 网关虚拟网络网关

为虚拟网络创建主动-主动模式下的 **VPN 网关** 虚拟网络网关。 创建网关时，可将现有公共 IP 地址用于该网关的两个实例，或者可以创建新的公共 IP。 设置虚拟 WAN 站点时，将使用这些公共 Ip。 有关主动-主动 VPN 网关和配置步骤的详细信息，请参阅 [配置主动-主动 vpn 网关](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active-mode-setting"></a><a name="active-active"></a>主动-主动模式设置

在 "虚拟网络网关 **配置** " 页上，启用主动-主动模式。

![主动-主动](./media/connect-virtual-network-gateway-vwan/active.png "主动-主动")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 设置

在 "虚拟网络网关 **配置** " 页上，可以配置 **BGP ASN**。 更改 BGP ASN。 BGP ASN 不能是 65515。 66515 将由 Azure 虚拟 WAN 使用。

![屏幕截图显示了一个虚拟网络网关配置页，其中选择了 "配置 BGP ASN"。](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>公共 IP 地址

创建网关后，导航到“属性”页。 属性和配置设置类似于以下示例。 请注意，对网关使用了两个公共 IP 地址。

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2.创建虚拟 WAN VPN 站点

若要创建虚拟 WAN VPN 站点，请导航到你的虚拟 WAN，然后在“连接”下选择“VPN 站点”。  在本部分，你将创建两个虚拟 WAN VPN 站点，它们对应于在上一部分创建的虚拟网络网关。

1. 选择“+创建站点”。
2. 在“创建 VPN 站点”页上键入以下值：

   * **区域** -与 Azure VPN 网关虚拟网络网关相同的区域。
   * **设备供应商** -输入设备供应商 (任何名称) 。
   * **专用地址空间** -输入一个值，或在启用 BGP 时保留为空。
   * **边界网关协议** -如果 Azure VPN 网关虚拟网络网关已启用 BGP，则设置为 " **启用** "。
   * **连接到中心** -从下拉列表中选择在系统必备组件中创建的中心。 如果看不到集线器，请验证是否为中心创建了站点到站点 VPN 网关。
3. 在“链接”下输入以下值：

   * **提供程序名称** -输入链接名称和提供程序名称 (任何名称) 。
   * **速度** (任意数量) 。
   * **Ip 地址** -输入 ip 地址 (与 (VPN 网关) 虚拟网络网关属性) 下显示的第一个公共 IP 地址相同。
   * **BGP 地址**和 **ASN** - BGP 地址和 ASN。 这些值必须与某个 BGP 对等 IP 地址以及在[步骤 1](#vnetgw) 中配置的 VPN 网关虚拟网络网关的 ASN 相同。
4. 检查设置，然后选择“确认”以创建站点。
5. 重复上述步骤，创建与 VPN 网关虚拟网络网关的第二个实例匹配的第二个站点。 保留相同的设置，不过这一次要使用 VPN 网关配置中的第二个公共 IP 地址和第二个 BGP 对等 IP 地址。
6. 现已成功预配两个站点，接下来可以转到下一部分下载配置文件。

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3.下载 VPN 配置文件

在本部分，你将下载在上一部分创建的每个站点的 VPN 配置文件。

1. 在虚拟 WAN 的“VPN 站点”页的顶部，依次选择“站点”、“下载站点到站点 VPN 配置”。   Azure 将使用相应的设置创建配置文件。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/download.png "下载")
2. 下载并打开配置文件。
3. 针对第二个站点重复上述步骤。 打开两个配置文件后，可以转到下一部分。

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4.创建本地网络网关

在本部分，你将创建两个 Azure VPN 网关本地网络网关。 在上一步骤中下载的配置文件包含网关配置设置。 使用这些设置来创建和配置 Azure VPN 网关本地网络网关。

1. 使用这些设置创建本地网络网关。 有关如何创建 VPN 网关本地网络网关的信息，请参阅 VPN 网关文章[创建本地网络网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)。

   * **IP 地址** - 使用配置文件中为 *gatewayconfiguration* 显示的 Instance0 IP 地址。
   * **BGP** - 如果通过 BGP 建立连接，请选择“配置 BGP 设置”并输入 ASN“65515”。 输入 BGP 对等 IP 地址。 使用配置文件中 *gatewayconfiguration* 的“Instance0 BgpPeeringAddresses”。
   * “订阅”、“资源组”和“位置”与虚拟 WAN 中心相同。
2. 检查设置，然后创建本地网络网关。 本地网络网关应类似于以下示例。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. 重复上述步骤以创建另一个本地网络网关，但这一次请使用配置文件中的“Instance1”值而不是“Instance0”值。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5.创建连接

在本部分，你将在 VPN 网关本地网络网关与虚拟网络网关之间创建连接。 有关如何创建 VPN 网关连接的步骤，请参阅[配置连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在门户中导航到你的虚拟网络网关，然后单击“连接”。 在“连接”页的顶部，单击“+添加”打开“添加连接”页。
2. 在“添加连接”页上，为你的连接配置以下值：

   * **名称：** 命名连接。
   * **连接类型：** 选择“站点到站点(IPSec)”
   * **虚拟网络网关：** 由于要从此网关连接，因此该值是固定的。
   * **本地网络网关：** 此连接会将虚拟网络网关连接到本地网络网关。 选择前面创建的本地网络网关之一。
   * **共享密钥：** 输入共享密钥。
   * **IKE 协议：** 选择 IKE 协议。
3. 单击“确定”以创建连接。
4. 可在虚拟网络网关的“连接”页中查看连接。

   ![Connection](./media/connect-virtual-network-gateway-vwan/connect.png "连接")
5. 重复上述步骤创建第二个连接。 对于第二个连接，请选择已创建的另一个本地网络网关。
6. 如果通过 BGP 连接，则在创建连接后，导航到连接并选择 " **配置**"。 在 "**配置**" 页上，选择 "**已启用**" 作为**BGP**。 然后单击“保存” 。 对第二个连接重复此操作。

## <a name="6-test-connections"></a><a name="test"></a>6.测试连接

可通过以下方式测试连接：创建两个虚拟机（一个位于 VPN 网关虚拟网络网关端，另一个位于虚拟 WAN 的虚拟网络中），然后 ping 这两个虚拟机。

1. 在 Azure VPN 网关 (Test1-VNG) 的虚拟网络 (Test1-VNet) 中创建一个虚拟机。 不要在 GatewaySubnet 中创建虚拟机。
2. 创建另一个虚拟网络以连接到虚拟 WAN。 在此虚拟网络的子网中创建一个虚拟机。 此虚拟网络不能包含任何虚拟网络网关。 可以使用[站点到站点连接](virtual-wan-site-to-site-portal.md#vnet)一文中的 PowerShell 步骤快速创建虚拟网络。 在运行 cmdlet 之前，请务必更改值。
3. 将 VNet 连接到虚拟 WAN 中心。 在虚拟 WAN 的页面上，依次选择“虚拟网络连接”、“+添加连接”。  在“添加连接”页上填写以下字段：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建虚拟网络连接。
5. 现已在 VM 之间设置了连接。 应该可以从一个 VM ping 另一个 VM，除非有任何防火墙或其他策略阻止了通信。

## <a name="next-steps"></a>后续步骤

有关配置自定义 IPsec 策略的步骤，请参阅[为虚拟 WAN 配置自定义 IPsec 策略](virtual-wan-custom-ipsec-portal.md)。
有关虚拟 WAN 的详细信息，请参阅[关于 Azure 虚拟 WAN](virtual-wan-about.md) 和 [Azure 虚拟 WAN 常见问题解答](virtual-wan-faq.md)。
