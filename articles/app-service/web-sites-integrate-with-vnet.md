---
title: "将应用与 Azure 虚拟网络进行集成"
description: "演示如何将 Azure 应用服务中的应用连接到新的或现有的 Azure 虚拟网络"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: b755197af7e8791e01273bcc25f72c0d92ef6bc2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络进行集成
本文档介绍 Azure 应用服务虚拟网络集成功能，并说明如何在 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714) 中使用应用对其进行设置。 如果不熟悉 Azure 虚拟网络 (VNet)，则这里需要指出的是，该功能允许将多个 Azure 资源置于可以控制其访问权限但无法通过 Internet 路由的网络中。 然后，可以使用多种 VPN 技术将这些网络连接到本地网络。 若要了解有关 Azure 虚拟网络的详细信息，请先了解以下信息：[Azure 虚拟网络概述][VNETOverview]。 

Azure 应用服务有两种形式。 

1. 支持全部定价计划的多租户系统
2. 部署到 VNet 中的应用服务环境 (ASE) 高级功能。 

本文档介绍 VNet 集成，而非应用服务环境。 若想要了解有关 ASE 功能的详细信息，请先了解以下信息：[应用服务环境简介][ASEintro]。

VNet 集成功能允许 Web 应用访问虚拟网络中的资源，但不允许通过虚拟网络对 Web 应用进行专用访问。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 专用站点访问仅适用于部署了内部负载均衡器 (ILB) 的 ASE。 有关使用 ILB ASE 的详细信息，请先参阅此文章：[创建和使用 ILB ASE][ILBASE]。 

使用 VNet 集成的常见情景是，需要通过 Web 应用来访问在 Azure 虚拟网络的虚拟机中运行的数据库或 Web 服务。 使用 VNet 集成时，不需要公开 VM 中应用程序的公共终结点，可以改用无法通过 Internet 路由的专用地址。 

VNet 集成功能：

* 需要“标准”、“高级”或“隔离”定价计划 
* 适用于经典 VNet 或资源管理器 VNet 
* 支持 TCP 和 UDP
* 适用于 Web 应用、移动应用、API 应用和函数应用
* 允许应用一次只连接到 1 个 VNet
* 允许在应用服务计划中一次最多集成 5 个 VNet 
* 允许在应用服务计划中由多个应用使用同一个 VNet
* 由于 VNet 网关上的 SLA，因此可实现 99.9% 的 SLA

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios
* 专用站点访问

### <a name="getting-started"></a>入门
将 Web 应用连接到虚拟网络之前，需要牢记以下几点：

* VNet 集成仅适用于“标准”、“高级”或“隔离”定价计划中的应用。 如果在启用此功能后又将应用服务计划缩放为不受支持的定价计划，应用会失去与所用 VNet 的连接。 
* 如果目标虚拟网络已经存在，必须在连接到应用之前借助动态路由网关使网络处于点到站点 VPN 启用状态。 如果网关由静态路由配置，则无法启用点到站点虚拟专用网络 (VPN)。
* VNet 所在的订阅必须与应用服务计划 (ASP) 所在的订阅相同。 
* 与 VNet 集成的应用使用为该 VNet 指定的 DNS。
* 默认情况下，集成应用只根据 VNet 中的已定义路由将流量路由到 VNet 中。 

## <a name="enabling-vnet-integration"></a>启用 VNet 集成

可以选择将应用连接到新的或现有的虚拟网络。 如果在集成过程中创建新网络，则除了创建 VNet，系统还会预先配置动态路由网关并启用点到站点 VPN。 

> [!NOTE]
> 配置新的虚拟网络集成可能需要几分钟的时间。 
> 
> 

要启用 VNet 集成，请先打开应用的“设置”，然后选择“网络”。 打开的 UI 提供三种网络选择。 本指南仅介绍如何通过混合连接进行 VNet 集成，对应用服务环境的讨论会在本文档后面部分进行。 

如果应用的定价计划不正确，可以通过该 UI 将计划扩展为所选的更高级别的定价计划。

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>允许与预先存在的 VNet 进行 VNet 集成
可以通过 VNet 集成 UI 从 VNet 列表中进行选择。 经典 VNet 会在 VNet 名称旁边的括号中加入“经典”一词进行表示。 对列表排序时，会首先列出资源管理器 VNet。 在下图中，可以看到，只能选择一个 VNet。 多种原因会导致 VNet 灰显，其中包括：

