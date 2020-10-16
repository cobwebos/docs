---
title: 在 VNet 之间创建连接：经典：Azure 门户
description: 使用 PowerShell 和 Azure 门户将 Azure 虚拟网络连接到一起。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103214"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>配置 VNet 到 VNet 连接（经典）

本文介绍如何在虚拟网络之间创建 VPN 网关连接。 虚拟网络可以位于相同或不同的区域，也可以来自相同或不同的订阅。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="显示经典 VNet 到 VNet 体系结构的图示":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

本文中的步骤适用于经典部署模型和 Azure 门户。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [经典](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [资源管理器](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [连接不同部署模型中的 Vnet](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>关于 VNet 到 VNet 的连接

在经典部署模型中使用 VPN 网关将一个虚拟网络连接到另一个虚拟网络（VNet 到 VNet）类似于将虚拟网络连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。

连接的 VNet 可位于不同的订阅和不同的区域中。 可以将 VNet 到 VNet 通信与多站点配置组合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="显示经典 VNet 到 VNet 体系结构的图示":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>为什么要连接虚拟网络？

你可能会出于以下原因而连接虚拟网络：

* **跨区域地域冗余和地域存在**

  * 可以使用安全连接设置自己的异地复制或同步，而无需借助于面向 Internet 的终结点。
  * 使用 Azure 负载均衡器和 Microsoft 或第三方群集技术，可以设置支持跨多个 Azure 区域实现地域冗余的高可用性工作负荷。 一个重要的示例就是对分布在多个 Azure 区域中的可用性组设置 SQL Always On。
* **具有强大隔离边界的区域多层应用程序**

  * 在同一区域中，可以设置具有多个 VNet 的多层应用程序，这些虚拟网络相互连接在一起，但同时又能保持强大的隔离性，而且还能进行安全的层间通信。
* **在 Azure 中跨订阅进行组织间通信**

  * 如果有多个 Azure 订阅，可以在虚拟网络之间安全地将不同订阅中的工作负荷连接起来。
  * 对于企业或服务提供商而言，可以在 Azure 中使用安全 VPN 技术启用跨组织通信。

有关 VNet 到 VNet 连接的详细信息，请参阅本文末尾的 [VNet 到 VNet 注意事项](#faq)。

## <a name="prerequisites"></a>必备条件

使用门户即可执行大部分步骤，但必须使用 PowerShell 创建 VNet 之间的连接。 无法使用 Azure 门户创建连接，因为无法在门户中指定共享密钥。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>规划

必须确定要用于配置虚拟网络的范围。 对于此配置，必须确保 VNet 的范围不互相重叠，也不能与所连接到的任何本地网络重叠。

### <a name="vnets"></a><a name="vnet"></a>Vnet

对于本练习，我们使用以下示例值：

**TestVNet1 的值**

名称：TestVNet1<br>
地址空间：10.11.0.0/16、10.12.0.0/16（可选）<br>
子网名称：默认值<br>
子网地址范围：10.11.0.0/24<br>
资源组：ClassicRG<br>
位置：美国东部<br>
GatewaySubnet：10.11.1.0/27

**用于 TestVNet4 的值**

名称：TestVNet4<br>
地址空间：10.41.0.0/16、10.42.0.0/16（可选）<br>
子网名称：默认值<br>
子网地址范围：10.41.0.0/24<br>
资源组：ClassicRG<br>
位置：美国西部<br>
GatewaySubnet：10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>连接

下表显示了如何连接 Vnet 的示例。 其中的范围仅供参考。 请记下虚拟网络的范围。 后面的步骤需要用到此信息。

在此示例中，TestVNet1 连接到你创建的名为 "VNet4Local" 的本地网络站点。 VNet4Local 的设置包含 TestVNet4 的地址前缀。
每个 VNet 的本地站点是另一个 VNet。 我们的配置使用以下示例值：

**示例**

| 虚拟网络 | 地址空间 | 位置 | 连接到本地网络站点 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |美国东部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |美国西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>创建虚拟网络

在此步骤中，将创建两个经典虚拟网络： TestVNet1 和 TestVNet4。 如果使用本文作为练习，请使用 [示例值](#vnet)。

**创建 VNet 时，请注意以下设置：**

* **虚拟网络地址空间** – 在“虚拟网络地址空间”页上，指定要用于虚拟网络的地址范围。 这些都是动态 IP 地址，将分配给部署到此虚拟网络的 VM 和其他角色实例。<br>所选地址空间不能与任何其他 VNet 的地址空间重叠，也不能与此 VNet 要连接到的本地位置的地址空间重叠。

* **位置** - 当你创建虚拟网络时，你会将它与一个 Azure 位置（区域）相关联。 例如，如果要部署到虚拟网络的 VM 的实际位置为美国西部，请选择该位置。 创建虚拟网络后，无法更改与该虚拟网络关联的位置。

**创建 VNet 后，可以添加以下设置：**

* **地址空间** – 此配置不需要额外的地址空间，但可以在创建 VNet 后添加额外的地址空间。

* **子网** – 此配置不需要额外的子网，但你可能希望使 VM 位于不同于其他角色实例的子网中。

* **DNS 服务器** – 输入 DNS 服务器名称和 IP 地址。 此设置不创建 DNS 服务器。 此设置允许指定要用于对此虚拟网络进行名称解析的 DNS 服务器。

### <a name="to-create-a-classic-virtual-network"></a>创建经典虚拟网络

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>配置站点和网关

Azure 使用在每个本地网络站点中指定的设置来确定如何在 VNet 之间路由流量。 每个 VNet 都必须指向要将流量路由到的相应本地网络。 如果需要使用名称来引用每个本地网络站点，由你来决定该名称。 最好使用描述性文本。

例如，TestVNet1 连接到所创建的名为“VNet4Local”的本地网络站点。 VNet4Local 的设置包含 TestVNet4 的地址前缀。

请记住，每个 VNet 的本地站点是另一个 VNet。

| 虚拟网络 | 地址空间 | 位置 | 连接到本地网络站点 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |美国东部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |美国西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>配置站点

本地站点通常指本地位置。 它包含 VPN 设备的 IP 地址和地址范围，需要创建到该设备的连接，并且需要通过 VPN 网关将地址范围路由到该设备。

1. 在 VNet 页的 " **设置**" 下，选择 " **站点到站点连接**"。
1. 在 "站点到站点连接" 页上，选择 " **+ 添加**"。
1. 在 " **配置 VPN 连接和网关** " 页上，对于 " **连接类型**"，请选择 " **站点到站点** "。

   * **VPN 网关 IP 地址：** 这是本地网络的 VPN 设备的公共 IP 地址。 对于此练习，你可以将放入虚拟地址，因为你还没有用于其他站点的 VPN 网关的 IP 地址。 例如，5.4.3.2。 稍后，为另一个 VNet 配置网关后，你可以调整此值。

   * **客户端地址空间：** 列出要通过此网关路由到另一个 VNet 的 IP 地址范围。 可以添加多个地址空间范围。 请确保在此处指定的范围与虚拟网络连接到的其他网络的范围不重叠，也与虚拟网络本身的地址范围不重叠。
1. 在页面底部，不要选择 "查看 + 创建"。 相反，选择 " **下一步：网关>**。

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>配置虚拟网络网关

1. 在 " **网关** " 页上，选择以下值：

   * **大小：** 这是用于创建虚拟网络网关的网关 SKU。 经典 VPN 使用老版（旧版）网关 SKU。 有关旧版网关 SKU 的详细信息，请参阅[使用虚拟网关 SKU（老版 SKU）](vpn-gateway-about-skus-legacy.md)。 对于此练习，可以选择 " **标准** "。

   * **路由类型：** 为网关选择路由类型。 这也称为 VPN 类型。 选择正确的类型很重要，因为无法将网关从一个类型转换为另一个类型。 VPN 设备必须兼容所选路由类型。 有关路由类型的详细信息，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 可能会有文章引用“RouteBased”和“PolicyBased”VPN 类型。 “动态”对应于“RouteBased”，“静态”对应于“PolicyBased”。 对于此配置，请选择 " **动态**"。

   * **网关子网：** 指定的网关子网的大小取决于要创建的 VPN 网关配置。 尽管网关子网最小可以创建为 /29，但建议使用 /27 或 /28。 这样可以创建较大的子网，包含的地址更多。 使用更大的网关子网可以有足够的 IP 地址来应对未来可能会有的配置。

1. 选择页面底部的 " **查看 + 创建** " 来验证设置。 选择 " **创建** " 以进行部署。 创建虚拟网络网关可能需要长达45分钟的时间，具体取决于所选网关 SKU。
1. 此网关创建时，可以开始执行下一步。

### <a name="configure-testvnet4-settings"></a>配置 TestVNet4 设置

重复 [创建站点和网关](#localsite) 以配置 TestVNet4 的步骤，如有必要，请替换值。 如果要执行此操作，请使用 [示例值](#planning)。

## <a name="update-local-sites"></a><a name="updatelocal"></a>更新本地站点

为两个 Vnet 创建虚拟网络网关后，必须调整 **VPN 网关 IP 地址**的本地站点属性。

|VNet 名称|连接的站点|网关 IP 地址|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4 的 VPN 网关 IP 地址|
|TestVNet4|VNet1Local|TestVNet1 的 VPN 网关 IP 地址|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>第 1 部分 - 获取虚拟网关的公共 IP 地址

1. 转到 **资源组** ，并选择 "虚拟网络"，导航到 VNet。
1. 在虚拟网络的页面上，在右侧的 " **概要** " 窗格中，找到 **网关 IP 地址** ，并将其复制到剪贴板。

### <a name="part-2---modify-the-local-site-properties"></a>第2部分-修改本地站点属性

1. 在 "站点到站点连接" 下，选择连接。 例如，SiteVNet4。
1. 在站点到站点连接的 " **属性** " 页上，选择 " **编辑本地站点**"。
1. 在 " **vpn 网关 ip 地址** " 字段中，粘贴在上一部分中复制的 VPN 网关 ip 地址。
1. 选择“确定”。
1. 该字段将在系统中更新。 你还可以使用此方法添加要路由到此站点的其他 IP 地址。

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>第3部分-对其他 VNet 重复步骤

针对 TestVNet4 重复上述步骤。

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>检索配置值

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>创建连接

完成前面的所有步骤后，可以设置 IPsec/IKE 预共享密钥并创建连接。 这组步骤使用 PowerShell。 无法在 Azure 门户中配置经典部署模型的 VNet 到 VNet 连接，因为不能在门户中指定共享密钥。

在示例中，可以看到共享密钥完全相同。 共享的密钥必须始终匹配。 务必将这些示例中的值替换为 VNet 和本地网络站点的确切名称。

1. 创建 TestVNet1 到 TestVNet4 的连接。 请确保更改值。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. 创建 TestVNet4 到 TestVNet1 的连接。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. 等待连接初始化。 在网关初始化后，状态将变为“成功”。

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>常见问题和注意事项

这些注意事项适用于经典虚拟网络和经典虚拟网络网关。

* 虚拟网络可以在相同或不同的订阅中。
* 虚拟网络可以在相同或不同的 Azure 区域（位置）中。
* 云服务或负载平衡终结点不能跨虚拟网络，即使它们连接在一起。
* 将多个虚拟网络连接在一起不需要任何 VPN 设备。
* VNet 到 VNet 通信支持连接 Azure 虚拟网络。 它不支持连接未部署到虚拟网络的虚拟机或云服务。
* VNet 到 VNet 通信需要动态路由网关。 不支持 Azure 静态路由网关。
* 虚拟网络连接可与多站点 VPN 同时使用。 最多可以将一个虚拟网络 VPN 网关的 10 个 VPN 隧道连接到其他虚拟网络或本地站点。
* 虚拟网络和本地网络站点的地址空间不得重叠。 地址空间重叠会导致创建虚拟网络或上传 netcfg 配置文件失败。
* 不支持一对虚拟网络之间存在冗余隧道。
* VNet 的所有 VPN 隧道（包括 P2S VPN）共享 VPN 网关上的可用带宽，以及 Azure 中的相同 VPN 网关运行时间 SLA。
* VNet 到 VNet 流量会流经 Azure 主干。

## <a name="next-steps"></a>后续步骤

验证连接。 请参阅[验证 VPN 网关连接](vpn-gateway-verify-connection-resource-manager.md)。
