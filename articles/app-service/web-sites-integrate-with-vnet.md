---
title: 将应用与 Azure 虚拟网络进行集成
description: 将 Azure App Service 中的应用集成到 Azure 虚拟网络。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770841"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络集成

本文介绍 Azure App Service VNet 集成功能，以及如何在[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中对应用进行设置。 使用[Azure 虚拟网络][VNETOverview]（vnet），可以将多个 Azure 资源置于非 internet 可路由网络中。

Azure App Service 有两种变体：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成

> [!NOTE]
> 如果 Linux 应用的菜单中的 "网络" 边栏选项卡处于禁用状态（灰显），则表示该功能当前不可用。
>

1. 在应用服务门户中转到 "**网络**UI"。 在 " **VNet 集成**" 下，选择 **"单击此处进行配置**"。

1. 选择“添加 VNet”****。

   ![选择 VNet 集成][1]

1. 下拉列表包含订阅中的所有 Azure 资源管理器虚拟网络在同一区域中。 下面是所有其他区域中资源管理器虚拟网络的列表。 选择要与之集成的 VNet。

   ![选择 VNet][2]

   * 如果 VNet 位于同一区域，请创建新的子网或选择一个空的预先存在的子网。
   * 若要选择另一个区域中的 VNet，必须启用 "点到站点" 配置的 VNet 网关。
   * 若要与经典 VNet 集成，请选择 "**单击此处以连接到经典 vnet**"，而不是选择 "**虚拟网络**" 下拉列表。 选择所需的经典虚拟网络。 目标 VNet 必须已启用了点到站点配置的虚拟网络网关。

    ![选择经典 VNet][3]

在集成期间，应用会重启。 完成集成后，你将看到有关要集成的 VNet 的详细信息。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用的托管计划，其中不会有其他客户的工作负荷在同一辅助角色上运行。 区域 VNet 集成通过使用委托子网中的地址装载虚拟接口来工作。 由于 "发件人" 地址在 VNet 中，因此它可以通过 VNet 或通过 VNet 访问大多数项目，如 VNet 中的 VM。 网络实现不同于在 VNet 中运行 VM。 这就是为什么某些网络功能尚不可用于此功能的原因。

![区域 VNet 集成的工作原理][5]

启用区域 VNet 集成后，应用将通过与普通通道相同的通道对 internet 进行出站调用。 应用属性门户中列出的出站地址是你的应用仍在使用的地址。 对应用程序所做的更改是对服务终结点保护服务的调用，或 RFC 1918 地址进入 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，则可以将所有出站流量发送到 VNet 中。

此功能仅支持每个辅助角色一个虚拟接口。 每个辅助角色一个虚拟接口意味着每个应用服务计划一个区域 VNet 集成。 同一应用服务计划中的所有应用都可使用相同的 VNet 集成。 如果需要某个应用连接到其他 VNet，需要创建另一个应用服务计划。 使用的虚拟接口不是客户可直接访问的资源。

由于此技术的工作原理，Azure 网络观察程序或 NSG 流日志中不显示与 VNet 集成一起使用的流量。

## <a name="gateway-required-vnet-integration"></a>网关-必需的 VNet 集成

网关-必需的 VNet 集成支持连接到其他区域中的 VNet 或连接到经典虚拟网络。 网关-必需的 VNet 集成：

* 允许应用一次只连接到一个 VNet。
* 在应用服务计划中，最多可以集成5个 Vnet。
* 允许应用服务计划中的多个应用使用同一个 VNet，而不会影响应用服务计划可使用的总数。 如果在同一应用服务计划中有六个使用同一 VNet 的应用，则会将其计为使用的一个 VNet。
* 由于网关的 SLA，支持 99.9% SLA。
* 使你的应用能够使用为 VNet 配置的 DNS。
* 需要一个使用 SSTP 点到站点 VPN 配置的基于虚拟网络路由的网关，然后才能将它连接到应用。

不能使用网关所需的 VNet 集成：

* 适用于 Linux 的应用。
* 使用与 Azure ExpressRoute 连接的 VNet。
* 访问服务终结点保护的资源。
* 使用支持 ExpressRoute 和点到站点或站点到站点 Vpn 的共存网关。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在 Azure 虚拟网络中设置网关 ###

若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。  

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 点到站点地址空间必须为 RFC 1918 地址块 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16。

如果创建用于应用服务 VNet 集成的网关，则无需上载证书。 创建网关可能需要 30 分钟。 在配置网关之前，你将无法将应用与 VNet 集成。

### <a name="how-gateway-required-vnet-integration-works"></a>网关所需的 VNet 集成工作原理

网关-必需的 VNet 集成基于点到站点 VPN 技术。 点到站点 Vpn 将网络访问限制在托管应用的虚拟机上。 应用只能通过混合连接或 VNet 集成将流量发送到 internet。 当你的应用程序配置为使用网关所需的 VNet 集成时，会代表你自行管理一种复杂的协商，以便在网关和应用程序端创建和分配证书。 结果是，用于托管应用的工作人员能够直接连接到所选 VNet 中的虚拟网络网关。

![网关所需的 VNet 集成工作原理][6]

### <a name="access-on-premises-resources"></a>访问本地资源

应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用网关必需的 VNet 集成，请使用点到站点地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 每个网关如何实现此操作的详细信息，此处未进行描述。 不能为 BGP 配置站点到站点 VPN 连接。

区域 VNet 集成功能无需额外配置即可通过 VNet 连接到本地资源。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地资源。

> [!NOTE]
> 网关所需的 VNet 集成功能不会将应用与具有 ExpressRoute 网关的 VNet 集成。 即使 ExpressRoute 网关配置为[共存模式][VPNERCoex]，VNet 集成也不起作用。 如果需要通过 ExpressRoute 连接访问资源，请使用区域 VNet 集成功能或在 VNet 中运行的[应用服务环境][ASE]。
> 
> 

### <a name="peering"></a>对等互连

如果将对等互连与区域 VNet 集成结合使用，则无需进行任何其他配置。

如果将网关所需的 VNet 集成与对等互连结合使用，则需要配置几个附加项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 添加对等连接时，启用 "**允许虚拟网络访问**"，然后选择 "**允许转发的流量**" 和 "**允许网关传输**"。
1. 在 VNet 中添加对等互连连接，该连接将被对等互连到你连接到的 VNet。 在目标 VNet 中添加对等互连连接时，启用 "**允许虚拟网络访问**"，并选择 "**允许转发的流量**" 和 "**允许远程网关**"。
1. 在门户中，请参阅**应用服务计划** > **网络** > **VNet 集成**UI。 选择应用连接的 VNet。 在 "路由" 部分下，添加与应用连接到的 VNet 对等互连的 VNet 的地址范围。

## <a name="manage-vnet-integration"></a>管理 VNet 集成

在应用级别连接和断开与 VNet 的连接。 可能会影响跨多个应用的 VNet 集成的操作位于应用服务计划级别。 通过应用 >**网络** > **VNet 集成**门户，你可以获取有关 VNet 的详细信息。 你可以在**应用服务计划** > **网络** > **VNet 集成**门户的 "应用服务计划" 级别查看类似的信息。

你可以在 VNet 集成实例的 "应用" 视图中执行的唯一操作是断开应用与当前连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”****。 当你从 VNet 断开连接时，你的应用程序将重新启动。 断开连接操作不会更改 VNet。 子网或网关未删除。 如果随后想要删除 VNet，请先将应用从 VNet 断开连接，并删除其中的资源，例如网关。

应用服务计划 VNet 集成 UI 显示应用服务计划中的应用使用的所有 VNet 集成。 若要查看每个 VNet 的详细信息，请选择你感兴趣的 VNet。 对于网关所需的 VNet 集成，可在此处执行两项操作：

* **同步网络**：同步网络操作仅用于依赖于网关的 VNet 集成功能。 执行同步网络操作可确保证书和网络信息同步。如果添加或更改 VNet 的 DNS，请执行同步网络操作。 此操作将重新启动任何使用此 VNet 的应用。
* **添加路由**：添加路由用于驱动到 VNet 的出站流量。

### <a name="gateway-required-vnet-integration-routing"></a>网关-必需的 VNet 集成路由
在 VNet 中定义的路由用于将流量从应用定向到 VNet。 若要将额外的出站流量发送到 VNet，请在此处添加这些地址块。 此功能仅适用于网关必需的 VNet 集成。 当你使用网关所需的 VNet 集成时，路由表不会影响你的应用程序流量，因为它们与区域 VNet 集成的方式相同。

### <a name="gateway-required-vnet-integration-certificates"></a>网关-必需的 VNet 集成证书
启用网关必需的 VNet 集成后，需要使用证书的交换来确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。

如果证书或网络信息已更改，请选择 "**同步网络**"。 选择 "**同步网络**" 时，会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。

## <a name="pricing-details"></a>定价详细信息
除了应用服务计划定价层收费以外，区域 VNet 集成功能不收取额外费用。

使用网关所需的 VNet 集成功能涉及三个费用：

* **应用服务计划定价层费用**：应用需要位于标准、高级或 PremiumV2 应用服务计划中。 有关这些成本的详细信息，请参阅[应用服务定价][ASPricing]。
* **数据传输成本**：数据传出需要支付费用，即使 VNet 处于同一数据中心也是如此。 [数据传输定价详细信息][DataPricing]中介绍了这些费用。
* **VPN 网关成本**：对于点到站点 VPN，需要使用虚拟网络网关。 有关详细信息，请参阅[VPN 网关定价][VNETPricing]。

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

CLI 支持可用于区域 VNet 集成。 若要访问以下命令，请[安装 Azure CLI][installCLI]。

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

对于网关必需的 VNet 集成，你可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 有关随时可运行的脚本，请参阅[将 Azure App Service 中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
