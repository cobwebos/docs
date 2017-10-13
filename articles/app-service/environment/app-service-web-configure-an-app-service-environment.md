---
title: "如何配置应用服务环境 v1"
description: "应用服务环境 v1 的配置、管理和监视"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-an-app-service-environment-v1"></a>配置应用服务环境 v1

> [!NOTE]
> 本文介绍应用服务环境 v1。  应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 若要深入了解新版本，请先参阅[应用服务环境简介](intro.md)。
> 

## <a name="overview"></a>概述
高级别 Azure 应用服务环境包括几个主要组件：

* 在应用服务环境托管服务中运行的计算资源
* 存储
* 数据库
* 经典 (V1) 或 Resource Manager(V2) Azure 虚拟网络 (VNet) 
* 内部运行应用服务环境托管服务的子网

### <a name="compute-resources"></a>计算资源
可以将计算资源用于四个资源池。  每个应用服务环境 (ASE) 都有一组前端和三个可能的工作节点池。 不需要三个辅助角色池都使用，如果需要，可以只使用其中一个或两个。

租户无法直接访问资源池（前端和辅助角色）中的主机。 无法使用远程桌面协议 (RDP) 来连接它们、更改其预配，或充当其管理员。

但是可以设置资源池的数量和大小。 在 ASE 中，有四个大小选项，标记为 P1 到 P4。 有关这些大小及其定价的详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。
更改数量或大小的操作称为缩放操作。  一次只能执行一项缩放操作。

**前端**：前端是保留在 ASE 中的应用的 HTTP/HTTPS 终结点。 不能在前端运行工作负荷。

* ASE 以两个 P2 开始，这对于开发/测试工作负荷和低级生产工作负荷而言已经足够。 强烈建议针对中型到重型生产工作负荷使用 P3。
* 对于中型到重型生产工作负荷，建议至少有四个 P3，以确保在计划的维护开始运行时有足够的前端在运行。 计划的维护活动一次会关闭一个前端。 这会减少维护活动期间可用的前端总容量。
* 前端预配可能需要多达一小时。 
* 要进一步微调，应监视前端池的 CPU 百分比、内存百分比和活动请求等度量值。 如果运行 P3 时 CPU 或内存百分比超出 70%，请添加更多前端。 如果每个前端的活动请求数目平均达到 15,000 到 20,000 个请求，也应该添加更多前端。 总体目标是在运行 P3 时，将 CPU 和内存百分比维持在 70% 以下，将每个前端的活动请求平均数维持在 15,000 个请求以下。  

**辅助角色**：辅助角色是实际运行应用的位置。 增加应用服务计划时，将用尽关联辅助角色池中的辅助角色。

* 无法立即添加辅助角色。 其预配可能需要多达一小时。
* 缩放任何池的计算资源大小时，每个更新域都需要将近 1 小时来完成缩放。 ASE 中有 20 个更新域。 如果缩放包含 10 个实例的辅助角色池的计算大小，可能需要多达 10 小时才能完成。
* 如果更改辅助角色池中所用计算资源的大小，将导致在该辅助角色池中运行的应用冷启动。

更改未运行任何应用的辅助角色池的计算资源大小的最快捷方式是：

* 将辅助角色的数量减至 2 个。  门户中的缩减大小下限为 2 个。 解除分配实例需要几分钟的时间。 
* 选择新的计算大小与实例数目。 从此处开始，需要多达 2 小时才能完成。

如果应用需要更多计算资源，则无法利用前面的指导。 可以改为在另一个辅助角色池中填充具有所需大小的辅助角色，并将应用转移到该池，而无需更改托管那些应用的辅助角色池的大小。

* 在另一个辅助角色池中创建具有所需计算大小的其他实例。 这需要多达一小时才能完成。
* 将需要更大大小且托管应用的应用服务计划重新分配到新配置的辅助角色池。 这是一个快速操作，在一分钟内应可完成。  
* 如果不再需要那些未使用的实例，请缩减第一个辅助角色池。 此操作需要数分钟才能完成。

