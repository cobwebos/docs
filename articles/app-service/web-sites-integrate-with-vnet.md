---
title: 将应用与 Azure 虚拟网络集成
description: 将 Azure App Service 中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673222"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure App Service 虚拟网络集成功能，以及如何在[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中对应用进行设置。 使用[Azure 虚拟网络][VNETOverview]（vnet）可以将多个 Azure 资源置于非 internet 可路由网络中。  

Azure App Service 有两种变体。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成 

1. 在应用服务门户中转到 "网络 UI"。 在 "VNet 集成" 下，选择 *"单击此处进行配置"* 。 

1. 选择“添加 VNet”。  

   ![选择 VNet 集成][1]

1. 下拉列表将在同一区域中包含订阅中的所有资源管理器 Vnet，以下是所有其他区域中所有资源管理器 Vnet 的列表。 选择要与之集成的 VNet。

   ![选择 VNet][2]

   * 如果 VNet 位于同一区域，则创建一个新的子网或选择一个空的预先存在的子网。 

   * 若要选择另一个区域中的 VNet，必须启用 "点到站点" 设置的虚拟网络网关。

   * 若要与经典 VNet 集成，请选择 "**单击此处以连接到经典 vnet**"，而不是单击 "vnet" 下拉。 选择所需的经典 VNet。 目标 VNet 必须已为启用了点到站点配置的虚拟网络网关。

    ![选择经典 VNet][3]
    
在集成期间，应用会重启。  完成集成后，你将看到与集成的 VNet 有关的详细信息。 

应用与 VNet 集成后，它将使用与 VNet 配置的 DNS 服务器相同的 DNS 服务器，除非 Azure DNS 专用区域。 目前不能将 VNet 与 Azure DNS 专用区域结合使用。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用的托管计划，其中不存在其他客户工作负荷在同一辅助角色上运行。 区域 VNet 集成通过使用委托子网中的地址装载虚拟接口来工作。 由于 "发件人" 地址在 VNet 中，因此它可以在中或通过 VNet 访问大多数功能，就像 VNet 中的 VM 一样。 网络实现不同于在 VNet 中运行 VM，这就是在使用此功能时某些网络功能尚不可用的原因。

![区域 VNet 集成的工作原理][5]

启用区域 VNet 集成后，应用仍将通过与普通通道相同的通道对 internet 进行出站调用。 应用属性门户中列出的出站地址仍是应用使用的地址。 应用的更改是：对服务终结点保护的服务或 RFC 1918 地址的调用将进入 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，则可以将所有出站流量发送到 VNet 中。 

此功能仅支持每个辅助角色一个虚拟接口。  每个辅助角色一个虚拟接口意味着每个应用服务计划一个区域 VNet 集成。 同一应用服务计划中的所有应用都可使用相同的 VNet 集成，但如果需要一个应用来连接到其他 VNet，则需要创建另一个应用服务计划。 使用的虚拟接口不是客户可直接访问的资源。

由于此技术的工作原理，与 VNet 集成一起使用的流量不会在网络观察程序或 NSG 流日志中显示。  

## <a name="gateway-required-vnet-integration"></a>网关所需的 VNet 集成

网关所需的 VNet 集成支持连接到另一区域中的 VNet，或连接到经典 VNet。 网关所需的 VNet 集成： 

* 允许应用一次只连接到1个 VNet
* 允许在应用服务计划中集成最多5个 Vnet 
* 允许应用服务计划中的多个应用使用同一个 VNet，而不会影响应用服务计划可以使用的总数。  如果在同一应用服务计划中有六个使用同一 VNet 的应用，则会将其计为使用1个 VNet。 
* 由于网关上的 SLA，支持99.9% 的 SLA
* 使你的应用能够使用配置了 VNet 的 DNS
* 需要一个使用 SSTP 点到站点 VPN 配置的基于虚拟网络路由的网关，然后才能将它连接到应用。 

不能使用网关所需的 VNet 集成：

* 与 Linux 应用
* 使用与 ExpressRoute 连接的 VNet 
* 访问服务终结点保护的资源
* 使用支持 ExpressRoute 和点到站点/站点到站点 Vpn 的共存网关

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中设置网关 ###

