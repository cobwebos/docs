---
title: 将应用与 Azure 虚拟网络进行集成
description: 将 Azure 应用服务中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673222"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure 应用服务虚拟网络集成功能，并说明如何在 [Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)中使用应用对其进行设置。 使用 [Azure 虚拟网络][VNETOverview] (VNet) 可将多个 Azure 资源置于无法通过 Internet 路由的网络中。  

Azure 应用服务有两种变体。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成 

1. 转到应用服务门户中的网络 UI。 在 VNet 集成下，选择 *"单击此处进行配置"。* 

1. 选择“添加 VNet”****。  

   ![选择 VNet 集成][1]

1. 下拉列表将包含同一区域中订阅中的所有资源管理器 VNet，下面是所有其他区域中所有资源管理器 VNet 的列表。 选择要集成的 VNet。

   ![选择 VNet][2]

   * 如果 VNet 位于同一区域中，则创建新子网或选取空预先存在的子网。 

   * 要选择另一个区域中的 VNet，必须启用指向站点的虚拟网络网关。

   * 要与经典 VNet 集成，请选择"**单击此处连接到经典 VNet"，** 而不是单击 VNet 下拉列表。 选择所需的经典 VNet。 目标 VNet 必须已预配了启用指向站点的虚拟网络网关。

    ![选择经典 VNet][3]
    
在集成期间，应用会重启。  集成完成后，您将在集成的 VNet 上看到详细信息。 

应用与 VNet 集成后，它将使用 VNet 配置的相同 DNS 服务器，除非它是 Azure DNS 专用区域。 当前无法使用 VNet 集成与 Azure DNS 专用区域。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用的托管计划，其中没有其他客户工作负载在同一工作人员上运行。 区域 VNet 集成的工作原理是安装具有委派子网中地址的虚拟接口。 由于 from 地址位于 VNet 中，因此它可以像 VNet 中的 VM 一样，访问 VNet 中或通过 VNet 中的大多数内容。 网络实现不同于在 VNet 中运行 VM，这就是为什么在使用此功能时某些网络功能尚不可用的原因。

![区域 VNet 集成的工作原理][5]

启用区域 VNet 集成后，你的应用仍将通过与正常相同的渠道向 Internet 进行出站呼叫。 应用属性门户中列出的出站地址仍然是应用使用的地址。 应用有哪些更改，对服务终结点安全服务的呼叫或 RFC 1918 地址将进入 VNet。 如果WEBSITE_VNET_ROUTE_ALL设置为 1，则所有出站流量都可以发送到您的 VNet。 

该功能仅支持每个工作人员一个虚拟接口。  每个工作人员一个虚拟接口意味着每个应用服务计划的一个区域 VNet 集成。 同一应用服务计划中的所有应用都可以使用相同的 VNet 集成，但如果需要应用连接到其他 VNet，则需要创建另一个应用服务计划。 使用的虚拟接口不是客户可以直接访问的资源。

由于此技术运行方式的性质，与 VNet 集成一起使用的流量不会显示在网络观察程序或 NSG 流日志中。  

## <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成

网关所需的 VNet 集成支持连接到另一个区域中的 VNet 或经典 VNet。 网关所需的 VNet 集成： 

* 允许应用一次只连接到 1 个 VNet
* 使多达五个 VNet 集成到应用服务计划中 
* 允许在应用服务计划中由多个应用使用同一个 VNet，不影响可供应用服务计划使用的总数。  如果同一应用服务计划中有六个应用使用相同的 VNet，则这计为正在使用的 1 个 VNet。 
* 由于网关上的 SLA，可实现 99.9% 的 SLA
* 使应用能够使用 VNet 配置的 DNS
* 需要使用 SSTP 点对点 VPN 配置的基于虚拟网络路由的网关才能连接到应用。 

不能使用网关所需的 VNet 集成：

* 使用 Linux 应用程序
* 与快速路由连接 VNet 
* 访问服务终结点安全资源
* 具有同时支持 ExpressRoute 并指向站点/站点到站点 VPN 的共存网关

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中设置网关 ###

若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。  

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在站点配置点中禁用 IKEV2，并且必须选择 SSTP。 点到站点地址空间必须在 RFC 1918 地址块中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果只是创建网关用于应用服务 VNet 集成，则不需要上传证书。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。 

### <a name="how-gateway-required-vnet-integration-works"></a>网关要求 VNet 集成的工作原理

