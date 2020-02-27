---
title: 将应用与 Azure 虚拟网络集成
description: 将 Azure App Service 中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648976"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure App Service 虚拟网络集成功能，以及如何在[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中对应用进行设置。 使用[Azure 虚拟网络][VNETOverview]（vnet）可以将多个 Azure 资源置于非 internet 可路由网络中。  

Azure App Service 有两种变体。 

1. 支持除独立定价计划以外的全部定价计划的多租户系统
2. 应用服务环境（ASE），它部署到 VNet 中并支持隔离的定价计划应用

本文档介绍 VNet 集成功能，该功能在多租户应用服务中使用。 如果你的应用处于[应用服务环境][ASEintro]中，则它已在 vnet 中，无需使用 VNet 集成功能来访问同一 VNet 中的资源。 有关所有应用服务网络功能的详细信息，请参阅[应用服务网络功能](networking-features.md)

VNet 集成允许 web 应用访问虚拟网络中的资源，但不会从 VNet 向你的 web 应用授予入站私有访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 与同一区域中的 Vnet 和其他区域中的 Vnet 一起使用时，VNet 集成功能的行为方式有所不同。 VNet 集成功能具有两种变体。

1. 区域 VNet 集成-连接到同一区域中资源管理器 Vnet 时，必须在要与之集成的 VNet 中具有专用子网。 
2. 网关所需的 VNet 集成-连接到其他区域中的 Vnet 或同一区域中的经典 VNet 时，需要在目标 VNet 中预配虚拟网络网关。

VNet 集成功能：

* 需要标准、高级、PremiumV2 或弹性高级定价计划 
* 支持 TCP 和 UDP
* 使用应用服务应用和函数应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

网关所需的 VNet 集成仅提供对目标 VNet 中的资源的访问权限，或通过对等互连或 Vpn 连接到目标 VNet 的网络中的资源。 网关所需的 VNet 集成不允许访问通过 ExpressRoute 连接提供的资源，也不能与服务终结点一起使用。 

无论使用何种版本，VNet 集成都允许 web 应用访问虚拟网络中的资源，但不会向虚拟网络授予对 web 应用程序的入站私有访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 

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

通过使用区域 VNet 集成，你的应用程序可以访问：

* VNet 中与你集成的同一区域中的资源 
* Vnet 对等互连中的资源位于同一区域中的 VNet
* 服务终结点保护服务
* 跨 ExpressRoute 连接的资源
* 你连接到的 VNet 中的资源
* 跨对等互连连接（包括 ExpressRoute 连接）的资源
* 专用终结点 

在同一区域中使用 VNet 与 Vnet 的集成时，可以使用以下 Azure 网络功能：

* 网络安全组（Nsg）-可以使用放置在集成子网上的网络安全组来阻止出站流量。 由于无法使用 VNet 集成提供对 web 应用的入站访问权限，因此入站规则不适用。
* 路由表（Udr）-可以将路由表放置在集成子网上，以便发送出站流量。 

默认情况下，应用仅将 RFC1918 流量路由到 VNet。 如果要将所有出站流量路由到 VNet，请将应用设置 WEBSITE_VNET_ROUTE_ALL 应用到应用。 配置应用设置：

1. 在应用门户中转到配置 UI。 选择**新应用程序设置**
1. 在 "名称" 字段中键入 " **WEBSITE_VNET_ROUTE_ALL** ，在" 值 "字段中键入**1**

   ![提供应用程序设置][4]

1. 选择“确定”
1. 选择“保存”

如果将所有出站流量路由到 VNet，则会受到应用于集成子网的 Nsg 和 Udr 的限制。 当你将所有出站流量路由到 VNet 中时，你的出站地址仍将是应用程序属性中列出的出站地址，除非你提供将流量发送到其他位置的路由。 

在同一区域中使用 VNet 与 Vnet 的集成存在一些限制：

