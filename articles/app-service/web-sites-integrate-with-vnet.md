---
title: 将应用与 Azure 虚拟网络集成
description: 将 Azure 应用服务中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a1a9739c444db2e41d55b8876011c066f2e71ca3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421372"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络集成

本文介绍 Azure 应用服务 VNet 集成功能以及如何在[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)中使用应用进行设置。 使用[Azure 虚拟网络][VNETOverview]，可以将许多 Azure 资源放在非 Internet 可路由网络中。

Azure 应用服务有两种变体：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成

1. 转到应用服务门户中的**网络**UI。 在**VNet 集成**下，选择 **"单击此处配置**"。

1. 选择“添加 VNet”****。

   ![选择 VNet 集成][1]

1. 下拉列表包含同一区域中订阅中的所有 Azure 资源管理器虚拟网络。 下面是所有其他区域中的资源管理器虚拟网络的列表。 选择要集成的虚拟网络。

   ![选择虚拟网络][2]

   * 如果虚拟网络位于同一区域中，请创建新子网或选择空的预先存在的子网。
   * 要选择另一个区域中的虚拟网络，必须启用指向站点的虚拟网络网关。
   * 要与经典虚拟网络集成，请选择"**单击此处连接到经典 VNet"，** 而不是选择**虚拟网络**下拉列表。 选择您想要的经典虚拟网络。 目标虚拟网络必须已经启用了启用点对点的虚拟网络网关。

    ![选择经典 VNet][3]

在集成期间，应用会重启。 集成完成后，您将看到有关要集成的虚拟网络的详细信息。

应用与虚拟网络集成后，它将使用虚拟网络配置的相同 DNS 服务器，除非它是 Azure DNS 专用区域。 目前，不能将 VNet 集成与 Azure DNS 专用区域结合使用。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用的托管计划，其中没有其他客户的工作负载在同一工作人员上运行。 区域 VNet 集成的工作原理是安装具有委派子网中地址的虚拟接口。 由于 from 地址位于虚拟网络中，因此它可以像虚拟网络中的 VM 一样在虚拟网络中或通过虚拟网络访问大多数内容。 网络实现不同于在虚拟网络中运行 VM。 这就是为什么某些网络功能尚未用于此功能的原因。

![区域 VNet 集成的工作原理][5]

启用区域 VNet 集成后，你的应用会通过与正常相同的渠道向 Internet 进行出站呼叫。 应用属性门户中列出的出站地址是应用仍在使用的地址。 应用的更改是对服务终结点安全服务的调用，或 RFC 1918 地址进入虚拟网络。 如果WEBSITE_VNET_ROUTE_ALL设置为 1，则所有出站流量都可以发送到虚拟网络。

该功能仅支持每个工作人员一个虚拟接口。 每个工作人员一个虚拟接口意味着每个应用服务计划的一个区域 VNet 集成。 同一应用服务计划中的所有应用都可以使用相同的 VNet 集成。 如果需要应用连接到其他虚拟网络，则需要创建另一个应用服务计划。 使用的虚拟接口不是客户可以直接访问的资源。

由于此技术运行方式的性质，与 VNet 集成一起使用的流量不会显示在 Azure 网络观察程序或 NSG 流日志中。

## <a name="gateway-required-vnet-integration"></a>网关所需的 VNet 集成

网关所需的 VNet 集成支持连接到另一个区域中的虚拟网络或经典虚拟网络。 网关所需的 VNet 集成：

* 使应用一次只能连接到一个虚拟网络。
* 使最多五个虚拟网络能够在应用服务计划中集成。
* 允许应用服务计划中的多个应用使用同一虚拟网络，而不影响应用服务计划可以使用的总数。 如果同一应用服务计划中有六个应用使用相同的虚拟网络，则这计为正在使用的一个虚拟网络。
* 由于网关上的 SLA，支持 99.9% 的 SLA。
* 使应用能够使用虚拟网络配置的 DNS。
* 需要使用 SSTP 点对点 VPN 配置的基于虚拟网络路由的网关才能连接到应用。

不能使用网关所需的 VNet 集成：

* 使用 Linux 应用程序。
* 使用 Azure ExpressRoute 连接虚拟网络。
* 访问服务终结点安全资源。
* 具有同时支持 ExpressRoute 和点对点或站点到站点 VPN 的共存网关。

### <a name="set-up-a-gateway-in-your-virtual-network"></a>在虚拟网络中设置网关 ###

若要创建网关，请执行以下操作：

1. [在虚拟网络中创建网关子网][creategatewaysubnet]。  

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点对点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 点对点地址空间必须位于 RFC 1918 地址块 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16 中。

如果创建网关以用于应用服务 VNet 集成，则无需上载证书。 创建网关可能需要 30 分钟。 在预配网关之前，您将无法将应用与虚拟网络集成。

### <a name="how-gateway-required-vnet-integration-works"></a>网关要求的 VNet 集成的工作原理

网关所需的 VNet 集成构建在点对点 VPN 技术之上。 点对点 VPN 限制对承载应用的虚拟机的网络访问。 应用仅限于通过混合连接或通过 VNet 集成将流量发送到互联网。 当应用配置了门户以使用网关所需的 VNet 集成时，将代表您管理复杂的协商，以在网关和应用程序端创建和分配证书。 结果是，用于托管应用的工作人员能够直接连接到所选虚拟网络中的虚拟网络网关。

