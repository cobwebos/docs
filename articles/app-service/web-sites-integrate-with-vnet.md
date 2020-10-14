---
title: 将应用与 Azure 虚拟网络集成
description: 将 Azure 应用服务中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f2e17e99208d076d05132638b5161a284b73986f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018621"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络集成

本文介绍 Azure 应用服务 VNet 集成功能，并介绍如何为 [Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)中的应用设置此功能。 使用 [Azure 虚拟网络][VNETOverview] (VNet) 可将多个 Azure 资源置于无法通过 Internet 路由的网络中。 使用 VNet 集成功能，你的应用可通过 VNet 访问中的资源。 VNet 集成不允许私下访问应用。

Azure App Service 在 VNet 集成功能上有两种变化形式：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成

1. 在应用服务门户中转到“网络”UI。 在“VNet 集成”下，选择“单击此处进行配置”。 

1. 选择“添加 VNet”。

   ![选择 VNet 集成][1]

1. 下拉列表包含订阅内位于相同区域中的所有 Azure 资源管理器虚拟网络。 下面是所有其他区域中资源管理器虚拟网络的列表。 选择要集成的 VNet。

   ![选择 VNet][2]

   * 如果 VNet 位于同一区域，要么创建一个新的子网，要么选择一个已有的空子网。
   * 若要选择另一个区域中的 VNet，必须预配了一个已启用点到站点连接的 VNet 网关。
   * 若要与经典 VNet 集成，请不要选择“虚拟网络”下拉列表，而应选择“单击此处连接到经典 VNet”。  选择所需的经典虚拟网络。 目标 VNet 中必须已预配一个启用了点到站点连接的虚拟网关。

    ![选择经典 VNet][3]

在集成期间，应用会重启。 完成集成后，系统将显示你与之集成的 VNet 的详细信息。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用托管计划，其中不会有任何其他客户的工作负载在同一辅助角色上运行。 区域 VNet 集成通过使用委托子网中的地址装载虚拟接口实现。 发送地址位于 VNet 中，因此它可以像 VNet 中的 VM 那样，访问位于 VNet 中或通过 VNet 传输的大多数内容。 网络实现不同于在 VNet 中运行 VM。 这就是一些网络功能尚不可用于此功能的原因。

![区域 VNet 集成的工作原理][5]

