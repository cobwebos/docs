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
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b269c75be7fec55fb77afecc6d04b86266c74a6f
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147304"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure 应用服务虚拟网络集成功能以及如何将它设置为中的应用[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)。 使用 [Azure 虚拟网络][VNETOverview] (VNet) 可将多个 Azure 资源置于无法通过 Internet 路由的网络中。  

Azure 应用服务有两种形式。 

1. 支持除独立定价计划以外的全部定价计划的多租户系统
2. 应用服务环境 (ASE)，也不能部署到 VNet 支持独立定价计划应用程序

本文档将完成两个 VNet 集成功能，即在多租户应用服务中使用。 如果应用在[应用服务环境][ASEintro]中，则该应用已处于 VNet 中且不需要使用 VNet 集成功能来获取同一 VNet 中的资源。 对所有应用服务网络功能的信息，请参阅[网络功能的应用服务](networking-features.md)

有两个窗体到 VNet 集成功能

1. 一种版本支持与在同一区域中的 Vnet 集成。 这种形式的功能要求在同一区域中的 VNet 的子网。 此功能仍处于预览阶段，但存在一些注意事项如下所示支持适用于 Windows 应用生产工作负荷。
2. 另一个版本可与其他区域中的 Vnet 或经典 Vnet 集成。 此版本的功能需要虚拟网络网关部署到 VNet。 这是点到站点基于 VPN 功能。

应用一次只能使用一种形式的 VNet 集成功能。 然后，问题是您应该使用哪种功能。 可以使用许多项。 不过是明确的与众不同之处：

| 问题  | 解决方案 | 
|----------|----------|
| 要在同一区域中达到 （10.0.0.0/8，172.16.0.0/12，192.168.0.0/16） 的 RFC 1918 地址 | 区域 VNet 集成 |
| 想要访问经典 VNet 或另一个区域中的 VNet | 网关所需的 VNet 集成 |
| 想要通过 ExpressRoute 访问 RFC 1918 终结点 | 区域 VNet 集成 |
| 想要在服务终结点之间访问资源 | 区域 VNet 集成 |

但两者无法以便可以通过 ExpressRoute 访问的非 RFC 1918 地址。 若要执行此操作，需要暂时使用 ASE。

使用区域的 VNet 集成不将 VNet 连接到内部部署或配置服务终结点。 这就是单独的网络配置。 区域的 VNet 集成只是让你的应用以使在这些连接类型的调用。

而不考虑所使用的版本，VNet 集成功能允许 web 应用程序访问的虚拟网络中的资源，但不允许入站专用访问你的 web 应用从虚拟网络。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于入 VNet 进行从您的应用程序的出站调用。 

VNet 集成功能：

* 需要“标准”、“高级”或“高级 V2”定价计划 
* 支持 TCP 和 UDP
* 适用于应用服务应用和 Function app

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

## <a name="regional-vnet-integration"></a>区域 VNet 集成 

当与您的应用程序所在的同一区域中的 Vnet 使用 VNet 集成时，它需要使用具有至少 32 个地址中的委派子网。 子网不能用于任何其他操作。 从您的应用程序进行的出站调用进行委派的子网中的地址。 当使用此版本的 VNet 集成时，调用都来自 VNet 中的地址。 在 VNet 中使用地址，你的应用：

* 对服务终结点保护的服务进行调用
* 通过 ExpressRoute 连接访问资源
* 访问已连接到 VNet 中的资源
* 在对等互连连接，包括 ExpressRoute 连接访问资源

此功能处于预览状态，但支持适用于 Windows 应用生产工作负荷有以下限制：

* 仅可以访问在 RFC 1918 范围内的地址。 这些都是 10.0.0.0/8、 172.16.0.0/12、 192.168.0.0/16 地址块中的地址。
* 不能跨全局对等互连连接访问资源
* 您不能设置从你的应用进入 VNet 的流量路由
* 该功能才可从更高版本支持高级 V2 应用服务计划的应用服务缩放单位。
* 应用服务环境中的独立的计划应用不能使用该功能
* 此功能要求使用资源管理器 VNet 中至少 32 个地址的未使用子网。
* 应用和 VNet 必须位于同一区域中
* 为每个应用服务计划实例使用一个地址。 由于子网大小在分配后不能更改，因此在使用子网时，其大小应该超出你的最大规模大小。 建议的大小是包含 32 个地址的 /27，因为它所容纳的应用服务计划可以扩大到 20 个实例。
* 不能删除带有集成应用的 VNet。 您必须首先删除集成 
* 您可以每个应用服务计划中只有一个区域的 VNet 集成。 在同一应用服务计划中的多个应用程序可以使用相同的 VNet。 