* 该 VNet 属于帐户有权访问的其他订阅
* 该 VNet 未启用点到站点连接
* 该 VNet 没有动态路由网关

![][2]

若要启用集成，请直接单击要集成的 VNet。 选择 VNet 以后，应用会自动重新启动，以使所做的更改生效。 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>在经典 VNet 中启用点到站点连接
如果 VNet 既没有网关，也没有点到站点连接，则需先进行此方面的设置。 若要对经典 VNet 进行此操作，请转到 [Azure 门户][AzurePortal]，此时会显示虚拟网络（经典）列表。 在此处单击要集成的网络，并单击 Essentials 下名为“VPN 连接”的大框。 在此处，可以创建点到站点 VPN，甚至可以让其创建网关。 如果有网关创建经验，则在完成点到站点连接以后，还需大约 30 分钟的时间才能让其就绪。 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>在资源管理器 VNet 中启用点到站点连接
若要使用网关和点到站点配置资源管理器 VNet，可以使用此处（[使用 PowerShell 配置与虚拟网络的点到站点连接][V2VNETP2S]）所述的 PowerShell，或使用此处（[使用 Azure 门户配置与 VNet 的点到站点连接][V2VNETPortal]）所述的 Azure 门户。 尚未提供执行此功能的 UI。 请注意，不需要为点到站点配置创建证书。 将 WebApp 连接到 VNet 时会自动配置证书。 

### <a name="creating-a-pre-configured-vnet"></a>创建预先配置的 VNet
若要创建配置了网关和点到站点连接的新 VNet，则可使用应用服务网络 UI 来执行该操作，但仅限于资源管理器 VNet。 若要创建配置了网关和点到站点连接的经典 VNet，则需通过“网络”用户界面手动执行该操作。 

要通过 VNet 集成 UI 创建资源管理器 VNet，请直接选择“创建新的虚拟网络”，并提供以下信息：

* 虚拟网络名称
* 虚拟网络地址块
* 子网名称
* 子网地址块
* 网关地址块
* 点到站点地址块

如果希望此 VNet 连接到任何其他网络，则应避免选取与那些网络重叠的 IP 地址空间。 

> [!NOTE]
> 创建带网关的资源管理器 VNet 大约需要 30 分钟，目前不会将 VNet 与应用集成。 创建带网关的 VNet 以后，需要回到应用 VNet 集成 UI 并选择新的 VNet。
> 
> 

![][3]

Azure VNet 通常在专用网络地址中创建。 默认情况下，VNet 集成功能会将任何流向那些 IP 地址范围的流量路由到 VNet 中。 专用 IP 地址范围如下：

* 10.0.0.0/8 - 相当于 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - 相当于 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - 相当于 192.168.0.0 - 192.168.255.255

VNet 地址空间需使用 CIDR 表示法来指定。 如果不熟悉 CIDR 表示法，则这里需要指出的是，该表示法是一种使用 IP 地址和整数（代表网络掩码）来指定地址块的方法。 举例来说，10.1.0.0/24 表示 256 个地址，10.1.0.0/25 表示 128 个地址。 IPv4 地址后接 /32 表示 1 个地址。 

如果在此处设置了 DNS 服务器信息，则会对 VNet 进行相应的设置。 创建 VNet 以后，可以根据 VNet 用户体验编辑此信息。 如果更改 VNet 的 DNS，则需执行“同步网络”操作。

使用 VNet 集成 UI 创建经典 VNet 时，该 UI 会在与应用相同的资源组中创建 VNet。 

## <a name="how-the-system-works"></a>系统的工作方式
实际上，该功能基于点到站点 VPN 技术将应用连接到 VNet。 Azure 应用服务中的应用具有多租户系统体系结构，与虚拟机中一样，它可以阻止直接在 VNet 中预配应用。 通过依托点到站点技术，我们将网络访问限制在托管应用的那台虚拟机。 在那些应用主机上会进一步限制对网络的访问，因此应用只能访问配置好的允许访问的网络。 

![][4]

如果尚未使用虚拟网络配置 DNS 服务器，则应用需要使用 IP 地址访问 VNet 中的资源。 使用 IP 地址时，请记住，此功能的主要好处是允许在专用网络中使用专用地址。 如果将应用设置为对其中一个 VM 使用公共 IP 地址，则你使用的不是 VNet 集成功能，而是通过 Internet 进行通信。