启用区域 VNet 集成后，应用通过往常所用的通道对 Internet 进行出站调用。 应用属性门户中列出的出站地址是应用仍然在使用的地址。 就应用而言，变化在于：对服务终结点保护服务的调用或者 RFC 1918 地址进入 VNet 中。 如果 WEBSITE_VNET_ROUTE_ALL 设置为 1，所有出站流量都可以被发送到 VNet 中。

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` Windows 容器当前不支持。
> 

此功能仅支持每个辅助角色一个虚拟接口。 每个辅助角色一个虚拟接口意味着每个应用服务计划一个区域 VNet 集成。 同一个应用服务计划中的所有应用都可以使用相同的 VNet 集成。 如果需要使用一个应用来连接其他 VNet，你需要另外创建一个应用服务计划。 使用的虚拟接口不是客户可直接访问的资源。

由于此技术的性质，用于 VNet 集成的流量不显示在 Azure 网络观察程序或 NSG 流日志中。

## <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成

需要网关的 VNet 集成支持连接到另一区域中的 VNet，或连接到经典虚拟网络。 需要网关的 VNet 集成：

* 允许应用一次只连接到一个 VNet。
* 允许在一个应用服务计划中最多集成 5 个 VNet。
* 允许在应用服务计划中由多个应用使用同一个 VNet，不影响可供应用服务计划使用的总数。 如果有 6 个应用在使用同一应用服务计划中的同一 VNet，则算作是使用了一个 VNet。
* 由于 SLA 是基于网关，因此可实现 99.9% 的 SLA。
* 允许应用使用配置给 VNet 的 DNS。
* 需要在基于虚拟网络路由的网关中配置 SSTP 点到站点 VPN，然后才能将其连接到应用。

需要网关的 VNet 集成不可用于：

* 通过 Azure ExpressRoute 连接的 VNet。
* 从 Linux 应用程序。
* 从 [Windows 容器](quickstart-custom-container.md)。
* 访问服务终结点保护的资源。
* 既支持 ExpressRoute，也支持点到站点 VPN 或站点到站点 VPN 的共存网关。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在 Azure 虚拟网络中设置网关 ###

若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 点到站点地址空间必须在 RFC 1918 地址块 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16 中。

如果创建用于应用服务 VNet 集成的网关，则不需要上传证书。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。

### <a name="how-gateway-required-vnet-integration-works"></a>需要网关的 VNet 集成的工作原理

需要网关的 VNet 集成基于点到站点 VPN 技术。 点到站点 VPN 将网络访问限制于可托管应用的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 通过门户将应用配置为使用需要网关的 VNet 集成后，系统会代你管理复杂的协商，以便在网关上和应用程序端创建并分配证书。 结果是，用于托管应用的辅助角色能够直接连接到所选 VNet 中的虚拟网关。

![需要网关的 VNet 集成的工作原理][6]

### <a name="access-on-premises-resources"></a>访问本地资源

应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用需要网关的 VNet 集成，请使用点到站点地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 操作详情取决于每个网关，在此不作说明。 你不能使用站点到站点 VPN 连接配置 BGP。

区域 VNet 集成功能无需额外的配置即可通过 VNet 连接到本地资源。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地资源。

> [!NOTE]
> 需要网关的 VNet 集成功能不将应用与包含 ExpressRoute 网关的 VNet 集成。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会生效。 如果需要通过 ExpressRoute 连接访问资源，请使用区域 VNet 集成功能或在 VNet 中运行的[应用服务环境][ASE]。
>
>

### <a name="peering"></a>对等互连

如果将对等互连与区域 VNet 集成结合使用，无需进行任何其他配置。

如果将需要网关的 VNet 集成与对等互连结合使用，需要额外配置几个项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，请启用“允许虚拟网络访问”并选择“允许转发流量”和“允许网关传输”  。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，请启用“允许虚拟网络访问”并选择“允许转发流量”和“允许远程网关”  。
1. 在门户中转到“应用服务计划” > “网络” > “VNet 集成”UI。 选择应用连接的 VNet。 在路由部分，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。

## <a name="manage-vnet-integration"></a>管理 VNet 集成

与 VNet 连接和断开连接都在应用级别进行的。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 可以通过应用 >“网络” > “VNet 集成”门户获取 VNet 的详细信息。 可以在“应用服务计划” > “网络” > “VNet 集成”门户中查看应用服务计划级别的类似信息。

在 VNet 集成实例的应用视图中，能够执行的唯一操作是断开应用与当前连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”。 断开与 VNet 的连接后，应用会重启。 断开连接操作不会更改 VNet。 不会删除子网或网关。 若要删除 VNet，请先断开应用与该 VNet 的连接，然后删除该 VNet 中的资源，例如网关。

应用服务计划 VNet 集成 UI 会显示应用服务计划中的应用使用的所有 VNet 集成。 若要查看单个 VNet 的详细信息，请选择你感兴趣的 VNet。 在此处，可以针对需要网关的 VNet 集成执行两项操作：

* **同步网络**：同步网络操作仅用于网关相关的 VNet 集成功能。 执行同步网络操作确保了证书与网络信息是同步的。如果添加或更改 VNet 的 DNS，请执行同步网络操作。 此操作重启使用此 VNet 的任何应用。 如果你使用的是属于不同订阅的应用和 VNet，此操作无效。
* **添加路由**：添加路由会促使出站流量进入 VNet。

### <a name="gateway-required-vnet-integration-routing"></a>需要网关的 VNet 集成路由
在 VNet 中定义的路由用于将流量从应用导入 VNet。 如果需要将其他出站流量发送到 VNet 中，请在此处添加地址块。 此功能仅适用于需要网关的 VNet 集成。 使用需要网关的 VNet 集成时，路由表不会像使用区域 VNet 集成时那样影响应用流量。

### <a name="gateway-required-vnet-integration-certificates"></a>需要网关的 VNet 集成证书
启用需要网关的 VNet 集成后，必须进行证书交换以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。

如果更改了证书或网络信息，请选择“同步网络”。 选择“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。

## <a name="pricing-details"></a>定价详细信息
除了应用服务计划定价层收费以外，区域 VNet 集成功能没有其他使用费。

使用需要网关的 VNet 集成功能涉及三项费用：

* **应用服务计划定价层费用**：应用需要处于标准、高级、PremiumV2 或 PremiumV3 应用服务计划中。 有关这些费用的详细信息，请参阅[应用服务定价][ASPricing]。
* **数据传输费用**：传出数据会产生费用，即使 VNet 位于同一数据中心也是如此。 [数据传输定价详细信息][DataPricing]中对这些费用进行了说明。
* **VPN 网关费用**：点到站点 VPN 所需的虚拟网关会产生费用。 有关详细信息，请参阅 [VPN 网关定价][VNETPricing]。

## <a name="troubleshooting"></a>疑难解答

> [!NOTE]
> 应用服务中的 Docker Compose 方案不支持 VNET 集成。
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

为区域 VNet 集成提供了 CLI 支持。 要访问以下命令，请[安装 Azure CLI][installCLI]。

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

还提供了对区域 VNet 集成的 PowerShell 支持，但你必须使用子网 resourceID 的属性数组创建通用资源

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


对于需要网关的 VNet 集成，可以使用 PowerShell 将应用服务与 Azure 虚拟网络相集成。 如需随时可运行的脚本，请参阅[将 Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md