**自动缩放**：可帮助管理计算资源消耗的工具之一。 可以针对前端或辅助角色池执行自动缩放。 可以执行以下操作：早上增加任何池类型的实例，而晚上减少实例。 或者，可以在辅助角色池中可用的辅助角色数目低于特定阈值时添加实例。

如果想要设置有关计算资源池度量值的自动缩放规则，请记得考虑预配所需的时间。 有关自动调整应用服务环境规模的详细信息，请参阅[如何配置应用服务环境中的自动调整规模][ASEAutoscale]。

### <a name="storage"></a>存储
每个 ASE 配置了 500 GB 的存储空间。 此空间用于 ASE 中的所有应用。 此存储空间属于 ASE 的一部分，目前无法切换为使用存储空间。 如果调整虚拟网络路由或安全性，仍然需要允许访问 Azure 存储，否则 ASE 将无法正常工作。

### <a name="database"></a>数据库
数据库会保存定义环境的信息，以及在其中运行的应用的详细信息。 这也是 Azure 保存的订阅的一部分。 但无法直接进行操作。 如果调整虚拟网络路由或安全性，仍然需要允许访问 SQL Azure，否则 ASE 将无法正常工作。

### <a name="network"></a>网络
与 ASE 配合使用的 VNet 可以是在创建 ASE 时创建的 VNet，也可以是现有的 VNet。 如果在创建 ASE 期间创建子网，会强制 ASE 位于与虚拟网络相同的资源组。 如果想要 ASE 所使用的资源组不同于 VNet 的资源组，那么需要使用 Resource Manager 模板创建 ASE。

用于 ASE 的虚拟网络有一些限制：

* 虚拟网络必须是区域虚拟网络。
* 需要有包含 8 个或更多个地址且为 ASE 部署位置的子网。
* 将某个子网用于托管 ASE 之后，无法更改该子网的地址范围。 因此，建议子网至少包含 64 个地址以适应将来的 ASE 增长。
* 除了 ASE 以外，子网中不能有其他项目。

不同于包含 ASE 的托管服务，[虚拟网络][virtualnetwork]和子网都在用户的控制之下。  可以通过虚拟网络 UI 或 PowerShell 管理虚拟网络。  ASE 可以部署在经典 VNet 或 Resource Manager VNet 中。  经典 VNet 与 Resource Manager VNet 之间的门户和 API 体验会稍有不同，但 ASE 体验相同。

用于托管 ASE 的 VNet 可以使用专用 RFC1918 IP 地址或使用公共 IP 地址。  如果想要使用 RFC1918（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）未涵盖的 IP 范围，则必须在创建 ASE 之前，创建 VNet 和子网以供 ASE 使用。

因为此功能将 Azure 应用服务置于虚拟网络中，这意味着托管在 ASE 中的应用现在可直接访问通过 ExpressRoute 或站点到站点虚拟专用网 (VPN) 提供的资源。 应用服务环境中的应用不需要其他网络功能即可访问托管应用服务环境的虚拟网络可用的资源。 这意味着无需使用 VNET 集成或混合连接，即可访问虚拟网络内部或连接到虚拟网络的资源。 但仍可以使用这两项功能访问网络中未连接到虚拟网络的资源。

例如，可以使用 VNET 集成来与订阅中未连接到 ASE 所在虚拟网络的虚拟网络集成。 像平时一样，仍可使用混合连接来访问其他网络中的资源。  

如果虚拟网络配置了 ExpressRoute VPN，则应注意 ASE 的某些路由要求。 某些用户定义的路由 (UDR) 配置与 ASE 不兼容。 有关使用 ExpressRoute 在虚拟网络中运行 ASE 的更多详细信息，请参阅[使用 ExpressRoute 在虚拟网络中运行应用服务环境][ExpressRoute]。

#### <a name="securing-inbound-traffic"></a>保护入站流量
有两种主要方法可控制 ASE 的入站流量。  可以使用网络安全组 (NSG) 来控制哪些 IP 地址可以访问 ASE（请参阅[如何控制应用服务环境中的入站流量](app-service-app-service-environment-control-inbound-traffic.md)），也可以使用内部负载均衡器 (ILB) 配置 ASE。  如果要使用 NSG 限制对 ILB ASE 的访问，这些功能也可以一起使用。

