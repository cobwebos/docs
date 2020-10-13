---
title: 将本地网络连接到 Azure 虚拟网络：站点到站点 VPN（经典）：门户 | Microsoft Docs
description: 通过公共 Internet 创建从本地网络到经典 Azure 虚拟网络的 IPsec 连接。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 7680c7ad4b2c5c8b8c1c13fb2344575659eda140
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892815"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>使用 Azure 门户创建站点到站点连接（经典）

本文介绍如何使用 Azure 门户创建站点到站点 VPN 网关连接，以便从本地网络连接到 VNet。 本文中的步骤适用于经典部署模型，不适用于当前部署模型“资源管理器”。 也可使用不同的部署工具或部署模型创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>准备工作

在开始配置之前，请验证是否符合以下条件：

* 确认要使用经典部署模型。 如果要使用资源管理器部署模型，请参阅[创建站点到站点连接（资源管理器）](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 建议使用资源管理器部署模型，因为经典模型是旧的。
* 确保有一台兼容的 VPN 设备和能够对其进行配置的人员。 有关兼容的 VPN 设备和设备配置的详细信息，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 确认 VPN 设备有一个面向外部的公共 IPv4 地址。
* 如果熟悉本地网络配置中的 IP 地址范围，则需咨询能够提供此类详细信息的人员。 创建此配置时，必须指定 IP 地址范围前缀，Azure 会将该前缀路由到本地位置。 本地网络的任何子网都不得与要连接到的虚拟网络子网重叠。
* 需要使用 PowerShell 来指定共享密钥和创建 VPN 网关连接。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>此练习的示例配置值

本文中的示例使用以下值。 可使用这些值创建测试环境，或参考这些值以更好地理解本文中的示例。 通常，使用地址空间的 IP 地址值时，需要与网络管理员协调，以便避免重叠的地址空间，这可能会影响路由。 在这种情况下，如果要创建工作连接，请将 IP 地址值替换成自己的值。

* **资源组：** TestRG1
* **VNet 名称：** TestVNet1
* **地址空间：** 10.11.0.0/16
* **子网名称：** 前端
* **子网地址范围：** 10.11.0.0/24
* **网关子网：** 10.11.255.0/27
* **区域：** (us) 美国东部
* **本地站点名称：** Site2
* **** 客户端地址空间：位于本地站点的地址空间。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>创建虚拟网络

创建适用于 S2S 连接的虚拟网络时，需确保指定的地址空间与适用于本地站点（需要连接到这些站点）的任何客户端地址空间不重叠。 如果有重叠子网，连接将无法正常工作。

* 如果已有一个 VNet，请验证这些设置是否与 VPN 网关设计兼容。 请特别注意任何可能与其他网络重叠的子网。

* 如果还没有虚拟网络，请创建。 这些屏幕截图仅供参考。 请务必替换成自己的值。

### <a name="to-create-a-virtual-network"></a>创建虚拟网络

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>配置站点和网关

### <a name="to-configure-the-site"></a>配置站点

本地站点通常指本地位置。 它包含 VPN 设备的 IP 地址和地址范围，需要创建到该设备的连接，并且需要通过 VPN 网关将地址范围路由到该设备。

1. 在 VNet 页的 " **设置**" 下，选择 " **站点到站点连接**"。
1. 在 "站点到站点连接" 页上，选择 " **+ 添加**"。
1. 在 " **配置 VPN 连接和网关** " 页上，对于 " **连接类型**"，请选择 " **站点到站点** "。 对于本练习，你将需要结合使用 [示例值](#values) 和你自己的值。

   * **VPN 网关 IP 地址：** 这是本地网络的 VPN 设备的公共 IP 地址。 VPN 设备需要 IPv4 公共 IP 地址。 为要连接到的 VPN 设备指定一个有效的公共 IP 地址。 它必须可由 Azure 访问。 如果不知道 VPN 设备的 IP 地址，则始终可以先添加一个占位符值（只要其格式是有效的公共 IP 地址），等到以后再更改。

   * **客户端地址空间:** 列出一个 IP 地址范围，需通过该网关将此范围路由到本地网络。 可以添加多个地址空间范围。 请确保在此处指定的范围与虚拟网络连接到的其他网络的范围不重叠，也与虚拟网络本身的地址范围不重叠。
1. 在页面底部，不要选择 "查看 + 创建"。 相反，选择 " **下一步：网关>**。

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>配置虚拟网络网关

1. 在 " **网关** " 页上，选择以下值：

   * **大小：** 这是用于创建虚拟网络网关的网关 SKU。 经典 VPN 使用老版（旧版）网关 SKU。 有关旧版网关 SKU 的详细信息，请参阅[使用虚拟网关 SKU（老版 SKU）](vpn-gateway-about-skus-legacy.md)。 对于此练习，可以选择 " **标准** "。

   * **路由类型：** 为网关选择路由类型。 这也称为 VPN 类型。 选择正确的类型很重要，因为无法将网关从一个类型转换为另一个类型。 VPN 设备必须兼容所选路由类型。 有关路由类型的详细信息，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 可能会有文章引用“RouteBased”和“PolicyBased”VPN 类型。 “动态”对应于“RouteBased”，“静态”对应于“PolicyBased”。 通常，您需要动态路由。

   * **网关子网：** 指定的网关子网的大小取决于要创建的 VPN 网关配置。 尽管网关子网最小可以创建为 /29，但建议使用 /27 或 /28。 这样可以创建较大的子网，包含的地址更多。 使用更大的网关子网可以有足够的 IP 地址来应对未来可能会有的配置。

1. 选择页面底部的 " **查看 + 创建** " 来验证设置。 选择 " **创建** " 以进行部署。 创建虚拟网络网关可能需要长达45分钟的时间，具体取决于所选网关 SKU。

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>配置 VPN 设备

通过站点到站点连接连接到本地网络需要 VPN 设备。 在此步骤中，请配置 VPN 设备。 配置 VPN 设备时，需要以下值：

* 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
* 虚拟网络网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>检索值

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>创建连接

> [!NOTE]
> 对于经典部署模型，此步骤在 Azure 门户或通过 Azure Cloud Shell 不可用。 必须从桌面本地使用 Service Management (SM) 版本的 Azure PowerShell cmdlet。
>

在此步骤中，使用前面步骤中的值设置共享密钥并创建连接。 设置的密钥必须是在 VPN 设备配置中使用过的同一密钥。

1. 设置共享密钥并创建连接。

   * 更改-VNetName 值和-LocalNetworkSiteName 值。 指定包含空格的名称时，请使用单引号将值引起来。
   * "-SharedKey" 是你生成的值，然后指定。 在此示例中，我们使用了 "abc123"，但你可以 (，并且应) 生成更复杂的内容。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. 创建连接后，结果为“状态: 成功”****。

## <a name="verify-your-connection"></a><a name="verify"></a>验证连接

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

如果无法进行连接，请参阅左窗格目录的“故障排除”**** 部分。

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>如何重置 VPN 网关

如果丢失一个或多个站点到站点隧道上的跨界 VPN 连接，重置 VPN 网关可有效解决该情况。 在此情况下，本地 VPN 设备都在正常工作，但却无法与 Azure VPN 网关建立 IPsec 隧道。 有关步骤，请参阅[重置 VPN 网关](vpn-gateway-resetgw-classic.md#resetclassic)。

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>如何更改网关 SKU

有关更改网关 SKU 的步骤，请参阅重 [设网关 Sku 大小](vpn-gateway-about-SKUS-legacy.md#classicresize)。

## <a name="next-steps"></a>后续步骤

* 连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/)。
* 有关强制隧道的信息，请参阅 [关于强制隧道](vpn-gateway-about-forced-tunneling.md)。