此功能处于预览也适用于 Linux。 若要使用的同一区域中的资源管理器 VNet 使用 VNet 集成功能：

1. 转至门户中的“网络 UI”。 如果您的应用程序能够使用新功能，您将看到添加 VNet （预览版） 的选项。  

   ![选择 VNet 集成][6]

1. 选择“添加 VNet (预览)”  。  

1. 选择希望与之集成的资源管理器 VNet，然后新建一个子网或从预先存在的空子网中选择一个。 集成只需不到一分钟即可完成。 在集成期间，应用会重启。  集成完成后，将能查看所集成的子网的详细信息，同时顶部会出现显示该功能处于预览状态的横幅。

   ![选择 VNet 和子网][7]

一旦你的应用与 VNet 集成，它将使用相同使用配置 VNet 的 DNS 服务器。 

若要从 VNet 断开应用，请选择“断开连接”  。 该操作将重启 Web 应用。 

通过新的 VNet 集成功能可以使用服务终结点。  若要将服务终结点和应用配合使用，请使用新的 VNet 集成连接至所选 VNet，然后在用于集成的子网上配置服务终结点。 

#### <a name="web-app-for-containers"></a>容器的 Web 应用

如果带内置映像，在 Linux 上使用应用服务，无需进行其他更改的工作区域的 VNet 集成功能。 如果容器中的使用 Web 应用，需要修改 docker 映像，以便使用 VNet 集成。 在 docker 映像，将端口环境变量用作主要 web 服务器的侦听端口，而不是使用硬编码端口号。 在容器启动时端口环境变量自动设置通过应用服务平台。

### <a name="how-vnet-integration-works"></a>VNet 集成的工作原理

辅助角色上托管应用服务中的应用。 托管计划专用的基本和更高的定价计划的同一个辅助角色上运行任何其他客户的工作负荷。 VNet 集成适用于通过装载虚拟接口中的委派的子网的地址。 因为从地址是在 VNet 中，它有权访问的大多数情况中或通过 VNet 就像在 VNet 中的 VM 会。 网络实现是不同于 VM 中运行你的 VNet，即为什么某些网络功能尚不可用时使用此功能。

![VNet 集成](media/web-sites-integrate-with-vnet/vnet-integration.png)

启用 VNet 集成后，您的应用程序仍将像平时一样的同一通道通过 internet 的出站调用。 应用属性门户中列出的出站地址仍是您的应用程序使用的地址。 调用服务终结点保护的服务或 RFC 1918 地址是什么更改为你的应用进入 VNet。 

该功能仅支持每个辅助角色的一个虚拟接口。  每个辅助角色的一个虚拟接口意味着每个应用服务计划的一个区域 VNet 集成。 所有相同的应用服务计划中的应用可以使用相同的 VNet 集成，但如果需要应用到其他 VNet 连接，您需要创建另一个应用服务计划。 使用的虚拟接口不是客户可以直接访问的资源。

由于此技术的运行方式的特性，可与 VNet 集成的流量不会不显示在网络观察程序或 NSG 流日志。  

## <a name="gateway-required-vnet-integration"></a>网关所需的 VNet 集成 

网关所需的 VNet 集成功能：

* 可用于将连接到任何区域中的 Vnet 是在资源管理器或经典 Vnet
* 允许应用一次连接到仅 1 个 VNet
* 允许最多五个 Vnet 集成的应用服务计划中 
* 允许在同一个 VNet，以供在应用服务计划中的多个应用，而不会影响可由应用服务计划的总数。  如果有 6 计为 1 个 VNet 所使用的相同应用服务计划中使用同一个 VNet 的应用。 
* 需要使用点到站点 VPN 配置的虚拟网络网关
* 不支持用于 Linux 应用
* 由于 SLA 99.9%的 SLA 支持在网关

此功能不支持：

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

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 地址空间必须是在 RFC 1918 地址块、 10.0.0.0/8、 172.16.0.0/12、 192.168.0.0/16

如果只是创建的网关应用 VNet 集成服务中使用，则不需要的证书上载。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。 

### <a name="configure-vnet-integration-with-your-app"></a>使用应用配置 VNet 集成 

要在应用上启用 VNet 集成，请执行以下步骤： 