创建 ASE 时，它会在 VNet 中创建 VIP。  VIP 有两种类型：外部和内部。  创建具有外部 VIP 的 ASE 时，可以通过 Internet 可路由 IP 地址访问 ASE 中的应用。 如果选择内部 VIP，ASE 会使用 ILB 进行配置，并且无法通过 Internet 直接访问。  ILB ASE 仍需要外部 VIP，但它只用来进行 Azure 管理和维护访问。  

在 ILB ASE 创建期间，需要提供 ILB ASE 所用的子域，而且必须针对你指定的子域管理自己的 DNS。  你设置了子域名称，所以也需要管理用于 HTTPS 访问的证书。  创建 ASE 之后，系统会提示提供证书。  若要了解有关创建和使用 ILB ASE 的详细信息，请参阅[在应用服务环境中使用内部负载均衡器][ILBASE]。 

## <a name="portal"></a>门户
可以使用 Azure 门户中的 UI 来管理和监视应用服务环境。 如果有 ASE，则可能会在边栏上看到应用服务符号。 此符号用于表示 Azure 门户中的应用服务环境：

![应用服务环境符号][1]

要打开列出所有的应用服务环境的 UI，可以使用图标，或选择边栏底部的 V 形标记（“>”符号），并选择应用服务环境。 选择其中一个列出的 ASE 会打开用于监视和管理该 ASE 的 UI。

![用于监视和管理应用服务环境的 UI][2]

这是第一个边栏选项卡，其中显示 ASE 的某些属性和每个资源池的度量值图表。 “基本信息”块中显示的某些属性同时也是超链接，将打开与其关联的边栏选项卡。 例如，可选择**虚拟网络**名称，以打开与运行 ASE 的虚拟网络关联的 UI。 “应用服务计划”和“应用”将分别打开不同的边栏选项卡，其中列出位于 ASE 中的项。  

### <a name="monitoring"></a>监视
通过图表可以查看每个资源池中各种不同的性能指标。 对于前端池，可以监视平均 CPU 和内存。 对于辅助角色池，可以监视已用和可用的数量。

多个应用服务计划可以使用同一个辅助角色池中的辅助角色。 工作负荷不是通过与前端服务器相同的方式分布，因此 CPU 和内存使用率不能提供很多有用的信息。 更重要的是要跟踪已用和可用的辅助角色数目，特别是在管理此系统以供他人使用时。  

也可以使用所有可在图表中跟踪的度量值来设置警报。 在此设置警报的效果与在应用服务的其他位置相同。 可从“警报”UI 部分设置警报，也可以钻取到任何度量值 UI 并选择“添加警报”进行设置。

![度量值 UI][3]

刚刚所讨论的度量值是应用服务环境度量值。 另外也有可在应用服务计划级别使用的度量值。 在其中监视 CPU 和内存是很合理的做法。

在 ASE 中，所有应用服务计划都是专用的应用服务计划。 这意味着，只有在分配给该应用服务计划的主机上运行的应用才是该应用服务计划中的应用。 若要查看有关应用服务计划的详细信息，可从 ASE UI 中的任何列表或通过访问**浏览应用服务计划**（其中列出了全部计划）显示应用服务计划。   

### <a name="settings"></a>设置
ASE 边栏选项卡中有一个包含几项重要功能的“设置”部分：

“设置” > “属性”：“设置”边栏选项卡在显示 ASE 边栏选项卡时会自动打开。 位于顶部的是“属性”。 此处显示了许多与“基本信息”重复的项，但“虚拟 IP 地址”和“出站 IP 地址”是非常有用的项。

![“设置”边栏选项卡和“属性”][4]

“设置” > “IP 地址”：在 ASE 中创建 IP 安全套接字层 (SSL) 应用时，需要有一个 IP SSL 地址。 要获取该地址，ASE 需要拥有一些可分配的 IP SSL 地址。 创建的 ASE 最初有一个 IP SSL 地址用于此目的，但可以添加更多地址。 额外的 IP SSL 地址需要付费，如[应用服务定价][AppServicePricing]（在有关 SSL 连接的一节中）所述。 额外的价格是 IP SSL 价格。