## <a name="managing-the-vnet-integrations"></a>管理 VNet 集成
连接到 VNet 以及断开其连接的功能在应用级别执行。 可能影响跨多个应用的 VNet 集成的操作在 ASP 级别执行。 可以通过应用级别显示的 UI 获取 VNet 的详细信息。 这些相同信息的大部分也会显示在 ASP 级别。 

![][5]

在“网络功能状态”页中，可查看应用是否已连接到 VNet。 如果 VNet 网关因某种原因而关闭，则会显示为未连接。 

现在应用级别的 VNet 集成 UI 中获得的信息与你通过 ASP 获得的详细信息是相同的。 下面是信息中的各项内容：

* VNet 名称 - 此链接打开 Azure 虚拟网络 UI
* 位置 - 此项反映 VNet 的位置。 可以在其他位置与 VNet 集成。
* 证书状态 - 可以使用证书来确保应用和 VNet 之间的 VPN 连接的安全性。 此项所反映的测试用于确保证书处于同步状态。
* 网关状态 - 如果网关因某种原因而关闭，则应用无法访问 VNet 中的资源。 
* VNet 地址空间 - 此项为 VNet 的 IP 地址空间。 
* 点到站点地址空间 - 此项为 VNet 的点到站点 IP 地址空间。 应用会在此地址空间中将通信显示成来自其中一个 IP。 
* 站点到站点地址空间 - 可以使用站点到站点 VPN 将 VNet 连接到本地资源或其他 VNet。 配置完成以后，针对该 VPN 连接定义的 IP 范围会显示在此处。
* DNS 服务器 - 如果已为 DNS 服务器配置了 VNet，则这些服务器会列在此处。
* 路由到 VNet 的 IP - 此项为通过 VNet 定义了路由的 IP 地址的列表，这些地址显示于此。 

在 VNet 集成的应用视图中，能够执行的唯一操作是断开应用与当前所连接到的 VNet 的连接。 为此，请直接单击顶部的“断开连接”。 此操作不会更改 VNet。 VNet 及其包括网关在内的配置都保持不变。 如果随后想要删除 VNet，则需先删除其中的资源（包括网关）。 

“应用服务计划”视图包含许多其他操作。 它还可以通过其他方式进行访问，通过应用进行访问除外。 要访问 ASP 网络 UI，请直接打开 ASP UI，并向下滚动。 有一个名为“网络功能状态”的 UI 元素。 该元素会提供一些有关 VNet 集成的次要详细信息。 单击此 UI 会打开网络功能状态 UI。 如果随后单击“单击此处进行管理”，则会打开一个 UI，其中会列出此 ASP 中的 VNet 集成。

![][6]

查看要集成的 VNet 的位置时，ASP 的位置很好记忆。 当 VNet 位于其他位置时，出现延迟问题的可能性要大得多。 

通过集成的 VNet 数，可以了解到此 ASP 中与应用集成的 VNet 数，以及到底可以集成多少。 

要查看每个 VNet 的更多详细信息，请直接单击你感兴趣的 VNet。 除了前述详细信息，还会看到此 ASP 中正在使用该 VNet 的应用的列表。 

关于操作，一共有两个主要操作。 第一个操作是允许添加驱使流量从应用进入 VNet 的路由。 第二个操作是允许同步证书和网络信息。

![][7]

**路由** 如前所述，在 VNet 中定义的路由用于将流量从应用导入 VNet。 不过还有一些用处。当客户需要将额外的出站流量从应用发送到 VNet 中时，就需要用到此功能。 此后对流量的控制取决于客户对其 VNet 的配置。 

**证书** 证书状态反映了应用服务所执行的检查的结果，该检查是为了验证用于 VPN 连接的证书是否仍然有效。 启用 VNet 集成后，如果这是第一次从该 ASP 中的应用集成到该 VNet，则必须进行证书交换以确保连接的安全性。 除了证书，我们还可以获得 DNS 配置、路由以及其他类似的用于描述网络的内容。
如果更改了这些证书或网络信息，则需单击“同步网络”。 注意：单击“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重新启动，但失去连接会导致站点功能失常。 

