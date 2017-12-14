---
title: "将 Azure 虚拟网络连接到另一 VNet：门户 | Microsoft 文档"
description: "使用 Resource Manager 和 Azure 门户创建 VNet 之间的 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 406cb4faf53bde5f615593e2e904d91a1d90a729
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>使用 Azure 门户配置 VNet 到 VNet VPN 网关连接

本文介绍如何使用 VNet 到 VNet 连接类型来连接虚拟网络。 虚拟网络可以位于相同或不同的区域，也可以来自相同或不同的订阅。 从不同的订阅连接 VNet 时，订阅不需要与相同的 Active Directory 租户相关联。 

本文中的步骤适用于资源管理器部署模型，并使用 Azure 门户。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [连接不同的部署模型 - Azure 门户](vpn-gateway-connect-different-deployment-models-portal.md)
> * [连接不同的部署模型 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![v2v 示意图](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>关于连接 VNet

可通过多种方式来连接 VNet。 以下各节介绍了如何通过不同方式来连接虚拟网络。

### <a name="vnet-to-vnet"></a>VNet 到 VNet

配置一个 VNet 到 VNet 连接即可轻松地连接 VNet。 使用 VNet 到 VNet 连接类型 (VNet2VNet) 将一个虚拟网络连接到另一个虚拟网络类似于创建到本地位置的站点到站点 IPsec 连接。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道，二者在通信时使用同样的方式运行。 连接类型的差异在于本地网关的配置方式。 创建 VNet 到 VNet 连接时，看不到本地网关地址空间。 它是自动创建并填充的。 如果更新一个 VNet 的地址空间，另一个 VNet 会自动知道路由到更新的地址空间。 与在 VNet 之间创建站点到站点连接相比，创建 VNet 到 VNet 连接通常速度更快且更容易。

### <a name="site-to-site-ipsec"></a>站点到站点 (IPsec)

如果要进行复杂的网络配置，则使用[站点到站点](vpn-gateway-howto-site-to-site-resource-manager-portal.md)步骤来连接 VNet 会较好。 使用站点到站点 IPsec 步骤时，可以手动创建和配置本地网关。 每个 VNet 的本地网关都将其他 VNet 视为本地站点。 这样可以为本地网关指定路由流量所需的其他地址空间。 如果 VNet 的地址空间更改，则需根据更改更新相应的本地网关。 它不自动进行更新。

### <a name="vnet-peering"></a>VNet 对等互连

可以考虑使用 VNet 对等互连来连接 VNet。 VNet 对等互连不使用 VPN 网关，并且有不同的约束。 另外，[VNet 对等互连定价](https://azure.microsoft.com/pricing/details/virtual-network)的计算不同于 [VNet 到 VNet VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)的计算。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

## <a name="why"></a>为何创建 VNet 到 VNet 连接？

你可能会出于以下原因而使用 VNet 到 VNet 连接来连接虚拟网络：

* **跨区域地域冗余和地域存在**

  * 可以使用安全连接设置自己的异地复制或同步，而无需借助于面向 Internet 的终结点。
  * 使用 Azure 流量管理器和负载均衡器，可以设置支持跨多个 Azure 区域实现异地冗余的高可用性工作负荷。 一个重要的示例就是对分布在多个 Azure 区域中的可用性组设置 SQL Always On。
* **具有隔离或管理边界的区域多层应用程序**

  * 在同一区域中，由于存在隔离或管理要求，可以设置多个虚拟网络连接在一起的多层应用程序。

可以将 VNet 到 VNet 通信与多站点配置组合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑，如下图所示：

![关于连接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "关于连接")

本文介绍如何使用 VNet 到 VNet 连接类型来连接 VNet。 使用这些步骤作为练习时，可以使用示例设置值。 示例中的虚拟网络在同一订阅中，但却在不同的资源组中。 如果 VNet 在不同的订阅中，则无法在门户中创建连接。 可以使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 或 [CLI](vpn-gateway-howto-vnet-vnet-cli.md)。 有关 VNet 到 VNet 连接的详细信息，请参阅本文末尾的 [VNet 到 VNet常见问题解答](#faq)。

### <a name="values"></a>示例设置

**TestVNet1 的值：**

* VNet 名称：TestVNet1
* 地址空间：10.11.0.0/16
* 订阅：选择要使用的订阅
* 资源组：TestRG1
* 位置：美国东部
* 子网名称：FrontEnd
* 子网地址范围：10.11.0.0/24
* 网关子网名称：GatewaySubnet（会在门户中自动填充）
* 网关子网地址范围：10.11.255.0/27
* DNS 服务器：使用 DNS 服务器的 IP 地址
* 虚拟网络网关名称：TestVNet1GW
* 网关类型：VPN
* VPN 类型：基于路由
* SKU：选择要使用的网关 SKU
* 公共 IP 地址名称：TestVNet1GWIP
* 连接名称：TestVNet1toTestVNet4
* 共享密钥：可以自己创建共享密钥。 在此示例中，我们将使用 abc123。 重要的是在创建 VNet 之间的连接时，值必须匹配。

**TestVNet4 的值：**

* VNet 名称：TestVNet4
* 地址空间：10.41.0.0/16
* 订阅：选择要使用的订阅
* 资源组：TestRG4
* 位置：美国西部
* 子网名称：FrontEnd
* 子网地址范围：10.41.0.0/24
* GatewaySubnet 名称：GatewaySubnet（会在门户中自动填充）
* GatewaySubnet 地址范围：10.41.255.0/27
* DNS 服务器：使用 DNS 服务器的 IP 地址
* 虚拟网络网关名称：TestVNet4GW
* 网关类型：VPN
* VPN 类型：基于路由
* SKU：选择要使用的网关 SKU
* 公共 IP 地址名称：TestVNet4GWIP
* 连接名称：TestVNet4toTestVNet1
* 共享密钥：可以自己创建共享密钥。 在此示例中，我们将使用 abc123。 重要的是在创建 VNet 之间的连接时，值必须匹配。

## <a name="CreatVNet"></a>1.创建并配置 TestVNet1
如果已有一个 VNet，请验证这些设置是否与 VPN 网关设计兼容。 请特别注意任何可能与其他网络重叠的子网。 如果有重叠子网，连接将无法正常工作。 如果 VNet 配置了正确的设置，则可以开始执行 [指定 DNS 服务器](#dns) 部分中的步骤。

### <a name="to-create-a-virtual-network"></a>创建虚拟网络
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2.添加其他地址空间并创建子网
创建 VNet 后，可以添加其他地址空间和创建子网。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3.创建网关子网
将虚拟网络连接到网关之前，必须先创建要连接的虚拟网络的网关子网。 在可能的情况下，最好是使用 CIDR 块 /28 或 /27 创建网关子网，以便提供足够的 IP 地址，满足将来的其他配置要求。

如果正在练习创建此配置，请在创建网关子网时参考[示例设置](#values)。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>创建网关子网
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4.指定 DNS 服务器（可选）
VNet 到 VNet 连接不需要 DNS。 但是，如果希望对部署到虚拟网络的资源进行名称解析，则应指定 DNS 服务器。 可以通过此设置指定 DNS 服务器，以便将其用于此虚拟网络的名称解析。 此设置不创建 DNS 服务器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5.创建虚拟网络网关
在此步骤中，为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选网关 SKU。 如果正在练习创建此配置，可以参考[示例设置](#values)。

### <a name="to-create-a-virtual-network-gateway"></a>创建虚拟网络网关
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6.创建并配置 TestVNet4
配置 TestVNet1 后，请通过重复上述步骤创建 TestVNet4，并将值替换为 TestVNet4 的值。 无需等待 TestVNet1 的虚拟网络网关完成创建即可配置 TestVNet4。 如果使用自己的值，请确保地址空间不与任何想要连接的 VNet 重叠。

## <a name="TestVNet1Connection"></a>7.配置 TestVNet1 网关连接
TestVNet1 和 TestVNet4 的虚拟网络网关创建完成后，可以开始创建虚拟网络网关连接。 在本部分，请创建从 VNet1 到 VNet4 的连接。 这些步骤仅适用于同一订阅中的 VNet。 如果 VNet 属于不同的订阅，则必须使用 PowerShell 进行连接。 请参阅 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 一文。 不过，如果 VNet 位于同一订阅的不同资源组中，则可使用门户来连接它们。

1. 在“所有资源”中，导航到 VNet 的虚拟网络网关。 例如，**TestVNet1GW**。 单击“TestVNet1GW”打开虚拟网关页。

  ![“连接”页](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "“连接”页")
2. 单击“+添加”打开“添加连接”页。

  ![添加连接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "添加连接")
3. 在“添加连接”页的“名称”字段中，键入连接名称。 例如，**TestVNet1toTestVNet4**。
4. 对于“连接类型”，请从下拉列表中选择“VNet 到 VNet”。
5. **第一个虚拟网络网关**字段值会自动填充，你是从指定的虚拟网络网关创建此连接。
6. “第二个虚拟网关”字段是你想要创建连接的 VNet 的虚拟网关。 单击“选择另一个虚拟网关”打开“选择虚拟网关”页。
7. 查看此页上列出的虚拟网关。 请注意，仅会列出订阅中的虚拟网络网关。 如果想要连接到订阅外的虚拟网络网关，请参阅 [PowerShell 文章](vpn-gateway-vnet-vnet-rm-ps.md)。
8. 单击想要连接的虚拟网络网关。
9. 在“共享密钥”字段中，键入连接的共享密钥。 可以自己生成或创建此密钥。 在站点到站点连接中，使用的密钥应与本地设备和虚拟网络网关连接的密钥完全相同。 原理大致相同，所不同的是此时不是连接到 VPN 设备，而是连接到另一个虚拟网络网关。
10. 单击页面底部的“确定”，保存所做的更改。

## <a name="TestVNet4Connection"></a>8.配置 TestVNet4 网关连接
接下来，创建从 TestVNet4 到 TestVNet1 的连接。 在门户中找到与 TestVNet4 关联的虚拟网关。 按上一部分的步骤替换相关值，创建从 TestVNet4 到 TestVNet1 的连接。 确保使用的同一个共享秘钥。

## <a name="VerifyConnection"></a>9.验证连接

在门户中找到虚拟网关。 在虚拟网关页上单击“连接”，查看虚拟网关的连接页。 建立连接后，会看到“状态”值更改为“成功”和“已连接”。 双击某个连接即可打开“概要”页并查看更多信息。

![成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "成功")

数据开始流动后，可看到“输入数据量”和“输出数据量”的值。

![概要](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "概要")

## <a name="to-add-additional-connections"></a>添加其他连接

若要添加其他连接，请导航到要从其创建连接的虚拟网关，然后单击“连接”。 可以创建另一个 VNet 到 VNet 连接，也可以创建一个 IPsec 站点到站点连接，以便连接到本地位置。 请务必调节“连接类型”，使之与要创建的连接类型匹配。 在创建其他连接之前，请验证虚拟网络的地址空间是否不与要连接到的地址空间重叠。 如需创建站点到站点连接的步骤，请参阅[创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

## <a name="faq"></a>VNet 到 VNet 常见问题解答
查看常见问题解答详细信息以获取有关 VNet 到 VNet 连接的其他信息。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>后续步骤

请参阅[网络安全](../virtual-network/security-overview.md)，了解如何限制发往虚拟网络中资源的网络流量。

请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)，了解 Azure 如何在 Azure 资源、本地资源和 Internet 资源之间路由流量。