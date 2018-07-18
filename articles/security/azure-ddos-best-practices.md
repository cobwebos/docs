---
title: Azure DDoS 防护最佳做法和参考体系结构 | Microsoft Docs
description: 了解如何利用日志记录数据深入了解应用程序的情况。
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: b802c7b96bd8d0cfa56347d45542495caf69d7e4
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824704"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS 防护：最佳做法和参考体系结构

本文面向 IT 决策人和安全管理人员。 预期读者熟悉 Azure、网络和安全性。

针对分布式拒绝服务 (DDoS) 复原能力进行设计需要规划和设计各种故障模式。 本文提供在 Azure 中针对 DDoS 攻击设计应用程序复原能力的最佳做法。

## <a name="types-of-attacks"></a>攻击类型

DDoS 是企图耗尽应用程序资源的一种攻击。 其目的是影响应用程序的可用性和处理合法请求的能力。 此类攻击正变得越来越复杂，且规模和影响程度越来越高。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

Azure 针对 DDoS 攻击提供持续保护。 这种保护默认已集成到 Azure 平台中，且不收取额外的费用。 

除了在平台中提供的核心 DDoS 防护以外，[Azure DDoS 防护标准](https://azure.microsoft.com/services/ddos-protection/)还针对网络攻击提供高级 DDoS 缓解功能。 这些功能自动经过优化，可保护特定的 Azure 资源。 在创建新虚拟网络期间，可以轻松启用保护。 也可以在创建之后启用它，而不需要对应用程序或资源做出任何更改。

![Azure DDoS 防护在防止客户和虚拟网络遭受攻击时发挥的作用](media/azure-ddos-best-practices/image1.png)

DDoS 攻击在可划分为三种类别：容量耗尽攻击、协议攻击和资源攻击。

### <a name="volumetric-attacks"></a>容量耗尽攻击

容量耗尽攻击是最常见的 DDoS 攻击类型。 容量耗尽攻击属于暴力式的攻击，针对网络和传输层。 这种攻击企图耗尽网络链接等资源。 

它们通常使用多个受感染的系统，在网络层填满看似合法的流量。 它们使用不同的网络层协议，例如 Internet 控制消息协议 (ICMP)、用户数据报协议 (UDP) 和传输控制协议 (TCP)。

最常用的网络层 DDoS 攻击包括 TCP SYN 泛洪、ICMP 回送、UDP 泛洪、DNS 和 NTP 放大攻击。 此类攻击不仅可以用于干扰服务，而且还能充当更险恶、更有针对性的网络入侵的烟幕。 最近发生的、对 GitHub 造成影响的 [Memcached 攻击](https://www.wired.com/story/github-ddos-memcached/)就是容量耗尽攻击的一个例子。 这次攻击针对 UDP 端口 11211，生成了 1.35 Tb/s 的攻击流量。

### <a name="protocol-attacks"></a>协议攻击

协议攻击针对应用程序协议。 此类攻击试图用尽防火墙、应用程序服务器和负载均衡器等基础结构设备中的所有可用资源。 协议攻击使用格式不当的数据包，或包含协议异常。 这些攻击的运作方式是发送极大数量的开放请求，让服务器和其他通信设备做出应答并等待数据包响应。 目标会尝试对开放请求做出响应，最终导致系统崩溃。

基于协议的 DDoS 攻击的最常见示例是 TCP SYN 泛洪。 这种攻击尝试发送一系列 TCP SYN 请求，导致目标瘫痪。 其目的是使目标无法做出响应。 2016 Dyn 中断（同时也属于应用层攻击）包括针对 Dyn DNS 服务器端口 53 的 TCP SYN 泛洪。

### <a name="resource-attacks"></a>资源攻击

资源攻击针对应用层。 它们触发后端进程，企图使系统瘫痪。 资源攻击滥用看似正常的流量，但这些流量携带了针对服务器的 CPU 密集型查询。 与其他类型的攻击相比，此类攻击耗尽资源所需的流量更少。 资源攻击中的流量与合法流量无法区分，因此很难检测到这种攻击。 最常见的资源攻击发生在 HTTP/HTTPS 和 DNS 服务上。

## <a name="shared-responsibility-in-the-cloud"></a>云中责任分担

深层防御策略可帮助应对不断增加的攻击类型和复杂性。 安全性的责任由客户和 Microsoft 共同分担。 Microsoft 将这种策略称作[责任分担模型](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/)。 下图显示了责任范围的划分：

![客户和 Azure 的责任](media/azure-ddos-best-practices/image2.png)

Azure 客户查看 Microsoft 最佳做法，构建采用防故障设计且经过测试的全局分布式应用程序，并从中受益。

## <a name="fundamental-best-practices"></a>基本最佳做法

以下部分提供了有关在 Azure 中构建弹性应对 DDoS 的服务的规范性指导。

### <a name="design-for-security"></a>安全设计

确保优先考虑从设计和实施到部署和操作的整个应用程序生命周期的安全性。 应用程序可能包含 bug，使相对较少的请求使用过多的资源，导致服务中断。 

为帮助保护 Microsoft Azure 上运行的服务，应该对应用程序体系结构有充分的了解，并重点关注[软件质量的五大要素](https://docs.microsoft.com/azure/architecture/guide/pillars)。
应该清楚典型的流量大小、应用程序与其他应用程序之间的连接模型，以及向公共 Internet 公开的服务终结点。

至关重要的一点是，确保应用程序具有足够的弹性，可应对针对应用程序本身的拒绝服务攻击。 从[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/default.aspx) 开始，安全和隐私就已内置到 Azure 平台中。 SDL 可以解决每个开发阶段的安全性，并确保 Azure 不断更新，以变得越来越安全。

### <a name="design-for-scalability"></a>可伸缩性设计

可伸缩性是指系统处理增加的负载的能力。 必须采用可[横向缩放](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)的应用程序设计，以满足放大负载的需求，尤其是防范 DDoS 攻击。 如果应用程序依赖于服务的单个实例，则会造成单一故障点。 预配多个实例能够提高复原能力和可伸缩性。

对于 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)，请选择提供多个实例的[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 对于 Azure 云服务，请将每个角色配置为使用[多个实例](../cloud-services/cloud-services-choose-me.md)。 对于 [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，请确保虚拟机 (VM) 体系结构包含多个 VM，并且每个 VM 包含在[可用性集](../virtual-machines/virtual-machines-windows-manage-availability.md)中。 我们建议使用[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)来实现自动缩放功能。

### <a name="defense-in-depth"></a>深层防御

深层防御的理念是通过多样化的防御策略来掌控风险。 应用程序中的分层安全防御可以减少攻击成功的可能性。 我们建议使用 Azure 平台的内置功能对其应用程序实施安全设计。

例如，攻击风险会随着应用程序的规模（外围应用）的增大而增大。 可以使用白名单关闭负载均衡器（[Azure 负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md)和 [Azure 应用程序网关](../application-gateway/application-gateway-create-probe-portal.md)）上不需要的公开 IP 地址空间和侦听端口，来减少外围应用。 [网络安全组 (NSG)](../virtual-network/security-overview.md) 是缩小受攻击面的另一种方法。
可以使用[服务标记](/virtual-network/security-overview.md#service-tags)和[应用程序安全组](/virtual-network/security-overview.md#application-security-groups)来最大程度地简化安全规则的创建，并将网络安全性配置为应用程序结构的自然扩展。

应尽可能地在[虚拟网络](../virtual-network/virtual-networks-overview.md)中部署 Azure 服务。 这种做法可让服务资源通过专用 IP 地址通信。 来自虚拟网络的 Azure 服务流量默认使用公共 IP 地址作为源 IP 地址。 使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。

我们经常看到，客户本地资源会连同其在 Azure 中资源的一起受到攻击。 如果将本地环境连接到 Azure，我们建议尽量不要在公共 Internet 上公开本地资源。 可以通过在 Azure 中部署已知的公共实体，来利用 Azure 的规模和高级 DDoS 防护功能。 由于这些可公开访问的实体通常会成为 DDoS 攻击的目标，将它们放在 Azure 中可以降低对本地资源的影响。

## <a name="azure-offerings-for-ddos-protection"></a>Azure 中的 DDoS 防护产品/服务

Azure 提供两个可以防御网络攻击（第 3 层和第 4 层）的 DDoS 服务产品/服务 - 基本 DDoS 防护和标准 DDoS 防护。 

### <a name="ddos-protection-basic"></a>基本 DDoS 防护

基本防护默认已集成到 Azure 中，不收取额外的费用。 全球部署的 Azure 网络的规模和容量通过始终开启的监视和实时缓解措施，来防御公用网络层攻击。 基本 DDoS 防护不需要对用户配置或应用程序做出任何更改。 基本 DDoS 防护会帮助保护所有 Azure 服务，包括 Azure DNS 等 PaaS 服务。

![Azure 网络地图，其中包含文字“全球 DDoS 缓解措施”和“领先的 DDoS 缓解能力”](media/azure-ddos-best-practices/image3.png)

Azure 中的基本 DDoS 防护包括硬件和软件组件。 软件控制平面决定何时、何处的何种流量应该流过用于分析和消除攻击流量的硬件设备。 控制平面根据基础结构范围的 DDoS 防护策略做出此决定。 此策略是静态设置的，将全局应用到所有 Azure 客户。

例如，DDoS 防护策略指定在生成多大的流量后应触发保护。 （即，应该通过清理设备路由租户的流量。）然后，策略指定清理设备应以何种方式缓解攻击。

Azure DDoS 防护基本服务用于实现基础结构保护和 Azure 平台保护。 当流量速率过大，从而可能影响到多租户环境中的多个客户时，该服务会降低流量。 它不提供警报或者按客户自定义的策略。

### <a name="ddos-protection-standard"></a>标准 DDoS 防护

标准防护提供增强的 DDoS 缓解功能。 这种防护自动经过优化，可帮助保护虚拟网络中的特定 Azure 资源。 可在任何新的或现有的虚拟网络上启用保护，且无需对应用程序或资源做出任何更改。 与基本服务相比，该服务具有多种优势，包括日志记录、警报和遥测。 以下部分概述了标准 Azure DDoS 防护服务的重要功能。

#### <a name="adaptive-real-time-tuning"></a>自适应实时优化

Azure DDoS 防护基本服务可帮助保护客户，并防止影响其他客户。 例如，如果为典型的合法传入流量预配了某个服务，并且该流量小于基础结构范围 DDoS 防护策略的触发率，那么，针对该客户资源的 DDoS 攻击可能会被忽略。 一般来说，最近攻击（例如多向量 DDoS）的复杂性，以及租户的应用程序特定行为，要求按客户采用自定义的保护策略。 该服务使用两项见解来实现这种自定义：

- 自动学习每个客户（每个 IP）的第 3 层和第 4 层流量模式。

- 尽量减少误报，因为 Azure 的规模可让它吸收大量的流量。

![标准 DDoS 防护工作原理示意图，其中圈住了“策略生成”](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS 防护遥测、监视和警报

标准 DDoS 防护在 DDoS 攻击持续期间通过 [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) 公开丰富的遥测数据。 可以针对 DDoS 防护使用的任何 Azure Monitor 指标配置警报。 可将日志记录集成到 Splunk（Azure 事件中心）、Azure Log Analytics 和 Azure 存储，以通过 Azure Monitor 诊断界面进行高级分析。

##### <a name="ddos-mitigation-policies"></a>DDoS 缓解策略

在 Azure 门户中，选择“监视” > “指标”。 在“指标”窗格上，依次选择资源组、“公共 IP 地址”资源类型和 Azure 公共 IP 地址。 DDoS 指标将显示在“可用指标”窗格中。

标准 DDoS 防护针对已启用 DDoS 的虚拟网络中受保护资源的每个公共 IP，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 可以选择“触发 DDoS 缓解措施的入站数据包数”指标来查看策略阈值。

![可用指标和指标图表](media/azure-ddos-best-practices/image7.png)

策略阈值是通过基于机器学习的网络流量探查自动配置的。 仅当超过策略阈值时，才会对受攻击的 IP 地址进行 DDoS 缓解。

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>受 DDoS 攻击的 IP 地址的指标

如果公共 IP 地址受到攻击，则“是否受 DDoS 攻击”指标的值将切换为 1，因为 DDoS 防护会针对攻击流量执行缓解措施。

![“是否受 DDoS 攻击”指标和图表](media/azure-ddos-best-practices/image8.png)

我们建议对此指标配置警报。 然后，在对公共 IP 地址执行主动的 DDoS 缓解措施时会收到通知。

有关详细信息，请参阅[使用 Azure 门户管理标准 Azure DDoS 防护](../virtual-network/ddos-protection-manage-portal.md)。

#### <a name="web-application-firewall-for-resource-attacks"></a>防范资源攻击的 Web 应用程序防火墙

针对应用层中发生的资源攻击，应该配置 Web 应用程序防火墙 (WAF) 来帮助保护 Web 应用程序。 WAF 会检查入站 Web 流量，以阻止 SQL 注入、跨站点脚本、DDoS 和其他第 7 层攻击。 Azure 提供 [WAF 作为应用程序网关的一项功能](../application-gateway/application-gateway-web-application-firewall-overview.md)，以便在出现常见攻击和漏洞时为 Web 应用程序提供集中保护。 此外，Azure 合作伙伴还会通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)提供其他 WAF 产品/服务，它们可能更适合解决你的需求。

即使是 Web 应用程序防火墙这样的服务，也很容易遭受容量耗尽和状态耗尽攻击。 我们强烈建议在 WAF 虚拟网络上启用标准 DDoS 防护，以帮助防范容量耗尽攻击和协议攻击。 有关详细信息，请参阅 [DDoS 防护参考体系结构](#ddos-protection-reference-architectures)部分。

### <a name="protection-planning"></a>保护规划

规划和准备对于了解系统在遇到 DDoS 攻击期间的表现至关重要。 设计事件管理响应计划属于此工作的一部分。

如果使用标准 DDoS 防护，请确保在面向 Internet 的终结点的虚拟网络上启用它。 配置 DDoS 警报有助于持续密切关注基础结构上存在的任何潜在攻击。 

应独立监视应用程序。 了解应用程序的正常行为。 如果应用程序在遇到 DDoS 攻击期间的行为不符合预期，请准备好采取措施。

#### <a name="testing-through-simulations"></a>通过模拟进行测试

良好的做法是通过执行定期模拟，测试服务对攻击做出响应的方式的假设。 在测试期间，验证服务或应用程序是否持续按预期运行，并且不会干扰用户体验。 从技术和流程角度识别现实与模拟中的差距，并将其融入 DDoS 响应策略。 我们建议在过渡环境或者在非高峰期执行此类测试，以便最大程度地降低对生产环境的影响。

我们与 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 合作开发一个界面，Azure 客户可在其中针对已启用 DDoS 防护的公共终结点生成流量，以进行模拟。 使用 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 模拟可以：

- 验证 Azure DDoS 防护如何帮助防范 Azure 资源受到 DDoS 攻击。

- 受到 DDoS 攻击时优化事件响应过程。

- 阐述 DDoS 符合性。

- 培训网络安全团队。

网络安全需要不断在防御技术上做出创新。 Azure DDoS 标准防护是一个艺术级的产品/服务，它提供有效的解决方案来缓解日益复杂的 DDoS 攻击。

## <a name="components-of-a-ddos-response-strategy"></a>DDoS 响应策略的组件

针对 Azure 资源的 DDoS 攻击通常只需用户做出极少量的干预。 在事件响应策略中整合 DDoS 缓解措施有助于进一步减少对业务连续性产生的影响。

### <a name="microsoft-threat-intelligence"></a>Microsoft 威胁情报

Microsoft 部署了广泛的威胁情报网络。 此网络利用了为 Microsoft 联机服务和 Microsoft 合作伙伴提供服务的扩展安全社区的集体知识，以及与 Internet 安全社区的内部关系。 

作为一家重要的基础结构提供商，Microsoft 会提前收到有关威胁的警告。 Microsoft 从其 Microsoft 联机服务和全球客户群体收集威胁情报。 Microsoft 将所有威胁情报融入到 Azure DDoS 防护产品中。

此外，Microsoft 的反数字犯罪部门 (DCU) 还会针对僵尸网络执行打击策略。 僵尸网络是 DDoS 攻击的常见指挥源头。

### <a name="risk-evaluation-of-your-azure-resources"></a>针对 Azure 资源的风险评估

必须不断了解 DDoS 攻击的风险范围。 定期询问自己： 
- 哪些新公开发布的 Azure 资源需要保护？

- 服务中是否存在单一故障点？ 

- 如何隔离服务，以限制某项攻击造成的影响，同时使服务仍可供合法客户使用？

- 是否有虚拟网络应启用标准 DDoS 防护，但却没有启用？ 

- 我的服务在跨多个区域故障转移后是否保持主动/主动状态？

### <a name="customer-ddos-response-team"></a>客户 DDoS 响应团队

建立 DDoS 响应团队是快速有效地对攻击做出响应的关键一步。 在组织中确定负责监督规划和执行的各个联系人。 此 DDoS 响应团队应该全面了解标准 Azure DDoS 防护服务。 确保该团队能够与内部和外部客户（包括 Microsoft 支持团队）协作，以识别和缓解攻击。

对于 DDoS 响应团队，Microsoft 建议使用模拟演练作为服务可用性和连续性规划的日常组成部分。 这些演练应包括规模测试。

### <a name="alerts-during-an-attack"></a>攻击期间的警报

标准 Azure DDoS 防护将识别并缓解 DDoS 攻击，而无需任何用户干预。 若要在受保护公共 IP 受到的攻击被主动缓解时收到通知，可以针对“是否受 DDoS 攻击”指标[配置警报](../virtual-network/ddos-protection-manage-portal.md)。 可以选择针对其他 DDoS 指标创建警报，以了解攻击规模、丢弃的流量和其他详细信息。

#### <a name="when-to-contact-microsoft-support"></a>何时与 Microsoft 支持部门联系

- 在 DDoS 攻击期间，发现受保护资源的性能严重下降，或资源不可用。

- 认为 DDoS 防护服务不按预期方式运行。 

  仅当指标值“触发 DDoS 缓解措施的策略(TCP/TCP SYN/UDP)”低于受保护公共 IP 资源上收到的流量时，DDoS 防护服务才会启动缓解措施。

- 规划的某个病毒事件导致网络流量显著增加。

- 某个行动者威胁针对你的资源发起 DDoS 攻击。

对于影响关键业务的攻击，请创建严重性为 A 的[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="post-attack-steps"></a>攻击后的措施

在发生攻击后执行事后剖析并按需重新调整 DDoS 响应策略，始终是一个良好的策略。 注意事项：

- 服务或用户的体验是否因缺少可缩放的体系结构而受到任何干扰？

- 哪些应用程序或服务受到的影响最大？

- DDoS 响应策略的效果如何，如何对它做出改进？

如果你怀疑自己受到 DDoS 攻击，请通过正常的 Azure 支持渠道上报。

## <a name="ddos-protection-reference-architectures"></a>DDoS 防护参考体系结构

标准 DDoS 防护面向[虚拟网络中部署的服务](../virtual-network/virtual-network-for-azure-services.md)。 对于其他服务，将会应用默认的基本 DDoS 防护服务。 以下参考体系结构按场景进行整理，体系结构模式已分组在一起。

### <a name="virtual-machine-windowslinux-workloads"></a>虚拟机 (Windows/Linux) 工作负荷

#### <a name="application-running-on-load-balanced-vms"></a>负载均衡的 VM 上运行的应用程序

针对如何通过在负载均衡器后的规模集中运行多个 Windows VM 以提高可用性和可伸缩性，此参考体系结构显示了一组已经过验证的做法。 可对任何无状态工作负荷（例如 Web 服务器）使用此体系结构。

![负载均衡 VM 上运行的应用程序的参考体系结构示意图](media/azure-ddos-best-practices/image9.png)

在此体系结构中，工作负荷分布于多个 VM 实例上。 有单个公共 IP 地址，并且 Internet 流量通过负载均衡器分布到这些 VM。 与公共 IP 关联的 Azure (Internet) 负载均衡器的虚拟网络上已启用标准 DDoS 防护。

负载均衡器将传入的 Internet 请求分配到 VM 实例。 虚拟机规模集允许手动或者基于预定义规则自动扩展或缩减 VM 的数量。 如果资源遭受 DDoS 攻击，此功能非常重要。 有关此参考体系结构的详细信息，请参阅[此文](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)。

#### <a name="application-running-on-windows-n-tier"></a>在 Windows N 层上运行的应用程序

有许多方法可用来实现 N 层体系结构。 下图显示了典型的三层 Web 应用程序。 此体系结构是基于[运行负载均衡的 VM 以实现可伸缩性和可用性](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)一文构建的。 Web 层和业务层都使用负载均衡的 VM。

![Windows N 层上运行的应用程序的参考体系结构示意图](media/azure-ddos-best-practices/image10.png)

在此体系结构中，已在虚拟网络上启用标准 DDoS 防护。 虚拟网络中的所有公共 IP 将得到第 3 层和第 4 层 DDoS 防护。 要获得第 7 层防护，请部署 WAF SKU 中的应用程序网关。 有关此参考体系结构的详细信息，请参阅[此文](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)。

#### <a name="paas-web-application"></a>PaaS Web 应用程序

此参考体系结构显示了在单个区域中运行 Azure 应用服务应用程序。 此体系结构显示了针对使用 [Azure 应用服务](https://azure.microsoft.com/documentation/services/app-service/)和 [Azure SQL 数据库](https://azure.microsoft.com/documentation/services/sql-database/)的 Web 应用程序运用的一套经过证实的做法。
已针对故障转移场景设置了备用区域。

![PaaS Web 应用程序的参考体系结构示意图](media/azure-ddos-best-practices/image11.png)

Azure 流量管理器将传入的请求路由到某个区域中的应用程序网关。 在正常操作期间，它会将请求路由到活动区域中的应用程序网关。 如果该区域不可用，流量管理器会故障转移到备用区域中的应用程序网关。

从 Internet 发往 Web 应用程序的所有流量通过流量管理器路由到[应用程序网关公共 IP 地址](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。 在此场景中，应用服务（Web 应用）本身不直接面向外部，且受应用程序网关的保护。 

我们建议配置应用程序网关 WAF SKU（预防模式）来帮助防范第 7 层（HTTP/HTTPS/Web 套接字）攻击。 此外，Web 应用配置为[仅接受来自应用程序网关 IP 地址的流量](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)。

有关此参考体系结构的详细信息，请参阅[此文](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

### <a name="mitigation-for-non-web-paas-services"></a>针对非 Web PaaS 服务的缓解措施

#### <a name="hdinsight-on-azure"></a>Azure 上的 HDInsight

此参考体系结构显示如何为 [Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/)配置标准 DDoS 防护。 确保 HDInsight 群集已链接到虚拟网络，并在该虚拟网络上启用了 DDoS 防护。

![“HDInsight”和“高级设置”窗格，其中包含虚拟网络设置](media/azure-ddos-best-practices/image12.png)

![用于启用 DDoS 防护的选项](media/azure-ddos-best-practices/image13.png)

在此体系结构中，从 Internet 发往 HDInsight 群集的流量路由到与 HDInsight 网关负载均衡器关联的公共 IP。 然后，网关负载均衡器直接将流量发送到头节点或工作节点。 由于已在 HDInsight 虚拟网络上启用标准 DDoS 防护，虚拟网络中的所有公共 IP 将得到第 3 层和第 4 层 DDoS 防护。 此参考体系结构可与 N 层和多区域参考体系结构相结合。

有关此参考体系结构的详细信息，请参阅[使用 Azure 虚拟网络扩展 Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) 文档。


> [!NOTE]
> 虚拟网络中使用公共 IP 的 PowerApps 或 API 管理的 Azure 应用服务环境都原生不受支持。

## <a name="next-steps"></a>后续步骤

* [Azure DDoS 防护产品页](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS 防护博客](http://aka.ms/ddosblog)

* [Azure DDoS 防护文档](../virtual-network/ddos-protection-overview.md)