## <a name="accessing-on-premises-resources"></a>访问本地资源
VNet 集成功能的一大好处是，如果 VNet 通过站点到站点 VPN 连接到本地网络，则可通过应用访问本地资源。 不过，可能需要使用点到站点 IP 范围的路由来更新本地 VPN 网关才能这样做。 先设置站点到站点 VPN 以后，接着应通过用于配置该 VPN 的脚本来设置包括点到站点 VPN 在内的路由。 如果在创建站点到站点 VPN 后才添加点到站点 VPN，则需手动更新路由。 具体操作信息取决于每个网关，在此不作说明。 

> [!NOTE]
> VNet 集成功能不会将应用与包含 ExpressRoute 网关的 VNet 集成。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会实现。 若要通过 ExpressRoute 连接访问资源，可以使用 VNet 中运行的[应用服务环境][ASE]。
> 
> 

## <a name="pricing-details"></a>定价详细信息
使用 VNet 集成功能时，应了解某些定价方面的细节差异。 使用此功能时，涉及到 3 种相关费用：

* ASP 定价层要求
* 数据传输费用
* VPN 网关费用。

应用必须是标准或高级应用服务计划中的应用才能使用此功能。 可在此处了解此方面费用的更多详细信息：[应用服务定价][ASPricing]。 

通过 VNet 集成连接进行传输的出站数据始终收费，即使该 VNet 是在同一数据中心。这是由系统对点到站点 VPN 的处理方式决定的。 若要了解具体的收费情况，请参阅：[数据传输定价详细信息][DataPricing]。 

最后一项是 VNet 网关费用。 即使不需使用网关来执行站点到站点 VPN 之类的其他功能，仍需为网关付费，否则无法使用 VNet 集成功能。 下面是此方面费用的详细信息：[VPN 网关定价][VNETPricing]。 

## <a name="troubleshooting"></a>故障排除
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以通过两种方式来体验控制台的使用。 一种方式是使用 Kudu 控制台，另一种方式是访问 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 这相当于访问 [sitename].scm.azurewebsites.net。 打开后，直接转到“调试控制台”选项卡。若要访问 Azure 门户托管的控制台，请在应用中转到“工具”->“控制台”。 

#### <a name="tools"></a>工具
由于存在安全约束，ping、nslookup 和 tracert 工具无法通过控制台来使用。 为了填补这方面的空白，我们添加了两项单独的工具。 我们添加了名为 nameresolver.exe 的工具，用于测试 DNS 功能。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 nameresolver 来检查应用所需的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 tcpping.exe，语法为：

    tcpping.exe hostname [optional: port]

tcpping 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙** 若存在防火墙，则会发生 TCP 超时。 本例中为 21 秒。 使用 tcpping 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。
* **无效的 P2S IP 范围** 点到站点 IP 范围点需要处于 RFC 1918 专用 IP 范围 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) 内。 如果范围的 IP 超出上述范围，则操作无法正常运行。 

如果这些情况不能解决问题，请首先查看以下简单因素： 

* 网关在门户中是否显示为已启动？
* 证书是否显示为处于同步状态？
* 是否有人更改了网络配置却没有在受影响的 ASP 中执行“同步网络”操作？ 

如果网关处于关闭状态，则将其重新启动。 如果证书不同步，则请转到 VNet 集成的 ASP 视图，并单击“同步网络”。 如果怀疑有人对 VNet 配置进行了更改且未使用 ASP 进行同步，则请转到 VNet 集成的 ASP 视图，并单击“同步网络”。需注意的是，这会导致 VNet 连接和应用出现短暂中断。 

如果这些都正常，则需进行更深入的故障诊断：

* 是否存在其他应用在使用 VNet 集成访问同一 VNet 中的资源？ 
* 是否能够转到应用控制台并使用 tcpping 来访问 VNet 中的任何其他资源？ 

如果上面这两个问题中有一个的回答为“是”，则说明 VNet 集成功能正常，问题出在其他地方。 这种情况下，解决问题会更加困难，因为并没有简单的方法可以查看你为何无法访问主机:端口。 部分原因包括：

* 在主机上开启了防火墙，导致无法从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭
* 应用程序已关闭
* IP 或主机名错误
* 应用程序所侦听的端口不同于所期望的端口。 要查看是否属于这种情况，可以转到该主机，并在命令提示符处执行“netstat -aon”。 此时会显示在相应端口上进行侦听的进程 ID。 
* 网络安全组的配置方式导致无法从点到站点 IP 范围访问应用程序主机和端口

