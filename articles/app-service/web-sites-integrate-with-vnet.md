---
title: 将应用与 Azure 虚拟网络集成 - Azure 应用服务
description: 演示如何将 Azure 应用服务中的应用连接到新的或现有的 Azure 虚拟网络
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a6d0cba41e694e154da32a878cb4c076aae13e65
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034725"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure 应用服务虚拟网络集成功能，并说明如何在 [Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)中使用应用对其进行设置。 使用 [Azure 虚拟网络][VNETOverview] (VNet) 可将多个 Azure 资源置于无法通过 Internet 路由的网络中。  

Azure App Service 有两种变体。 

1. 支持除独立定价计划以外的全部定价计划的多租户系统
2. 应用服务环境（ASE），它部署到 VNet 中并支持隔离的定价计划应用

本文档将介绍两个 VNet 集成功能，这些功能在多租户应用服务中使用。 如果你的应用处于[应用服务环境][ASEintro]中，则它已在 vnet 中，无需使用 VNet 集成功能来访问同一 VNet 中的资源。 有关所有应用服务网络功能的详细信息，请参阅[应用服务网络功能](networking-features.md)

VNet 集成功能提供了两种形式

1. 一个版本可在同一区域中实现与 Vnet 的集成。 此功能的这种形式需要在同一区域中的 VNet 中有一个子网。 此功能仍为预览版，但支持用于 Windows 应用生产工作负荷，下面是一些注意事项。
2. 另一种版本允许与其他区域中的 Vnet 或通过经典 Vnet 进行集成。 这种版本的功能需要将虚拟网关部署到 VNet 中。 这是基于点到站点 VPN 的功能，仅受 Windows 应用的支持。

一个应用一次只能使用一种形式的 VNet 集成功能。 那么，问题来了，你应该使用哪种功能？ 在许多情况下，可以使用任意一种。 但是，主要区别是：

| 问题  | 解决方案 | 
|----------|----------|
| 想要访问同一区域中的某个 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） | 区域 VNet 集成 |
| 想要访问经典 VNet 或另一区域的 VNet 中的资源 | 需要网关的 VNet 集成 |
| 想要跨 ExpressRoute 访问 RFC 1918 终结点 | 区域 VNet 集成 |
| 想要跨服务终结点访问资源 | 区域 VNet 集成 |

两种功能都不允许跨 ExpressRoute 访问非 RFC 1918 地址。 为此，你现在需要使用 ASE。

使用区域 VNet 集成不能将 VNet 连接到本地，也不能配置服务终结点。 这是单独的网络配置。 区域 VNet 集成只是允许应用跨这些连接类型进行调用。

无论使用何种版本，VNet 集成都允许 web 应用访问虚拟网络中的资源，但不会向虚拟网络授予对 web 应用程序的入站私有访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成只是为了从应用对 VNet 进行出站调用。 

VNet 集成功能：

* 需要“标准”、“高级”或“高级 V2”定价计划 
* 支持 TCP 和 UDP
* 使用应用服务应用和函数应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

## <a name="regional-vnet-integration"></a>区域 VNet 集成 

> [!NOTE]
> 对等互连对于基于 Linux 的应用服务尚不可用。
>

在应用的同一区域中与 Vnet 一起使用 VNet 集成时，它要求使用至少包含32地址的委托子网。 子网不能用于其他任何内容。 从你的应用程序发出的出站调用将从委托子网中的地址进行。 当你使用此版本的 VNet 集成时，将从 VNet 中的地址进行调用。 使用 VNet 中的地址，你的应用可以：

* 调用服务终结点保护服务
* 跨 ExpressRoute 连接访问资源
* 访问连接到的 VNet 中的资源
* 跨对等互连连接（包括 ExpressRoute 连接）访问资源

此功能处于预览阶段，但 Windows 应用生产工作负荷支持此功能，但具有以下限制：