网关需要 VNet 集成构建在点对点 VPN 技术之上。 指向站点 VPN 将网络访问限制为仅托管应用程序的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 当应用配置了门户以使用所需的网关 VNet 集成时，将代表您管理复杂的协商，以在网关和应用程序端创建和分配证书。 最终结果是，用于托管应用的工作人员能够直接连接到所选 VNet 中的虚拟网络网关。 

![网关要求 VNet 集成的工作原理][6]

### <a name="accessing-on-premises-resources"></a>访问本地资源

应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用网关所需的 VNet 集成，需要使用点到站点地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 具体操作信息取决于每个网关，在此不作说明。 不能使用站点到站点 VPN 连接配置 BGP。

区域 VNet 集成功能无需经过其他配置即可访问 VNet 和本地。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地。 

> [!NOTE]
> 需要网关的 VNet 集成功能不会将应用与包含 ExpressRoute 网关的 VNet 集成。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会生效。 如果需要通过 ExpressRoute 连接访问资源，则可以使用区域 VNet 集成功能或 VNet 中运行的[应用服务环境][ASE]。 
> 
> 

### <a name="peering"></a>对等互连

如果对区域 VNet 集成使用对等互连，则不需要进行任何附加的配置。 

如果结合对等互连使用网关所需的 VNet 集成，则需要配置几个附加的项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许网关传输”************。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许远程网关”************。
1. 转到门户中的“应用服务计划”>“网络”>“VNet 集成 UI”。  选择应用连接的 VNet。 在路由部分下，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。  

## <a name="managing-vnet-integration"></a>管理 VNet 集成 

与 VNet 连接和断开连接处于应用级别。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 可以通过“应用 > 网络 > VNet 集成门户”获取 VNet 的详细信息。 您可以在 ASP > 网络> VNet 集成门户的 ASP 级别看到类似的信息。

在 VNet 集成的应用视图中，能够执行的唯一操作是断开应用与当前所连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”****。 在从 VNet 断开连接时，应用将会重启。 断开连接操作不会更改 VNet。 不会删除子网或网关。 若要删除 VNet，需要先从该 VNet 断开连接应用，然后删除该 VNet 中的资源，例如网关。 

ASP VNet 集成 UI 将显示 ASP 中的应用使用的所有 VNet 集成。 要查看每个 VNet 的详细信息，请单击感兴趣的 VNet。 对于网关所需的 VNet 集成，您可以在此处执行两个操作。

* **同步网络**。 同步网络操作仅适用于网关相关的 VNet 集成功能。 执行同步网络操作可确保证书和网络信息同步。如果添加或更改 VNet 的 DNS，则需要执行**同步网络**操作。 此操作会重启所有使用此 VNet 的应用。
* **添加路由** 添加路由会驱动出站流量进入 VNet。 

**网关所需的 VNet 集成路由**VNet 中定义的路由用于从应用将流量定向到 VNet。 如果需要将其他出站流量发送到 VNet 中，则可以在此处添加地址块。 此功能只适用于网关所需的 VNet 集成。 路由表在使用网关时不会影响应用流量，就像使用区域 VNet 集成那样需要 VNet 集成。

**网关所需的 VNet 集成证书**启用网关要求 VNet 集成时，需要交换证书以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。
如果更改了证书或网络信息，则需单击“同步网络”。 单击“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。 

## <a name="pricing-details"></a>定价详细信息
除 ASP 定价层的费用外，区域 VNet 集成功能不会产生额外的使用费。

使用网关所需的 VNet 集成功能涉及到三种相关费用：

* ASP 定价层费用 - 应用需要属于“标准”、“高级”或“高级 V2”应用服务计划。 可在此处了解此方面费用的更多详细信息：[应用服务定价][ASPricing]。 
* 数据传输费用 - 数据传出会产生费用，即使 VNet 在同一数据中心也是如此。 [数据传输定价详细信息][DataPricing]中对这些费用进行了说明。 
* VPN 网关费用 - 点到站点 VPN 所需的 VNet 网关会产生费用。 [VPN 网关定价][VNETPricing]页上介绍了详细信息。

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

CLI 支持区域 VNet 集成。 要访问以下命令，[请安装 Azure CLI][installCLI]。 

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

对于网关所需的 VNet 集成，可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 对于准备好运行的脚本，请参阅[将 Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


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
