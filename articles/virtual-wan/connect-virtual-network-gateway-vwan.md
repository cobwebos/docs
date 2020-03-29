---
title: 将虚拟网络网关连接到 Azure 虚拟 WAN
description: 本文可帮助您将 Azure 虚拟网络网关连接到 Azure 虚拟 WAN VPN 网关
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066234"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>将 VPN 网关（虚拟网络网关）连接到虚拟广域网

本文可帮助您设置从 Azure VPN 网关（虚拟网络网关）到 Azure 虚拟 WAN（VPN 网关）的连接。 从 VPN 网关（虚拟网络网关）创建到虚拟 WAN （VPN 网关）的连接类似于从分支 VPN 站点设置到虚拟 WAN 的连接。

为了尽量减少两个功能之间的可能混淆，我们将以我们所指的功能的名称来介绍网关。 例如，VPN 网关虚拟网络网关和虚拟 WAN VPN 网关。

## <a name="before-you-begin"></a>开始之前

开始之前，请创建以下资源：

Azure 虚拟 WAN

* [创建虚拟 WAN。](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)。 虚拟中心包含虚拟 WAN VPN 网关。

Azure 虚拟网络

* 创建没有任何虚拟网络网关的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. 创建 Azure 虚拟网络网关

为虚拟网络在主动-主动模式下为虚拟网络创建 VPN 网关虚拟网络网关。 创建网关时，可以为网关的两个实例使用现有的公共 IP 地址，也可以创建新的公共 IP。 在设置虚拟 WAN 站点时，可以使用这些公共 IP。 有关活动-活动模式的详细信息，请参阅[配置活动-活动连接](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active-mode-setting"></a><a name="active-active"></a>主动-主动模式设置

![主动活动](./media/connect-virtual-network-gateway-vwan/active.png "主动-主动")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 设置

BGP ASN 不能为 65515。 66515 将由 Azure 虚拟 WAN 使用。

![Bgp](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>公共 IP 地址

创建网关时，导航到 **"属性"** 页。 属性和配置设置将类似于以下示例。 请注意用于网关的两个公共 IP 地址。

![性能](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. 创建虚拟广域网 VPN 站点

要创建虚拟 WAN VPN 站点，请导航到虚拟 WAN，并在 **"连接"** 下选择**VPN 站点**。 在本节中，您将创建两个虚拟 WAN VPN 站点，它们对应于您在上一节中创建的虚拟网络网关。

1. 选择 **"创建网站**"。
2. 在 **"创建 VPN 站点"** 页上，键入以下值：

   * **区域**- （与 Azure VPN 网关虚拟网络网关相同的区域）
   * **设备供应商**- 输入设备供应商（任何名称）
   * **专用地址空间**- （输入值，或在启用 BGP 时留空）
   * **边界网关协议**- （如果 Azure VPN 网关虚拟网络网关启用 BGP，则设置为**启用**）
   * **连接到集线器**（从下拉列表中选择在必备条件中创建的集线器）
3. 在**链接下**，输入以下值：

   * **提供程序名称**- 输入链接名称和提供程序名称（任何名称）
   * **速度**- 速度（任意数字）
   * **IP 地址**- 输入 IP 地址（与 （VPN 网关） 虚拟网络网关属性下显示的第一个公共 IP 地址相同）
   * **BGP 地址**和**ASN** - BGP 地址和 ASN。 这些地址必须与 BGP 对等 IP 地址之一相同，并且从[您在步骤 1](#vnetgw)中配置的 VPN 网关虚拟网络网关中的 ASN 相同。
4. 查看并选择 **"确认"** 以创建网站。
5. 重复前面的步骤以创建第二个站点以匹配 VPN 网关虚拟网络网关的第二个实例。 除了使用 VPN 网关配置的第二个公共 IP 地址和第二个 BGP 对等 IP 地址外，您将保留相同的设置。
6. 现在，您已成功预配了两个站点，可以转到下一节以下载配置文件。

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. 下载 VPN 配置文件

在本节中，您可以下载上一节中创建的每个站点的 VPN 配置文件。

1. 在虚拟 WAN VPN**网站页面**的顶部，选择 **"站点**"，然后选择 **"下载站点到站点 VPN"配置**。 Azure 会创建具有设置的配置文件。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/download.png "下载")
2. 下载并打开配置文件。
3. 对第二个站点重复这些步骤。 打开两个配置文件后，可以继续下一节。

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 创建本地网络网关

在本节中，您将创建两个 Azure VPN 网关本地网络网关。 上一步的配置文件包含网关配置设置。 使用这些设置创建和配置 Azure VPN 网关本地网络网关。

1. 使用这些设置创建本地网络网关。 有关如何创建 VPN 网关本地网络网关的信息，请参阅 VPN 网关文章["创建本地网络网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)"。

   * **IP 地址**- 使用从配置文件中显示的网关*配置*的实例0 IP 地址。
   * **BGP** - 如果连接超过 BGP，请选择 **"配置 BGP 设置**"并输入 ASN "65515"。 输入 BGP 对等 IP 地址。 使用"实例0 Bgp对等地址"从配置文件的*网关配置*。
   * **订阅、资源组和位置**与虚拟广域网中心相同。
2. 查看并创建本地网络网关。 本地网络网关应与此示例类似。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng1.png "实例0")
3. 重复这些步骤以创建另一个本地网络网关，但这次使用配置文件中的"实例1"值而不是"实例0"值。

   ![下载配置文件](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 创建连接

在本节中，您将在 VPN 网关本地网络网关和虚拟网络网关之间创建连接。 有关如何创建 VPN 网关连接的步骤，请参阅[配置连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在门户中，导航到虚拟网络网关，然后单击"**连接**"。 在“连接”页的顶部，单击“+添加”**** 打开“添加连接”**** 页。
2. 在 **"添加连接**"页上，为连接配置以下值：

   * “名称”：**** 命名连接。
   * **连接类型：** 选择**站点到站点 （IPSec）**
   * “虚拟网络网关”：**** 由于要从此网关连接，因此该值是固定的。
   * **本地网络网关：** 此连接将虚拟网络网关连接到本地网络网关。 选择您之前创建的本地网络网关之一。
   * **共享密钥：** 输入共享密钥。
   * **IKE 协议：** 选择 IKE 协议。
   * **BGP：** 如果连接超过**BGP，请选择启用 BGP。**
3. 单击“确定”以创建连接****。
4. 可在虚拟网络网关的“连接”**** 页中查看连接。

   ![连接](./media/connect-virtual-network-gateway-vwan/connect.png "连接")
5. 重复上述步骤以创建第二个连接。 对于第二个连接，请选择您创建的其他本地网络网关。

## <a name="6-test-connections"></a><a name="test"></a>6. 测试连接

您可以通过创建两个虚拟机（一个位于 VPN 网关虚拟网络网关侧，另一个在虚拟 WAN 的虚拟网络中）来测试连接，然后 ping 两个虚拟机。

1. 在虚拟网络 （Test1-VNet） 中为 Azure VPN 网关 （Test1-VNG） 创建虚拟机。 请勿在网关子网中创建虚拟机。
2. 创建另一个虚拟网络以连接到虚拟广域网。 在此虚拟网络的子网中创建虚拟机。 此虚拟网络不能包含任何虚拟网络网关。 您可以使用[站点到站点连接](virtual-wan-site-to-site-portal.md#vnet)文章中的 PowerShell 步骤快速创建虚拟网络。 在运行 cmdlet 之前，请确保更改值。
3. 将 VNet 连接到虚拟广域网中心。 在虚拟 WAN 的页面上，选择**虚拟网络连接**，然后 **[添加连接**。 在“添加连接”页上填写以下字段****：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”**** 以创建虚拟网络连接。
5. 现在，VM 之间已设置连接。 您应该能够从另一个 VM ping 一个 VM，除非有任何防火墙或其他策略阻止通信。

## <a name="next-steps"></a>后续步骤

有关配置自定义 IPsec 策略的步骤，请参阅[为虚拟 WAN 配置自定义 IPsec 策略](virtual-wan-custom-ipsec-portal.md)。
有关虚拟 WAN 的详细信息，请参阅[关于 Azure 虚拟 WAN](virtual-wan-about.md) 和 [Azure 虚拟 WAN 常见问题解答](virtual-wan-faq.md)。