* 只能访问 RFC 1918 范围内的地址。 它们是 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 地址块中的地址。
* 不能跨全局对等连接访问资源
* 不能将来自应用的流量的路由设置到 VNet 中
* 此功能仅适用于支持 PremiumV2 应用服务计划的更新的应用服务缩放单位。
* 集成子网仅可由一个应用服务计划使用
* 此功能不能由处于应用服务环境中的独立计划应用使用
* 此功能要求使用的子网为/27，其中包含32地址或更大的资源管理器 VNet 中
* 应用和 VNet 必须位于同一区域中
* 不能删除带有集成应用的 VNet。 必须先删除集成 
* 对于每个应用服务计划，只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用相同的 VNet。 
* 当存在使用区域 VNet 集成的应用时，不能更改应用或应用服务计划的订阅

为每个应用服务计划实例使用一个地址。 如果将应用扩展到5个实例，则使用5个地址。 由于在分配后无法更改子网大小，因此你必须使用足够大的子网来容纳你的应用程序可能会达到的任何规模。 建议大小为/26，其中包含64地址。 如果未更改应用服务计划的大小，则包含32地址的/27 将提供高级应用服务计划20个实例。 当你向上或向下缩放应用服务计划时，你需要在短时间内使用两个地址。 

如果希望其他应用服务计划中的应用连接到已由其他应用服务计划中的应用程序连接的 VNet，则需要选择与预先存在的 VNet 集成所使用的子网不同的子网。  

此功能也适用于 Linux。 若要在同一区域中将 VNet 集成功能与资源管理器 VNet 一起使用：

1. 转至门户中的“网络 UI”。 如果你的应用程序能够使用新功能，则会看到一个用于添加 VNet （预览版）的选项。  

   ![选择 VNet 集成][6]

1. 选择“添加 VNet (预览)”。  

1. 选择希望与之集成的资源管理器 VNet，然后新建一个子网或从预先存在的空子网中选择一个。 集成只需不到一分钟即可完成。 在集成期间，应用会重启。  集成完成后，将能查看所集成的子网的详细信息，同时顶部会出现显示该功能处于预览状态的横幅。

   ![选择 VNet 和子网][7]

当你的应用与 VNet 集成后，它将使用你的 VNet 配置的 DNS 服务器。 

区域 VNet 集成要求将集成子网委托给 Microsoft。  VNet 集成 UI 会自动将子网委托给 Microsoft。 如果你的帐户没有足够的网络权限来进行设置，则你将需要可设置集成子网上属性的人员来委派子网。 若要手动委派集成子网，请参阅 Azure 虚拟网络子网 UI，并设置 Microsoft 的委派。

若要从 VNet 断开应用，请选择“断开连接”。 该操作将重启 Web 应用。 


#### <a name="web-app-for-containers"></a>容器的 Web 应用

如果在 Linux 上使用带有内置映像的应用服务，则区域 VNet 集成功能无需进行其他更改即可工作。 如果使用用于容器的 Web 应用，则需要修改 docker 映像才能使用 VNet 集成。 在 docker 映像中，使用端口环境变量作为主 web 服务器的侦听端口，而不是使用硬编码的端口号。 在容器启动时，应用服务平台会自动设置端口环境变量。 如果你使用的是 SSH，则必须将 SSH 守护程序配置为侦听 SSH_PORT 环境变量在使用区域 VNet 集成时指定的端口号。

### <a name="service-endpoints"></a>服务终结点

通过新的 VNet 集成功能可以使用服务终结点。  若要将服务终结点和应用配合使用，请使用新的 VNet 集成连接至所选 VNet，然后在用于集成的子网上配置服务终结点。 