* 不能跨全局对等连接访问资源
* 此功能仅适用于支持 PremiumV2 应用服务计划的更新的应用服务缩放单位。
* 集成子网仅可由一个应用服务计划使用
* 此功能不能由处于应用服务环境中的独立计划应用使用
* 此功能需要一个不使用的子网，该子网为/27，其中包含32地址或更大的资源管理器 VNet
* 应用和 VNet 必须位于同一区域中
* 不能删除带有集成应用的 VNet。 请在删除 VNet 之前删除集成。 
* 只能与 web 应用相同的订阅中的 Vnet 集成
* 对于每个应用服务计划，只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用相同的 VNet。 
* 当存在使用区域 VNet 集成的应用时，不能更改应用或应用服务计划的订阅

为每个应用服务计划实例使用一个地址。 如果将应用扩展到五个实例，则使用5个地址。 由于在分配后无法更改子网大小，因此你必须使用足够大的子网来容纳你的应用程序可能会达到的任何规模。 建议大小为/26，其中包含64地址。 带有64地址的/26 将容纳包含30个实例的高级应用服务计划。 当你向上或向下缩放应用服务计划时，你需要在短时间内使用两个地址。 

如果希望其他应用服务计划中的应用连接到已由其他应用服务计划中的应用程序连接的 VNet，则需要选择与预先存在的 VNet 集成所使用的子网不同的子网。  

此功能在 Linux 中处于预览阶段。 此功能的 Linux 格式仅支持调用 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）。

### <a name="web-app-for-containers"></a>容器的 Web 应用

如果在 Linux 上使用带有内置映像的应用服务，则区域 VNet 集成无需进行其他更改即可工作。 如果使用用于容器的 Web 应用，则需要修改 docker 映像才能使用 VNet 集成。 在 docker 映像中，使用端口环境变量作为主 web 服务器的侦听端口，而不是使用硬编码的端口号。 在容器启动时，应用服务平台会自动设置端口环境变量。 如果你使用的是 SSH，则必须将 SSH 守护程序配置为侦听 SSH_PORT 环境变量在使用区域 VNet 集成时指定的端口号。  在 Linux 上，不支持网关必需的 VNet 集成。 

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成，可以使用服务终结点。  若要将服务终结点与应用程序一起使用，请使用区域 VNet 集成连接到选定的 VNet，然后在用于集成的子网中配置服务终结点。 

### <a name="network-security-groups"></a>网络安全组

网络安全组使你能够阻止到 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的 web 应用可以使用[网络安全组][VNETnsg]来阻止到 VNet 或 internet 中的资源的出站流量。 若要阻止流量发送到公共地址，则必须将应用程序设置 WEBSITE_VNET_ROUTE_ALL 设置为1。 NSG 中的入站规则不适用于应用，因为 VNet 集成仅影响应用的出站流量。 若要控制到 web 应用的入站流量，请使用访问限制功能。 适用于你的集成子网的 NSG 将生效，而与任何应用到集成子网的路由无关。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，并且你的集成子网上没有任何影响公共地址流量的路由，则所有的出站流量仍需服从 Nsg 分配给集成子网。 如果 WEBSITE_VNET_ROUTE_ALL 未设置，则 Nsg 将仅应用于 RFC1918 的流量。

### <a name="routes"></a>路由

使用路由表，你可以将应用程序的出站流量路由到所需的任何位置。 默认情况下，路由表只会影响 RFC1918 目标流量。  如果将 WEBSITE_VNET_ROUTE_ALL 设置为1，则将影响所有出站调用。 在集成子网上设置的路由不会影响对入站应用请求的答复。 常见目标可以包括防火墙设备或网关。 如果要将所有出站流量路由到本地，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果你确实要将流量路由到网关，请确保在外部网络中设置路由以发送任何回复。