1. 在 Azure 门户中转到该应用并打开应用设置，然后选择“网络”>“VNet 集成”。 ASP 必须在标准 SKU 或更好地使用这两个 VNet 集成功能。 
 ![VNet 集成 UI][1]

1. 选择“添加 VNet”  。 
 ![添加 VNet 集成][2]

1. 选择 VNet。 
  ![选择 VNet][8]
  
完成最后一步后，应用将会重启。  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>网关需要如何指定 VNet 集成功能的工作方式

网关需要 VNet 集成功能基于点到站点 VPN 技术。 点到站点技术将网络访问限制于托管应用的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 

![VNet 集成的工作原理][3]

## <a name="managing-vnet-integration"></a>管理 VNet 集成
连接到 VNet 以及断开其连接的功能在应用级别执行。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 从应用程序 > 网络 > VNet 集成门户中，您可以在 VNet 上获取详细信息。 您所见，ASP 在 ASP 级别的类似信息 > 网络 > 包括该应用服务计划中的哪些应用使用给定的集成的 VNet 集成门户。

 ![VNet 详细信息][4]

所掌握的信息是相同应用和 ASP 门户之间的 VNet 集成 UI 中。

* VNet 名称 - 链接至虚拟网络 UI
* 位置 - 反映 VNet 的位置。 与其他位置的 VNet 集成可能会导致应用出现延迟。 
* 证书状态 - 反映证书在应用服务计划和 VNet 之间的同步状态。
* 网关状态-应你使用网关所需的 VNet 集成，可以看到网关状态。
* VNet 地址空间 - 显示 VNet 的 IP 地址空间。 
* 点到站点地址空间 - 显示 VNet 的点到站点 IP 地址空间。 当使用网关所需的功能的同时进行到 VNet 的调用，你应用程序发件人地址将是这些地址之一。 
* 站点到站点地址空间 - 可以使用站点到站点 VPN 将 VNet 连接到本地资源或其他 VNet。 使用该 VPN 连接定义的 IP 范围如下所示。
* DNS 服务器 - 显示配置了 VNet 的 DNS 服务器。
* 路由到 VNet 的 IP 地址 - 显示路由的地址块，这些地址块用于驱动流量进入 VNet 

在 VNet 集成的应用视图中，能够执行的唯一操作是断开应用与当前所连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”  。 在从 VNet 断开连接时，应用将会重启。 断开连接操作不会更改 VNet。 子网关不会删除。 如果随后想要删除 VNet，需要先断开你的应用与 VNet 的连接并删除网关等资源。 

若要访问 ASP VNet 集成 UI，请打开 ASP UI 并选择“网络”  。  在 VNet 集成下，选择“单击此处可配置”以打开网络功能状态 UI  。

![ASP VNet 集成信息][5]

ASP VNet 集成 UI 会显示 ASP 中的应用使用的所有 VNet。 要查看每个 VNet 的详细信息，请单击感兴趣的 VNet。 此处有两种操作可以执行。

* **同步网络**。 同步网络操作仅适用于网关依赖于 VNet 集成功能。 执行同步网络操作可确保你的证书和网络信息保持同步。如果添加或更改 VNet 的 DNS，则需执行“同步网络”操作  。 此操作会重启所有使用此 VNet 的应用。
* **添加路由** 添加路由会驱动出站流量进入 VNet。

**路由** 在 VNet 中定义的路由，用于将流量从应用导入 VNet。 如果需要将其他出站流量发送到 VNet 中，则可以在此处添加地址块。 此功能只适用于网关需要 VNet 集成。

**证书**网关在需要时启用 VNet 集成，没有必须的交换证书以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。
如果更改了证书或网络信息，则需单击“同步网络”。 单击“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。 

## <a name="accessing-on-premises-resources"></a>访问本地资源
应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用的网关所需的 VNet 集成，需要使用点到站点地址块更新你的本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 具体操作信息取决于每个网关，在此不作说明。 不能通过站点到站点 VPN 连接配置 BGP。

没有其他配置所需的区域的 VNet 集成功能，来访问 VNet，并在本地。 只需将 VNet 连接到在本地使用 ExpressRoute 或站点到站点 VPN。 

> [!NOTE]
> 网关需要 VNet 集成功能不会将应用与包含 ExpressRoute 网关的 VNet。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会生效。 如果您需要访问的资源通过 ExpressRoute 连接，则可以使用区域的 VNet 集成功能或[应用服务环境][ASE]，在 VNet 中运行。 
> 
> 