若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。  

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置 "点到站点地址"][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在 "点到站点配置" 中禁用 IKEV2，并且必须选择 SSTP。 点到站点地址空间必须在 RFC 1918 地址块中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果只是创建用于应用服务 VNet 集成的网关，则无需上载证书。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。 

### <a name="how-gateway-required-vnet-integration-works"></a>网关所需的 VNet 集成工作原理

网关需要在点到站点 VPN 技术之上构建的 VNet 集成。 点到站点 Vpn 将网络访问限制为仅托管应用的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 当你的应用程序配置为使用网关所需的 VNet 集成时，会代表你自行管理一种复杂的协商，以便在网关和应用程序端创建和分配证书。 最终的结果是，用于托管应用程序的工作人员能够直接连接到所选 VNet 中的虚拟网络网关。 

![网关所需的 VNet 集成工作原理][6]

### <a name="accessing-on-premises-resources"></a>访问本地资源

应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用网关所需的 VNet 集成，则需要使用点到站点地址块来更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 具体操作信息取决于每个网关，在此不作说明。 不能为 BGP 配置站点到站点 VPN 连接。

区域 VNet 集成功能无需其他配置即可通过 VNet 和本地访问。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地。 

> [!NOTE]
> 网关所需的 VNet 集成功能不会将应用与具有 ExpressRoute 网关的 VNet 集成。 即使 ExpressRoute 网关配置为[共存模式][VPNERCoex]，VNet 集成也不起作用。 如果需要通过 ExpressRoute 连接访问资源，可以使用区域 VNet 集成功能或在 VNet 中运行的[应用服务环境][ASE]。 
> 
> 

### <a name="peering"></a>对等互连

如果使用与区域 VNet 集成的对等互连，则无需进行任何其他配置。 

如果你使用的是网关，则需要配置其他一些项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许网关传输”。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许远程网关”。
1. 转到门户中的“应用服务计划”>“网络”>“VNet 集成 UI”。  选择应用连接的 VNet。 在路由部分下，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。  

## <a name="managing-vnet-integration"></a>管理 VNet 集成 

在应用级别连接和断开与 VNet 的连接。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 从应用 > 网络 > VNet 集成门户，你可以获取有关 VNet 的详细信息。 可以在 asp > 网络 > VNet 集成门户中查看 ASP 级别的类似信息。

在 VNet 集成的应用视图中，能够执行的唯一操作是断开应用与当前所连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”。 在从 VNet 断开连接时，应用将会重启。 断开连接操作不会更改 VNet。 子网或网关未删除。 如果随后想要删除 VNet，需要先将应用从 VNet 断开连接，并删除其中的资源，例如网关。 

ASP VNet 集成 UI 将显示 ASP 中的应用使用的所有 VNet 集成。 要查看每个 VNet 的详细信息，请单击感兴趣的 VNet。 对于网关所需的 VNet 集成，可以在此处执行两个操作。

* **同步网络**。 同步网络操作仅适用于与网关相关的 VNet 集成功能。 执行同步网络操作可确保证书和网络信息同步。如果添加或更改 VNet 的 DNS，则需要执行**同步网络**操作。 此操作会重启所有使用此 VNet 的应用。
* **添加路由** 添加路由会驱动出站流量进入 VNet。 

**网关所需的 VNet 集成路由**在 VNet 中定义的路由用于将流量从应用定向到 VNet。 如果需要将其他出站流量发送到 VNet 中，则可以在此处添加地址块。 此功能仅适用于网关所需的 VNet 集成。 当使用网关所需的 VNet 集成时，路由表不会影响应用程序流量，这与区域 VNet 集成的方式相同。

**网关所需的 VNet 集成证书**当网关需要启用 VNet 集成时，需要使用证书交换以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。
如果更改了证书或网络信息，则需单击“同步网络”。 单击“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。 

## <a name="pricing-details"></a>定价详细信息
除了 ASP 定价层收费以外，区域 VNet 集成功能不收取额外费用。

使用网关所需的 VNet 集成功能有三个相关费用：

* ASP 定价层费用-应用需要位于标准、高级或 PremiumV2 应用服务计划中。 可在此处查看有关这些成本的更多详细信息：[应用服务定价][ASPricing]。 
* 数据传输成本-即使 VNet 位于同一数据中心，也会对数据传出收费。 [数据传输定价详细信息][DataPricing]中介绍了这些费用。 
* VPN 网关成本-对于点到站点 VPN，VNet 网关需要付费。 详细信息位于[VPN 网关定价][VNETPricing]页。

## <a name="troubleshooting"></a>故障排除

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

对于区域 VNet 集成，CLI 支持。 若要访问以下命令，请[安装 Azure CLI][installCLI]。 

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

对于网关必需的 VNet 集成，你可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 对于准备好运行的脚本，请参阅[将 Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


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