![网关要求的 VNet 集成的工作原理][6]

### <a name="access-on-premises-resources"></a>访问本地资源

应用可以通过与具有站点到站点连接的虚拟网络集成来访问本地资源。 如果使用网关所需的 VNet 集成，请使用指向站点的地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 有关如何执行此操作的详细信息因网关而异，此处未对此进行说明。 不能使用站点到站点 VPN 连接配置 BGP。

区域 VNet 集成功能无需额外配置即可通过虚拟网络到达本地资源。 您只需使用 ExpressRoute 或站点到站点 VPN 将虚拟网络连接到本地资源即可。

> [!NOTE]
> 网关所需的 VNet 集成功能不会将应用与具有 ExpressRoute 网关的虚拟网络集成。 即使 ExpressRoute 网关配置为[共存模式][VPNERCoex]，VNet 集成也不起作用。 如果需要通过 ExpressRoute 连接访问资源，请使用在虚拟网络中运行的区域 VNet 集成功能或[应用服务环境][ASE]。
> 
> 

### <a name="peering"></a>对等互连

如果对等互连区域 VNet 集成，则无需执行任何其他配置。

如果使用网关所需的 VNet 集成进行对等互连，则需要配置一些其他项目。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用连接到的虚拟网络上添加对等互连连接。 添加对等互连连接时，启用**允许虚拟网络访问**，然后选择 **"允许转发流量**"和"**允许网关传输**"。
1. 在虚拟网络上添加对等互连连接，该连接将与您连接到的虚拟网络相对等。 在目标虚拟网络上添加对等互连连接时，启用 **"允许虚拟网络访问**"，然后选择 **"允许转发流量**"和"**允许远程网关**"。
1. 转到门户中的**应用服务计划** > **网络** > **VNet 集成**UI。 选择应用连接到的虚拟网络。 在路由部分下，添加与应用连接到的虚拟网络对等的虚拟网络的地址范围。

## <a name="manage-vnet-integration"></a>管理 VNet 集成

与虚拟网络连接和断开连接处于应用级别。 可能影响跨多个应用的 VNet 集成的操作处于应用服务计划级别。 通过应用>**网络** > **VNet 集成**门户，您可以获取有关虚拟网络的详细信息。 您可以在**应用服务计划** > **网络** > **VNet 集成**门户的应用服务计划级别看到类似的信息。

在 VNet 集成实例的应用视图中，唯一可以采用的操作是断开应用与当前连接到的虚拟网络的连接。 要断开应用与虚拟网络的连接，请选择 **"断开连接**"。 与虚拟网络断开连接时，你的应用将重新启动。 断开连接不会更改虚拟网络。 子网或网关未被删除。 如果随后要删除虚拟网络，请先断开应用与虚拟网络的连接，然后删除其中的资源，如网关。

应用服务计划 VNet 集成 UI 显示应用服务计划中应用使用的所有虚拟网络集成。 要查看每个虚拟网络的详细信息，请选择您感兴趣的虚拟网络。 您可以在此处执行两个操作，以便进行网关所需的 VNet 集成：

* **同步网络**：同步网络操作仅用于网关相关的 VNet 集成功能。 执行同步网络操作可确保证书和网络信息同步。如果添加或更改虚拟网络的 DNS，请执行同步网络操作。 此操作将重新启动使用此虚拟网络的任何应用。
* **添加路由**：添加路由会驱动出站流量到虚拟网络。

### <a name="gateway-required-vnet-integration-routing"></a>网关所需的 VNet 集成路由
虚拟网络中定义的路由用于从应用将流量定向到虚拟网络。 要向虚拟网络发送其他出站流量，请在此处添加这些地址块。 此功能仅适用于网关所需的 VNet 集成。 当您使用网关所需的 VNet 集成时，路由表不会影响应用流量，就像它们使用区域 VNet 集成一那样。

### <a name="gateway-required-vnet-integration-certificates"></a>网关所需的 VNet 集成证书
启用网关所需的 VNet 集成后，需要交换证书以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。

如果证书或网络信息已更改，请选择 **"同步网络**"。 选择 **"同步网络**"时，会导致应用和虚拟网络之间的连接短暂中断。 虽然应用不会重启，但失去连接会导致站点功能失常。

## <a name="pricing-details"></a>定价详细信息
除了应用服务计划定价层费用之外，区域 VNet 集成功能无需支付额外费用。

三项费用与使用网关所需的 VNet 集成功能有关：

* **应用服务计划定价层费用**：你的应用必须处于标准、高级或高级 V2 应用服务计划中。 有关这些成本的详细信息，请参阅[应用服务定价][ASPricing]。
* **数据传输成本**：即使虚拟网络位于同一数据中心，数据出口也会收取费用。 这些费用在[数据传输定价详细信息][DataPricing]中描述。
* **VPN 网关成本**：点对点 VPN 需要虚拟网络网关的成本。 有关详细信息，请参阅[VPN 网关定价][VNETPricing]。

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

CLI 支持可用于区域 VNet 集成。 要访问以下命令[，请安装 Azure CLI][installCLI]。

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

对于网关所需的 VNet 集成，可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 有关即用即用脚本，请参阅将[Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


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