## <a name="peering"></a>对等互连
如果您使用对等互连使用区域的 VNet 集成，您不需要执行任何其他配置。 

如果使用的网关所需的对等互连的 VNet 集成，需要配置几个其他项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许网关传输”    。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，启用“允许虚拟网络访问”并单击“允许转发流量”和“允许远程网关”    。
1. 转到门户中的“应用服务计划”>“网络”>“VNet 集成 UI”。  选择应用连接的 VNet。 在路由部分下，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。  


## <a name="pricing-details"></a>定价详细信息
区域的 VNet 集成功能已超出 ASP 定价层费用的使用收取任何额外费用。

有三种相关的费用对所需的网关的 VNet 集成功能的使用：

* ASP 定价层费用-您的应用程序需要在标准、 高级版或高级 V2 应用服务计划。 可在此处了解这些费用的更多详细信息：[应用服务定价][ASPricing]。 
* 数据传输成本-此处是针对数据流出量费用，即使该 VNet 是在同一数据中心。 [数据传输定价详细信息][DataPricing]中对这些费用进行了说明。 
* VPN 网关费用-此处是需要为点到站点 VPN 将 VNet 网关会产生费用。 [VPN 网关定价][VNETPricing]页上介绍了详细信息。


## <a name="troubleshooting"></a>故障排除
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 这相当于访问 [sitename].scm.azurewebsites.net。 打开后，转到“调试控制台”选项卡。若要访问 Azure 门户托管的控制台，请在应用中转到“工具”->“控制台”。 

#### <a name="tools"></a>工具
由于存在安全约束，**ping**、**nslookup** 和 **tracert** 工具无法通过控制台来使用。 为了填补这方面的空白，我们添加了两种单独的工具。 为了测试 DNS 功能，我们添加了名为 nameresolver.exe 的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 **nameresolver** 来检查应用所依赖的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。 可以看到您的应用程序将使用控制台中通过查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER 的 DNS 服务器。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问。** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。 如果无法访问，可以有防火墙或 NSG 阻止对 DNS 或它的访问可能已停机。

如果这些项不能解决您的问题，可查看第一个之类的内容： 

**区域 VNet 集成**
* 可为您的 RFC 1918 地址
* 有来自集成子网的 NSG 阻止出口
* 如果将跨 ExpressRoute 或 VPN，在本地网关配置为备份到 Azure 的流量路由？ 如果可以访问 VNet 但不是在本地中的终结点，这是很有必要检查。

**网关所需的 VNet 集成**
* 是在 RFC 1918 的范围内的点到站点地址范围 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示为正在进行的同步或执行您怀疑网络配置已更改？  如果证书不同步或怀疑已被与 Asp 不同步对 VNet 配置所做的更改，然后单击"同步网络"。
* 如果将跨 ExpressRoute 或 VPN，在本地网关配置为备份到 Azure 的流量路由？ 如果可以访问 VNet 但不是在本地中的终结点，这是很有必要检查。

调试网络问题是一项挑战，因为你不能看到什么阻止到特定的主机： 端口组合的访问。 部分原因包括：

* 在主机上开启了防火墙，导致无法从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭
* 应用程序已关闭
* IP 或主机名错误
* 应用程序所侦听的端口不同于所期望的端口。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。 
* 网络安全组的配置方式导致无法从点到站点 IP 范围访问应用程序主机和端口

请记住，您不知道您的应用程序实际上将使用哪个地址。 因此您需要允许从整个地址范围进行访问，则可能是集成点到站点或子网地址范围中的任何地址。 

其他调试步骤包括：

* 连接到 VNet 中的某个 VM，尝试在该处访问资源主机:端口。 若要针对 TCP 访问权限进行测试，请使用 PowerShell 命令 test-netconnection  。 语法为：

      test-netconnection hostname [optional: -Port]

* 显示与该主机上的虚拟机和测试访问的应用程序和端口的控制台中的应用使用**tcpping**

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，则请检查是否能够通过 VNet 访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试  。 如果 VM 无法访问你的本地资源，可能未正确配置 VPN 或 ExpressRoute 连接。

如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 路由未配置与子网或点到站点中的本地网关的地址范围
* 网络安全组阻止点到站点 IP 范围中的 IP 进行访问
* 本地防火墙阻止来自点到站点 IP 范围的流量
* 正在尝试通过联系使用区域的 VNet 集成功能的非 RFC 1918 地址


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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