### <a name="how-vnet-integration-works"></a>VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用的托管计划，其中不存在其他客户工作负荷在同一辅助角色上运行。 VNet 集成的工作方式是使用委托子网中的地址装载虚拟接口。 由于 "发件人" 地址在 VNet 中，因此它可以访问或通过 VNet 中的大部分功能，就像 VNet 中的 VM 一样。 网络实现不同于在 VNet 中运行 VM，这就是在使用此功能时某些网络功能尚不可用的原因。

![VNet 集成](media/web-sites-integrate-with-vnet/vnet-integration.png)

启用 VNet 集成后，应用仍将通过与普通通道相同的通道对 internet 进行出站调用。 应用属性门户中列出的出站地址仍是应用使用的地址。 应用的更改是：对服务终结点保护的服务或 RFC 1918 地址的调用将进入 VNet。 

此功能仅支持每个辅助角色一个虚拟接口。  每个辅助角色一个虚拟接口意味着每个应用服务计划一个区域 VNet 集成。 同一应用服务计划中的所有应用都可使用相同的 VNet 集成，但如果需要一个应用来连接到其他 VNet，则需要创建另一个应用服务计划。 使用的虚拟接口不是客户可直接访问的资源。

由于此技术的工作原理，与 VNet 集成一起使用的流量不会在网络观察程序或 NSG 流日志中显示。  

## <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成 

需要网关的 VNet 集成功能：

* 可以用来连接到任何区域中的 VNet，不管它们是资源管理器 VNet 还是经典 VNet
* 允许应用一次只连接到 1 个 VNet
* 允许在应用服务计划中一次最多集成 5 个 VNet 
* 允许在应用服务计划中由多个应用使用同一个 VNet，不影响可供应用服务计划使用的总数。  如果有 6 个应用在使用同一应用服务计划中的同一 VNet，则计为 1 个 VNet 被使用。 
* 需要使用点到站点 VPN 配置的虚拟网关
* 由于网关上的 SLA，可实现 99.9% 的 SLA

此功能不支持以下操作：
* 用于 Linux 应用
* 跨 ExpressRoute 访问资源 
* 跨服务终结点访问资源 

### <a name="getting-started"></a>入门

将 Web 应用连接到虚拟网络之前，需要牢记以下几点：

* 在将目标虚拟网络连接到应用之前，必须借助基于路由的网关启用点到站点 VPN。 
* VNet 所在的订阅必须与应用服务计划 (ASP) 所在的订阅相同。
* 与 VNet 集成的应用使用为该 VNet 指定的 DNS。

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中设置网关 ###

如果已使用点到站点地址配置网关，则可以跳至“配置 VNet 与应用的集成”这一步。  
若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。  

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 地址空间必须在 RFC 1918 地址块 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 中

如果只是创建网关用于应用服务 VNet 集成，则不需要上传证书。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。 

### <a name="configure-vnet-integration-with-your-app"></a>使用应用配置 VNet 集成 

要在应用上启用 VNet 集成，请执行以下步骤： 

1. 在 Azure 门户中转到该应用并打开应用设置，然后选择“网络”>“VNet 集成”。 ASP 必须在标准 SKU 或更高的层级中才能使用上述任一 VNet 集成功能。 
 ![VNet 集成 UI][1]

1. 选择“添加 VNet”。 
 ![添加 VNet 集成][2]

1. 选择 VNet。 
  ![选择 VNet][8]
  
完成最后一步后，应用将会重启。  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>网关所需的 VNet 集成功能的工作原理

网关所需的 VNet 集成功能基于点到站点 VPN 技术。 点到站点技术将网络访问限制于托管应用的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 

![VNet 集成的工作原理][3]

## <a name="managing-vnet-integration"></a>管理 VNet 集成
连接到 VNet 以及断开其连接的功能在应用级别执行。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 可以通过“应用 > 网络 > VNet 集成门户”获取 VNet 的详细信息。 可以在“ASP > 网络 > VNet 集成门户”中查看 ASP 级别的类似信息，包括应用服务计划中的哪些应用在使用给定集成。

 ![VNet 详细信息][4]