**设置** > **前端池** / **辅助角色池**：其中的每个资源池边栏选项卡可让用户查看仅与该资源池有关的信息，并获得全面缩放该资源池所需的控制权。  

每个资源池的基本边栏选项卡分别提供一个图表，其中包含该资源池的度量值。 就像 ASE 边栏选项卡中的图表一样，可以进入该图表并设置所需的警报。 从 ASE 边栏选项卡为特定的资源池设置警报，效果等同于从资源池进行设置。 在辅助角色池的“设置”边栏选项卡中，有权访问在此辅助角色池中运行的所有应用或应用服务计划。

![辅助角色池设置 UI][5]

### <a name="portal-scale-capabilities"></a>门户缩放功能
共有三种缩放操作：

* 更改 ASE 中可供 IP SSL 使用的 IP 地址数。
* 更改用于资源池的计算资源大小。
* 以手动方式或通过自动缩放更改用于资源池的计算资源数。

在门户中，有三种方式可控制资源池中的服务器数：

* 顶部主要 ASE 边栏选项卡中的缩放操作。 可以对前端池和辅助角色池进行多项缩放配置更改。 它们全部以单个操作的方式应用。
* 单个资源池的“缩放”边栏选项卡（位于“设置”下）中的手动缩放操作。
* 可从单个资源池的“缩放”边栏选项卡设置的自动缩放。

要使用 ASE 边栏选项卡上的缩放操作，请将滑块拖到所需的数量并保存。 此 UI 还支持更改大小。  

![缩放 UI][6]

若要使用特定资源池中的手动或自动缩放功能，请根据需要转到“设置” > “前端池” / “辅助角色池”。 然后打开要更改的池。 转到“设置” > “扩大”或“设置” > “增加”。 通过“扩大”边栏选项卡可控制实例数量。 使用“增加”可控制资源大小。  

![缩放设置 UI][7]

## <a name="fault-tolerance-considerations"></a>容错注意事项
可将应用服务环境配置为总共使用最多 55 个计算资源。 在这 55 个计算资源中，只有 50 个可用于承载工作负荷。 其原因有两个。 至少有 2 个前端计算资源。  还剩最多 53 个来支持辅助角色池分配。 为了提供容错功能，还需根据以下规则分配额外计算资源：

* 每个辅助角色池至少需要 1 个无法被分配工作负荷的额外计算资源。
* 当辅助角色池中计算资源的数量超出特定值时，则需要另一个计算资源以实现容错。 在前端池中不需要这样做。

在任何单个辅助角色池中，对于分配到辅助角色池的给定 X 个资源，容错要求如下：

* 如果 X 介于 2 和 20 之间，则可用于工作负荷的可用计算资源量为 X-1。
* 如果 X 介于 21 和 40 之间，则可用于工作负荷的可用计算资源量为 X-2。
* 如果 X 介于 41 和 53 之间，则可用于工作负荷的可用计算资源量为 X-3。

最少的使用量具有 2 个前端服务器和 2 个辅助角色。  上述理论可通过几个示例来进一步说明：  

* 如果在单个池中有 30 个辅助角色，则其中有 28 个可用于托管工作负荷。
* 如果在单个池中有 2 个辅助角色，则有 1 个可用于托管工作负荷。
* 如果在单个池中有 20 个辅助角色，则有 19 个可用于托管工作负荷。  
* 如果在单个池中有 21 个辅助角色，则同样只有 19 个可用于托管工作负荷。  

容错是很重要的环节，但在缩放超过特定阈值时必须留意。 如果想要从 20 个实例添加更多容量，请缩放到 22 或更高，因为 21 并不额外添加任何容量。 缩放到 40 以上时也是一样，下一个添加容量的数为 42。  

## <a name="deleting-an-app-service-environment"></a>删除应用服务环境
如果想要删除应用服务环境，只需使用“应用服务环境”边栏选项卡顶部的“删除”操作即可。 当执行此操作时，系统会提示输入应用服务环境的名称，以确认你确实想要执行此操作。 请注意，删除应用服务环境时，将同时删除其中包含的所有内容。  

![删除应用服务环境 UI][9]  

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅[如何创建应用服务环境](app-service-web-how-to-create-an-app-service-environment.md)。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