边界网关协议（BGP）路由也会影响应用程序流量。 如果你有来自 ExpressRoute 网关之类的 BGP 路由，你的应用程序出站流量将受到影响。 默认情况下，BGP 路由只会影响 RFC1918 目标流量。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，则可能会受到 BGP 路由影响的所有出站流量。 

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
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 你还可以在 [sitename] appname>.azurewebsites.net 上访问 Kudo 控制台。 网站加载后，请切换到 "调试控制台" 选项卡。若要转到 Azure 门户托管的控制台，请转到 "工具"-"> 控制台"。 

#### <a name="tools"></a>工具
由于安全约束， **ping**、 **nslookup**和**tracert**工具无法通过控制台工作。 为了填充 void，添加了两个单独的工具。 为了测试 DNS 功能，我们添加了名为 nameresolver.exe 的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 **nameresolver** 来检查应用所依赖的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。 您可以通过查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER，查看应用程序在控制台中将使用的 DNS 服务器。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问。** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。 如果无法访问，则可能是防火墙或 NSG 阻止对 DNS 的访问，或者它可能已关闭。

如果这些项不能回答您的问题，请先查看以下内容： 

**区域 VNet 集成**
* 你的目标是非 RFC1918 的地址，并且你没有将 WEBSITE_VNET_ROUTE_ALL 设置为1
* 你的集成子网是否有 NSG 阻止出口
* 如果要跨越 ExpressRoute 或 VPN，是否配置了本地网关，以将流量备份到 Azure？ 如果可以访问 VNet 中的终结点，但不能访问本地终结点，请检查路由。
* 你是否有足够的权限在集成子网上设置委派？ 在区域 VNet 集成配置期间，集成子网将委托给 Microsoft。 VNet 集成 UI 会自动将子网委托给 Microsoft。 如果你的帐户没有足够的网络权限来设置委派，你将需要可设置你的集成子网中的属性的用户来委派子网。 若要手动委派集成子网，请参阅 Azure 虚拟网络子网 UI，并设置 Microsoft 的委派。 

**网关所需的 VNet 集成**
* RFC 1918 范围内的点到站点地址范围（10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255）？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示为同步或是否怀疑网络配置已更改？  如果你的证书不同步或者你怀疑已对 VNet 配置进行了不同步的更改，则会命中 "同步网络"。
* 如果通过 VPN，则配置了本地网关，以将流量路由到 Azure？ 如果可以访问 VNet 中的终结点，但不能访问本地终结点，请检查路由。
* 是否在尝试使用支持点到站点和 ExpressRoute 的共存网关？ VNet 集成不支持共存网关 

调试网络问题是一项挑战，因为看不到阻止访问特定主机的操作：端口组合。 部分原因包括：

* 在主机上开启了防火墙，导致无法从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭
* 应用程序已关闭
* IP 或主机名错误
* 应用程序所侦听的端口不同于所期望的端口。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。 
* 网络安全组的配置方式导致无法从点到站点 IP 范围访问应用程序主机和端口

请记住，您不知道您的应用程序将实际使用的地址。 它可以是集成子网或点到站点地址范围中的任何地址，因此需要允许从整个地址范围进行访问。 

其他调试步骤包括：

* 连接到 VNet 中的某个 VM，尝试在该处访问资源主机:端口。 若要针对 TCP 访问权限进行测试，请使用 PowerShell 命令 test-netconnection。 语法为：

      test-netconnection hostname [optional: -Port]

* 使用**tcpping**启动 VM 上的应用程序，并从应用程序中的控制台测试对该主机和端口的访问权限

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，则请检查是否能够通过 VNet 访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试。 如果 VM 无法访问本地资源，则 VPN 或 ExpressRoute 连接可能配置不正确。

如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 你的路由未配置为你的子网或指向本地网关中的站点地址范围
* 网络安全组阻止点到站点 IP 范围中的 IP 进行访问
* 本地防火墙阻止来自点到站点 IP 范围的流量
* 正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址


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
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
