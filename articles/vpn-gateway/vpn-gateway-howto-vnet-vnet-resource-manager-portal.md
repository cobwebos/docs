---
title: 使用 Azure 门户配置 VNet 到 VNet VPN 网关连接 | Microsoft Docs
description: 使用 Resource Manager 和 Azure 门户创建 VNet 之间的 VPN 网关连接。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 94b32595cf2c884ccfd1362f6c8d03f542aabfc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128375"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>使用 Azure 门户配置 VNet 到 VNet VPN 网关连接

本文介绍如何使用 VNet 到 VNet 连接类型来连接虚拟网络 (VNet)。 虚拟网络可位于不同的区域和不同的订阅中。 从不同的订阅连接 VNet 时，订阅不需要与相同的 Active Directory 租户相关联。 

![v2v 示意图](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

本文中的步骤适用于 Azure 资源管理器部署模型，并使用 Azure 门户。 可使用以下文章中所述的选项，通过不同的部署工具或模型创建此配置：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [连接不同的部署模型 - Azure 门户](vpn-gateway-connect-different-deployment-models-portal.md)
> * [连接不同的部署模型 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>


## <a name="about-connecting-vnets"></a>关于连接 VNet

以下部分介绍如何通过不同的方式连接虚拟网络。

### <a name="vnet-to-vnet"></a>VNet 到 VNet

配置 VNet 到 VNet 连接是连接 VNet 的简单方式。 使用 VNet 到 VNet 连接类型 (VNet2VNet) 将一个虚拟网络连接到另一个虚拟网络类似于与本地位置建立站点到站点 IPsec 连接。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道，两者在通信时的运行方式相同。 但是，两者在本地网络网关的配置方式上有差别。 

创建 VNet 到 VNet 连接时，不会自动创建和填充本地网络网关地址空间。 如果更新一个 VNet 的地址空间，另一个 VNet 会自动路由到更新的地址空间。 与创建站点到站点连接相比，创建 VNet 到 VNet 连接通常速度更快且更容易。

### <a name="site-to-site-ipsec"></a>站点到站点 (IPsec)

如果使用复杂的网络配置，你可能偏向于使用[站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)来连接 VNet。 遵循站点到站点 IPsec 步骤时，可以手动创建和配置本地网络网关。 每个 VNet 的本地网关都将其他 VNet 视为本地站点。 使用这些步骤可为本地网络网关指定其他地址空间用于路由流量。 如果 VNet 的地址空间发生更改，必须手动更新相应的本地网络网关。

### <a name="vnet-peering"></a>VNet 对等互连

也可以使用 VNet 对等互连来连接 VNet。 VNet 对等互连不使用 VPN 网关，并且具有不同的约束。 另外，[VNet 对等互连定价](https://azure.microsoft.com/pricing/details/virtual-network)的计算不同于 [VNet 到 VNet VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)的计算。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

## <a name="why-create-a-vnet-to-vnet-connection"></a>为何创建 VNet 到 VNet 连接？

你可能会出于以下原因而使用 VNet 到 VNet 连接来连接虚拟网络：

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>跨区域地域冗余和地域存在

  * 可以使用安全连接设置自己的异地复制或同步，而无需借助于面向 Internet 的终结点。
  * 使用 Azure 流量管理器和 Azure 负载均衡器，可以设置支持跨多个 Azure 区域实现异地冗余的高可用性工作负荷。 例如，可跨多个 Azure 区域中设置 SQL Always On 可用性组。

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>具有隔离或管理边界的区域多层应用程序

  * 在同一区域中，由于存在隔离或管理要求，可以设置多个虚拟网络连接在一起的多层应用程序。

可以将 VNet 到 VNet 通信与多站点配置组合使用。 使用这些配置可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑，如下图所示：

![关于连接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "关于连接")

本文介绍如何使用 VNet 到 VNet 连接类型来连接 VNet。 遵循这些步骤进行练习时，可以使用以下示例设置值。 示例中的虚拟网络在同一订阅中，但却在不同的资源组中。 如果 VNet 在不同的订阅中，则无法在门户中创建连接。 改用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 或 [CLI](vpn-gateway-howto-vnet-vnet-cli.md)。 有关 VNet 到 VNet 连接的详细信息，请参阅 [VNet 到 VNet 连接常见问题解答](#vnet-to-vnet-faq)。

### <a name="example-settings"></a>示例设置

**TestVNet1 的值：**

- **虚拟网络设置**
    - **名称**：输入 *TestVNet1*。
    - **地址空间**：输入 *10.11.0.0/16*。
    - **订阅**：选择要使用的订阅。
    - **资源组**：输入 *TestRG1*。
    - **位置**：选择“美国东部”。
    - **子网**
        - **名称**：输入 *FrontEnd*。
        - **地址范围**：输入 *10.11.0.0/24*。
    - **网关子网**：
        - **名称**：“网络子网”会自动填充。
        - **地址范围**：输入 *10.11.255.0/27*。
    - **DNS 服务器**：选择“自定义”，然后输入 DNS 服务器的 IP 地址。

- **虚拟网络网关设置** 
    - **名称**：输入*TestVNet1GW*。
    - **网关类型**：选择“VPN”。
    - **VPN 类型**：选择“基于路由”。
    - **SKU**：选择要使用的网关 SKU。
    - **公共 IP 地址名称**：输入 *TestVNet1GWIP*
    - **Connection** 
       - **名称**：输入 *TestVNet1toTestVNet4*。
       - **共享密钥**：输入 *abc123*。 可以自行创建共享密钥。 在 VNet 之间建立连接时，上述值必须匹配。

**TestVNet4 的值：**

- **虚拟网络设置**
   - **名称**：输入 *TestVNet4*。
   - **地址空间**：输入 *10.41.0.0/16*。
   - **订阅**：选择要使用的订阅。
   - **资源组**：输入 *TestRG4*。
   - **位置**：选择“美国西部”。
   - **子网** 
      - **名称**：输入 *FrontEnd*。
      - **地址范围**：输入 *10.41.0.0/24*。
   - **GatewaySubnet** 
      - **名称**：“网络子网”会自动填充。
      - **地址范围**：输入 *10.41.255.0/27*。
   - **DNS 服务器**：选择“自定义”，然后输入 DNS 服务器的 IP 地址。

- **虚拟网络网关设置** 
    - **名称**：输入 *TestVNet4GW*。
    - **网关类型**：选择“VPN”。
    - **VPN 类型**：选择“基于路由”。
    - **SKU**：选择要使用的网关 SKU。
    - **公共 IP 地址名称**：输入 *TestVNet4GWIP*。
    - **Connection** 
       - **名称**：输入 *TestVNet4toTestVNet1*。
       - **共享密钥**：输入 *abc123*。 可以自行创建共享密钥。 在 VNet 之间建立连接时，上述值必须匹配。

## <a name="create-and-configure-testvnet1"></a>创建并配置 TestVNet1
如果已有一个 VNet，请验证这些设置是否与 VPN 网关设计兼容。 请特别注意任何可能与其他网络重叠的子网。 如果有重叠的子网，将无法正常连接。 为 VNet 配置正确的设置后，可以开始执行“指定 DNS 服务器”部分中的步骤。

### <a name="to-create-a-virtual-network"></a>创建虚拟网络
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>添加其他地址空间并创建子网
创建 VNet 后，可以添加其他地址空间和创建子网。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>创建网关子网
为虚拟网络创建虚拟网络网关前，需要先创建网关子网。 网关子网包含虚拟网络网关使用的 IP 地址。 在可能的情况下，最好是使用 CIDR 块 /28 或 /27 创建网关子网，以提供足够的 IP 地址，满足将来的其他配置要求。

如果正在练习创建此配置，请在创建网关子网时参考这些[示例设置](#example-settings)。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>创建网关子网
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>指定 DNS 服务器（可选）
VNet 到 VNet 连接不需要 DNS。 但是，若要对部署到虚拟网络的资源进行名称解析，请指定 DNS 服务器。 可以通过此设置指定 DNS 服务器，以便将其用于此虚拟网络的名称解析。 此设置不会创建 DNS 服务器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>创建虚拟网络网关
在此步骤中，为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。 如果你正在练习创建此配置，请参阅[示例设置](#example-settings)。

### <a name="to-create-a-virtual-network-gateway"></a>创建虚拟网络网关
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>创建并配置 TestVNet4
配置 TestVNet1 后，重复上述步骤创建 TestVNet4（请将值替换为 TestVNet4 的值）。 无需等到 TestVNet1 的虚拟网络网关创建完成即可配置 TestVNet4。 如果使用自己的值，请确保地址空间不与任何想要连接的 VNet 重叠。

## <a name="configure-the-testvnet1-gateway-connection"></a>配置 TestVNet1 网关连接
TestVNet1 和 TestVNet4 的虚拟网络网关创建完成后，可以开始创建虚拟网络网关连接。 在本部分，请创建从 VNet1 到 VNet4 的连接。 这些步骤仅适用于同一订阅中的 VNet。 如果 VNet 属于不同的订阅，则必须使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 进行连接。 不过，如果 VNet 位于同一订阅的不同资源组中，则可使用门户来连接它们。

1. 在 Azure 门户中选择“所有资源”，在搜索框中输入“虚拟网络网关”，然后导航到 VNet 的虚拟网络网关。 例如，**TestVNet1GW**。 选择该网关打开“虚拟网络网关”页。

   ![“连接”页](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "“连接”页")
2. 在“设置”下选择“连接”，然后选择“添加”打开“添加连接”页。

   ![添加连接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "添加连接")
3. 在“添加连接”页上，填写用于建立连接的值：

   - **名称**：输入连接的名称。 例如，*TestVNet1toTestVNet4*。

   - **连接类型**：从下拉列表选择“VNet 到 VNet”。

   - **第一个虚拟网络网关**：此字段值会自动填充，因为要从指定的虚拟网络网关建立此连接。

   - **第二个虚拟网络网关**：此字段是要连接到的 VNet 的虚拟网络网关。 选择“选择另一个虚拟网络网关”打开“选择虚拟网络网关”页。

     - 查看此页上列出的虚拟网关。 请注意，仅会列出订阅中的虚拟网络网关。 若要连接到订阅外部的虚拟网络网关，请使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)。

     - 选择要连接的虚拟网络网关。

     - **共享密钥(PSK)**：在此字段中，输入连接的共享密钥。 可以自己生成或创建此密钥。 在站点到站点连接中，使用的密钥与本地设备和虚拟网络网关连接的密钥相同。 此处的概念大致相同，不过，此时不是连接到 VPN 设备，而是连接到另一个虚拟网络网关。
    
4. 选择“确定”保存更改。

## <a name="configure-the-testvnet4-gateway-connection"></a>配置 TestVNet4 网关连接
接下来，创建从 TestVNet4 到 TestVNet1 的连接。 在门户中找到与 TestVNet4 关联的虚拟网关。 按上一部分的步骤替换相关值，创建从 TestVNet4 到 TestVNet1 的连接。 确保使用的同一个共享秘钥。

## <a name="verify-your-connections"></a>验证连接

在 Azure 门户中找到虚拟网络网关。 在“虚拟网络网关”页上选择“连接”，查看虚拟网络网关的“连接”页。 建立连接后，会看到“状态”值更改为“成功”和“已连接”。 选择一个连接打开“概要”页并查看更多信息。

![成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "成功")

数据开始流动后，会看到“输入数据”和“输出数据”的值。

![概要](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "概要")

## <a name="add-additional-connections"></a>添加其他连接

若要添加其他连接，请导航到要从中创建连接的虚拟网络网关，然后选择“连接”。 可以创建另一个 VNet 到 VNet 连接，也可以创建一个 IPsec 站点到站点连接，以便连接到本地位置。 请务必调节“连接类型”，使之与要创建的连接类型匹配。 在创建其他连接之前，请验证虚拟网络的地址空间是否不与要连接到的地址空间重叠。 如需创建站点到站点连接的步骤，请参阅[创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

## <a name="vnet-to-vnet-faq"></a>VNet 到 VNet 常见问题
查看常见问题解答详细信息以获取有关 VNet 到 VNet 连接的其他信息。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>后续步骤

有关如何限制发往虚拟网络中资源的网络流量的信息，请参阅[网络安全性](../virtual-network/security-overview.md)。

有关 Azure 如何在 Azure 资源、本地资源和 Internet 资源之间路由流量的信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。