应用和 ASP 门户的 VNet 集成 UI 中提供的信息相同。

* VNet 名称 - 链接至虚拟网络 UI
* 位置 - 反映 VNet 的位置。 与其他位置的 VNet 集成可能会导致应用出现延迟。 
* 证书状态 - 反映证书在应用服务计划和 VNet 之间的同步状态。
* 网关状态 - 如果使用网关所需的 VNet 集成，则可以看到网关状态。
* VNet 地址空间 - 显示 VNet 的 IP 地址空间。 
* 点到站点地址空间 - 显示 VNet 的点到站点 IP 地址空间。 如果在使用网关所需的功能时调用 VNet，应用的 FROM 地址将是这些地址之一。 
* 站点到站点地址空间 - 可以使用站点到站点 VPN 将 VNet 连接到本地资源或其他 VNet。 使用该 VPN 连接定义的 IP 范围如下所示。
* DNS 服务器 - 显示配置了 VNet 的 DNS 服务器。
* 路由到 VNet 的 IP 地址 - 显示路由的地址块，这些地址块用于驱动流量进入 VNet 

在 VNet 集成的应用视图中，能够执行的唯一操作是断开应用与当前所连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”。 在从 VNet 断开连接时，应用将会重启。 断开连接操作不会更改 VNet。 不会删除子网或网关。 若要删除 VNet，需要先从该 VNet 断开连接应用，然后删除该 VNet 中的资源，例如网关。 

若要访问 ASP VNet 集成 UI，请打开 ASP UI 并选择“网络”。  在 VNet 集成下，选择“单击此处可配置”以打开网络功能状态 UI。

![ASP VNet 集成信息][5]

ASP VNet 集成 UI 会显示 ASP 中的应用使用的所有 VNet。 要查看每个 VNet 的详细信息，请单击感兴趣的 VNet。 此处有两种操作可以执行。

* **同步网络**。 同步网络操作仅适用于网关相关的 VNet 集成功能。 执行同步网络操作可确保证书和网络信息保持同步。如果添加或更改 VNet 的 DNS，则需执行“同步网络”操作。 此操作会重启所有使用此 VNet 的应用。
* **添加路由** 添加路由会驱动出站流量进入 VNet。

**路由** 在 VNet 中定义的路由，用于将流量从应用导入 VNet。 如果需要将其他出站流量发送到 VNet 中，则可以在此处添加地址块。 此功能只适用于网关所需的 VNet 集成。

**证书**：启用网关所需的 VNet 集成后，必须进行证书交换以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。
如果更改了证书或网络信息，则需单击“同步网络”。 单击“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。 

## <a name="accessing-on-premises-resources"></a>访问本地资源
应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用网关所需的 VNet 集成，需要使用点到站点地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 具体操作信息取决于每个网关，在此不作说明。 不能使用站点到站点 VPN 连接配置 BGP。

区域 VNet 集成功能无需经过其他配置即可访问 VNet 和本地。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地。 

> [!NOTE]
> 需要网关的 VNet 集成功能不会将应用与包含 ExpressRoute 网关的 VNet 集成。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会生效。 如果需要通过 ExpressRoute 连接访问资源，可以使用区域 VNet 集成功能或在 VNet 中运行的[应用服务环境][ASE]。 
> 
> 

## <a name="peering"></a>对等互连
如果对区域 VNet 集成使用对等互连，则不需要进行任何附加的配置。 

如果结合对等互连使用网关所需的 VNet 集成，则需要配置几个附加的项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许网关传输”。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许远程网关”。
1. 转到门户中的“应用服务计划”>“网络”>“VNet 集成 UI”。  选择应用连接的 VNet。 在路由部分下，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。  


## <a name="pricing-details"></a>定价详细信息
除 ASP 定价层的费用外，区域 VNet 集成功能不会产生额外的使用费。

