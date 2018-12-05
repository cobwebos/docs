---
title: Azure 操作安全性最佳做法 | Microsoft Docs
description: 本文提供有关 Azure 操作安全性的一套最佳做法。
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: ae6eeb2506eb82160c68e15e17eeb95c1e2ec046
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853603"
---
# <a name="azure-operational-security-best-practices"></a>Azure 操作安全性最佳做法
Azure 操作安全性是指用户可用于在 Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的功能获得的知识，包括 [安全开发生命周期 (SDL)](https://www.microsoft.com/sdl)、[Microsoft 安全响应中心](https://www.microsoft.com/msrc?rtc=1)计划以及对网络安全威胁形态的深刻认识。

本文介绍一系列安全最佳做法。 这些最佳做法衍生自我们的 Azure 数据库安全经验和客户的经验。

对于每项最佳做法，本文将说明：
-   最佳实践是什么
-   为何要启用该最佳实践
-   如果无法启用该最佳实践，可能的结果是什么
- 如何学习启用最佳实践

这篇 Azure 操作安全性最佳做法以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文会定期更新以反映这些更改。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>监视存储服务的意外行为更改
诊断和排查在云环境中托管的分布式应用程序中的问题可能会比在传统环境中更复杂。 应用程序可以部署在 PaaS 或 IaaS 基础结构、本地、移动设备，或这些环境的某种组合中。 应用程序的网络流量可能会遍历公用和专用网络，你的应用程序可能使用多种存储技术。

应持续监视应用程序使用的存储服务是否存在任何意外行为更改（如响应时间变长）。 若要收集更详细的数据和深度分析问题，请使用日志记录。 从监视和日志记录获取的诊断信息将有助于确定应用程序所遇到问题的根本原因。 然后，可以排查该问题，并确定修正问题的相应步骤。

[Azure 存储分析](../storage/storage-analytics.md)执行日志记录并为 Azure 存储帐户提供指标数据。 建议使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。

## <a name="prevent-detect-and-respond-to-threats"></a>防范、检测和应对威胁
[Azure 安全中心](../security-center/security-center-intro.md)有助于预防、检测和响应威胁，同时增加 Azure 资源安全的可见性和可控性。 它提供对 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于各种安全解决方案。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 标准层将这些功能扩展到本地和其他云中。 借助安全中心标准层，可以查找和修复安全漏洞、应用访问控制和应用程序控制来阻止恶意活动、使用分析和智能功能检测威胁，以及在受到攻击时迅速做出响应。 可以尝试安全中心标准版，头 60 天免费。 建议[将 Azure 订阅加入安全中心标准层](../security-center/security-center-get-started.md)。

使用安全中心，可以获得所有 Azure 资源的安全状态的核心情况。 一眼就可验证适当的安全控件是否配置到位且配置正确，还可快速确认任何需要注意的资源。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>监视基于端到端方案的网络监视
客户在 Azure 中通过合并虚拟网络、ExpressRoute、应用程序网关和负载均衡器等网络资源来构建端到端网络。 监视适用于每个网络资源。

[Azure 网络观察程序](../network-watcher/network-watcher-monitoring-overview.md)是一项区域性服务。 其诊断和可视化工具可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。

以下是网络监视和可用工具的最佳做法。

最佳做法：使用数据包捕获实现远程网络监视的自动化。  
详细信息：使用网络观察程序监视和诊断网络问题，无需登录 VM。 通过设置警报触发[数据包捕获](../network-watcher/network-watcher-alert-triggered-packet-capture.md)，并获取数据包级别上的实时性能信息访问权限。 如果遇到问题，可进行详细调查，获得更精确的诊断。

最佳做法：使用流日志深入了解网络流量。  
详细信息：使用[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-overview.md)更深入地了解网络流量模式。 流日志中的信息可帮助收集符合性数据、审核和监视网络安全配置文件。

最佳做法：诊断 VPN 连接问题。  
详细信息：使用网络观察程序来[诊断最常见的 VPN 网关和连接问题](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 不仅可以确定问题，还可以使用详细日志进一步调查。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>使用经验证的 DevOps 工具确保安全部署
使用以下 DevOps 最佳做法来确保企业和团队多产且高效。

最佳做法：自动生成和部署服务。  
详细信息：[基础结构即代码](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)是一组技术和实践，使 IT 专业人员不再需要日复一日地生成和管理模块化基础结构。 使得 IT 专业人员生成和维护新式服务器环境的方式就像是软件开发人员生成和维护应用程序代码的方式。

[Azure 资源管理器](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)允许用户使用声明性模板预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。

最佳做法：自动生成并部署到 Azure Web 应用或云服务。  
详细信息：可以使用 Azure Pipelines [自动生成并部署](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)到 Azure Web 应用或云服务。 Azure Pipelines 在每次代码签入后对 Azure 执行一次生成，然后自动部署二进制文件。 包生成过程与 Visual Studio 中的 Package 命令等效，而发布步骤与 Visual Studio 中的 Publish 命令等效。

最佳做法：使用持续部署。  
详细信息：[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) 是实现多阶段部署和管理发布过程自动化的解决方案。 创建托管的持续部署管道，快速、轻松地频繁发布。 通过 Azure Pipelines，可以使发布过程自动化，还可以拥有预定义的批准工作流。 根据需要进行本地部署和部署到云、扩展和自定义。

最佳做法：在推出应用或将更新部署到生产环境之前，先检查该应用的性能。  
详细信息：通过使用 Azure Test Plans 执行以下操作来运行基于云的[负载测试](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)：

- 在应用中查找性能问题。
- 提高部署质量。
- 请确保应用始终可用。
- 确保应用可以处理下一次启动或市场营销活动的流量。

最佳做法：监视应用程序性能。  
详细信息：[Azure Application Insights](../application-insights/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用 Application Insights 来监视实时 Web 应用程序。 它会自动检测性能异常。 其中包含分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。 Application Insights 有助于持续提高性能与可用性。

## <a name="mitigate-and-protect-against-ddos"></a>缓解和防范 DDoS
分布式拒绝服务 (DDoS) 是企图耗尽应用程序资源的一种攻击。 其目的是影响应用程序的可用性和处理合法请求的能力。 这些攻击正变得越来越复杂，且规模和影响程度越来越高。 它们可能会将任何可通过 Internet 公开访问的终结点作为目标。

设计和构建 DDoS 复原能力需要规划和设计各种故障模式。

下面是用于在 Azure 上构建 DDoS 可复原服务的最佳做法。

最佳做法：确保优先考虑从设计和实施到部署和操作的整个应用程序生命周期的安全性。 应用程序可能包含 bug，使相对较少的请求使用过多的资源，从而导致服务中断。  
详细信息：为帮助保护 Microsoft Azure 上运行的服务，应该对应用程序体系结构有充分的了解，并重点关注[软件质量的五大要素](https://docs.microsoft.com/azure/architecture/guide/pillars)。 应该清楚典型的流量大小、应用程序与其他应用程序之间的连接模型，以及向公共 Internet 公开的服务终结点。

至关重要的一点是，确保应用程序具有足够的弹性，可应对针对应用程序本身的拒绝服务攻击。 从[安全开发生命周期 (SDL)](https://www.microsoft.com/en-us/sdl) 开始，安全和隐私就已内置到 Azure 平台中。 SDL 可以解决每个开发阶段的安全性，并确保 Azure 不断更新，以变得越来越安全。

最佳做法：采用可[横向缩放](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)的应用程序设计，以满足放大负载的需求，尤其是防范 DDoS 攻击。 如果应用程序依赖于服务的单个实例，则会造成单一故障点。 预配多个实例能够提高复原能力和可伸缩性。  
详细信息：对于 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)，请选择提供多个实例的[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

对于 Azure 云服务，请将每个角色配置为使用[多个实例](../cloud-services/cloud-services-choose-me.md)。

对于 [Azure 虚拟机](../virtual-machines/windows/overview.md)，请确保 VM 体系结构包含多个 VM，并且每个 VM 包含在[可用性集](../virtual-machines/virtual-machines-windows-manage-availability.md)中。 建议使用虚拟机规模集来实现自动缩放功能。

最佳做法：应用程序中的分层安全防御可以减少攻击成功的可能性。 使用 Azure 平台的内置功能对其应用程序实施安全设计。  
详细信息：攻击风险会随着应用程序的规模（外围应用）的增大而增大。 可以使用允许列表关闭负载均衡器（[Azure 负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md)和 [Azure 应用程序网关](../application-gateway/application-gateway-create-probe-portal.md)）上不需要的公开 IP 地址空间和侦听端口，来减少外围应用。

[网络安全组](../virtual-network/security-overview.md)是缩小受攻击面的另一种方法。 可以使用[服务标记](../virtual-network/security-overview.md#service-tags)和[应用程序安全组](../virtual-network/security-overview.md#application-security-groups)来最大程度地简化安全规则的创建，并将网络安全性配置为应用程序结构的自然扩展。

应尽可能地在[虚拟网络](../virtual-network/virtual-networks-overview.md)中部署 Azure 服务。 这种做法可让服务资源通过专用 IP 地址通信。 来自虚拟网络的 Azure 服务流量默认使用公共 IP 地址作为源 IP 地址。

使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。

我们经常看到，客户本地资源会连同其在 Azure 中资源的一起受到攻击。 如果将本地环境连接到 Azure，尽量不要在公共 Internet 上公开本地资源。

Azure 具有两个 DDoS [服务产品](../virtual-network/ddos-protection-overview.md)，提供网络攻击防护：

- 基本防护默认已集成到 Azure 中，不收取额外的费用。 全球部署的 Azure 网络的规模和容量通过始终开启的监视和实时缓解措施，来防御公用网络层攻击。 基本防护无需用户配置或应用程序更改，并帮助保护所有 Azure 服务，包括 Azure DNS 等 PaaS 服务。
- 标准防护提供针对网络攻击的高级 DDoS 缓解功能。 这些功能自动经过优化，可保护特定的 Azure 资源。 在创建虚拟网络期间，可以轻松启用保护。 也可以在创建之后启用它，而不需要对应用程序或资源做出任何更改。

## <a name="next-steps"></a>后续步骤
有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](security-best-practices-and-patterns.md)。

以下资源提供了有关 Azure 安全性及相关 Microsoft 服务的更多常规信息：
* [Azure 安全团队博客](https://blogs.msdn.microsoft.com/azuresecurity/) - 随时掌握 Azure 安全性的最新信息
* [Microsoft 安全响应中心](https://technet.microsoft.com/library/dn440717.aspx) - 可在其中报告 Microsoft 安全漏洞（包括 Azure 问题）或将其通过电子邮件发送到 secure@microsoft.com