请记住，不知道应用会使用点到站点 IP 范围中的哪个 IP，因此需允许整个范围中的 IP 进行访问。 

其他调试步骤包括：

* 登录到 VNet 中的其他 VM，尝试在该处访问资源主机:端口。 可以使用某些 TCP ping 实用程序来进行此类操作，甚至还可以视需要使用 telnet。 此处的目的就是确定是否可以从这个其他的 VM 进行连接。 
* 在其他 VM 中启动应用程序，测试能否在应用的控制台中访问该主机和端口

#### <a name="on-premises-resources"></a>本地资源 ####
如果应用无法访问本地资源，则首先应检查是否能够访问 VNet 中的资源。 如果可以，请尝试从 VNet 中的 VM 访问本地应用程序。 可以使用 telnet 或 TCP ping 实用程序。 如果 VM 无法访问本地资源，请确保站点到站点 VPN 连接正常。 如果正常，则请检查前述项目以及本地网关配置和状态。 

现在，如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 在本地网关中未使用点到站点 IP 范围对路由进行配置
* 网络安全组阻止点到站点 IP 范围中的 IP 进行访问
* 本地防火墙阻止来自点到站点 IP 范围的流量
* VNet 中的用户定义路由 (UDR) 阻止点到站点型流量访问本地网络

## <a name="powershell-automation"></a>PowerShell 自动化

可以使用 PowerShell 将应用服务与 Azure 虚拟网络集成。 对于准备好运行的脚本，请参阅[将 Azure 应用服务中的应用连接到 Azure 虚拟网络](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。

## <a name="hybrid-connections-and-app-service-environments"></a>混合连接和应用服务环境
可以通过 3 大功能来访问 VNet 托管的资源。 它们是：

* VNet 集成
* 混合连接
* 应用服务环境

混合连接要求在网络中安装名为混合连接管理器 (HCM) 的中继代理。 HCM 需要能够连接到 Azure 以及应用程序。 此解决方案尤其适合远程网络（例如本地网络），甚至适用于其他云托管的网络，因为它不需要可通过 Internet 进行访问的终结点。 HCM 只能在 Windows 上运行，最多可以运行 5 个实例以确保为用户提供高可用性。 不过，混合连接只支持 TCP，而且每个 HC 终结点必须与特定的主机:端口组合匹配。 

应用服务环境功能允许在 VNet 中运行 Azure App Service 的实例。 这样可以让应用访问 VNet 中的资源而不需执行额外的步骤。 应用服务环境的其他好处包括：可以在 RAM 最大为 14 GB 的情况下使用基于 Dv2 的辅助角色。 另一好处是，可以根据需要进行系统伸缩。 不同于多租户环境中 ASP 限制为 20 个实例，在 ASE 中可扩展至 100 个 ASP 实例。 ASE 能够提供而 VNet 集成无法提供的其中一项功能就是，应用服务适用于 ExpressRoute VPN。 

尽管使用起来存在功能重叠的情况，但这些功能却无法彼此替代。 需要知道使用哪种功能取决于具体需要。 例如：

* 如果是一名开发人员，只想在 Azure 中运行一个站点并让其访问你办公桌下工作站上的数据库，则可使用混合连接，那样最轻松。 
* 如果是一家大型组织，需要在公有云中存储大量 Web 属性并在自己的网络中对其进行管理，则最好是使用应用服务环境。 
* 如果你有大量应用服务托管的应用，但只想访问 VNet 中的资源，则不妨采用 VNet 集成。 

除了这些用例，还需考虑到一些简便性相关方面。 如果 VNet 已连接到本地网络，则可通过 VNet 集成或应用服务环境轻松使用本地资源。 另一方面，如果 VNet 尚未连接到本地网络，则与安装 HCM 相比，通过 VNet 设置站点到站点 VPN 需要多得多的开销。 

除了功能差异，还有定价差异。 应用服务环境功能是我们推出的高级服务，不仅提供最多的网络配置可能性，还提供其他重要功能。 VNet 集成适用于标准或高级 ASP，尤其适合在 VNet 中通过多租户应用服务安全地使用资源。 混合连接目前依托于 BizTalk 帐户，该帐户的定价级别最低为免费，价格随所需使用量的增加逐渐增加。 但是，在需要使用多个网络时，混合连接是再适合不过的了，可以通过混合连接访问远不止 100 个的独立网络中的资源。 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