使用网关所需的 VNet 集成功能涉及到三种相关费用：

* ASP 定价层费用 - 应用需要属于“标准”、“高级”或“高级 V2”应用服务计划。 可在此处了解这些费用的更多详细信息：[应用服务定价][ASPricing]。 
* 数据传输费用 - 数据传出会产生费用，即使 VNet 在同一数据中心也是如此。 [数据传输定价详细信息][DataPricing]中对这些费用进行了说明。 
* VPN 网关费用 - 点到站点 VPN 所需的 VNet 网关会产生费用。 [VPN 网关定价][VNETPricing]页上介绍了详细信息。


## <a name="troubleshooting"></a>疑难解答
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 你还可以在 [sitename] appname>.azurewebsites.net 上访问 Kudo 控制台。 网站加载后，请切换到 "调试控制台" 选项卡。若要访问 Azure 门户托管的控制台，请在应用中转到“工具”->“控制台”。 

#### <a name="tools"></a>工具
由于存在安全约束，**ping**、**nslookup** 和 **tracert** 工具无法通过控制台来使用。 为了填补这方面的空白，我们添加了两种单独的工具。 为了测试 DNS 功能，我们添加了名为 nameresolver.exe 的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 **nameresolver** 来检查应用所依赖的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。 若要了解可供应用在控制台中使用的 DNS 服务器，请查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问。** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。 如果无法访问，则表明可能有防火墙或 NSG 在阻止对 DNS 的访问，或者该 DNS 可能已停机。

如果这些方法未解决问题，请首先检查以下因素： 

**区域 VNet 集成**
* 目标是否为 RFC 1918 地址
* 是否有 NSG 阻止了集成子网传出数据
* 如果要跨越 ExpressRoute 或 VPN，是否配置了本地网关，以将流量备份到 Azure？ 如果你可以访问 VNet 中的终结点但不能访问本地的终结点，则最好是检查这一点。

**网关所需的 VNet 集成**
* 点到站点地址范围是否在 RFC 1918 范围内 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示正在同步，或者，你是否怀疑网络配置已更改？  如果证书未同步，或者你怀疑 VNet 配置存在与 ASP 不同步的更改，请单击“同步网络”。
* 如果要跨越 ExpressRoute 或 VPN，是否配置了本地网关，以将流量备份到 Azure？ 如果你可以访问 VNet 中的终结点但不能访问本地的终结点，则最好是检查这一点。

调试网络问题很有难度，因为你看不到哪些因素正在阻止访问特定的“主机:端口”组合。 部分原因包括：

* 在主机上开启了防火墙，导致无法从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭
* 应用程序已关闭
* IP 或主机名错误
* 应用程序所侦听的端口不同于所期望的端口。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。 
* 网络安全组的配置方式导致无法从点到站点 IP 范围访问应用程序主机和端口

请记住，你并不知道应用实际使用的地址。 它可能是集成子网中或点到站点地址范围内的任意地址，因此你需要允许从整个地址范围进行访问。 

其他调试步骤包括：

* 连接到 VNet 中的某个 VM，尝试在该处访问资源主机:端口。 若要针对 TCP 访问权限进行测试，请使用 PowerShell 命令 test-netconnection。 语法为：

      test-netconnection hostname [optional: -Port]

* 在某个 VM 中启动应用程序，然后使用 **tcpping** 测试能否在应用的控制台中访问该主机和端口

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，则请检查是否能够通过 VNet 访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试。 如果 VM 无法访问本地资源，原因可能是未正确配置 VPN 或 ExpressRoute 连接。

如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 在本地网关中未使用子网或点到站点地址范围配置路由
* 网络安全组阻止点到站点 IP 范围中的 IP 进行访问
* 本地防火墙阻止来自点到站点 IP 范围的流量
* 正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址


## <a name="powershell-automation"></a>PowerShell 自动化

可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 对于准备好运行的脚本，请参阅[将 Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
