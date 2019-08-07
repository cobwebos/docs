---
title: Azure 澳大利亚安全系统监视
description: 指导如何在澳大利亚地区配置系统监视, 以满足澳大利亚政府政策、法规和法律的特定要求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779328"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Azure 澳大利亚安全系统监视

具有稳定的安全策略 (包括实时监视和日常安全评估) 对你的 IT 环境 (包括云) 的日常运营安全至关重要。

云安全是客户与云提供商之间的共同努力。 Microsoft Azure 提供了四项服务来满足这些要求, 并考虑到[澳大利亚网络安全中心 (ACSC) 信息安全手册控制](https://acsc.gov.au/infosec/ism/index.htm)(ISM) 中包含的建议,具体而言, 即实现集中式事件日志记录、事件日志审核和安全漏洞评估与管理。 Microsoft Azure 服务是:

* Azure 安全中心
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC 建议将这些服务用于**受保护**的数据。 通过使用这些服务, 你可以主动监视和分析你的 IT 环境, 并在何处最好地分配资源来增强安全性。 其中每个服务都是组合解决方案的一部分, 可为你提供最佳的洞察力、建议和保护。

## <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)提供统一的安全管理控制台, 你可以使用它来监视和增强 Azure 资源和你的托管数据的安全性。 Azure 安全中心包含安全分数, 这是基于 Azure 顾问的最佳实践配置状态和 Azure 策略的总体符合性的分析分数。

Azure 安全中心为 Azure 客户提供以下功能:

* 安全策略、评估和推荐
* 安全事件收集和搜索
* 访问和应用程序控件
* 高级威胁检测
* 实时虚拟机访问控制
* 混合安全性

可以扩展 Azure 安全中心监视的资源范围, 以在混合云环境中包括受支持的本地资源。 这包括当前由受支持的 System Center Operations Manager 版本监视的本地资源。

安全中心的 "标准" 层还提供了 " [.asd](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)" 所需的基于云的安全控制。 其中包括应用程序允许列表和通过实时访问对管理权限的限制。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)是适用于所有 Azure 资源的集中式日志记录解决方案, 其中包括 Log Analytics 和 Application Insights。 从 Azure 资源收集两种关键数据类型: 日志和指标。 在 Azure Monitor 收集后, 可以通过各种工具和各种用途来使用日志记录信息。

![Azure Monitor 概述](media/overview.png)

Azure Monitor 还包括 "Azure 活动日志"。 SActivity 日志存储在 Azure 内发生的所有订阅级别事件。 它允许 Azure 客户查看在其 Azure 资源上进行操作的 "人员、操作和时间"。 发送到 Azure Monitor 和 Azure 活动日志事件的基于资源的日志可以使用生成的 Kusto 查询语言并非。 然后, 可以导出这些日志, 用于创建自定义仪表板和视图, 并将其配置为触发警报和通知。

### <a name="azure-advisor"></a>Azure Advisor

[Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-overview)分析 azure 订阅中支持的 azure 资源、系统生成的日志文件和当前资源配置。 Azure 顾问中提供的分析会实时生成, 并基于 Microsoft 建议的最佳做法。 添加到你的环境中的任何受支持的 Azure 资源将是并非, 将提供相应的建议。 Azure 顾问建议 categorised 四种最佳做法类别:

* 安全性
* 高可用性
* 性能
* 开销

Azure 顾问生成的安全建议构成了 Azure 安全中心提供的总体安全分析的一部分。

Azure 顾问收集的信息为管理员提供以下内容:

* 深入了解不符合建议的最佳做法的资源配置
* 有关要执行的特定更正操作的指南
* 指示应采取哪些补救措施作为高优先级的排名

### <a name="azure-policy"></a>Azure Policy

[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)提供应用管理 azure 资源类型及其允许配置的规则的功能。 策略可用于控制资源创建和配置, 也可用于审核环境中的配置设置。 这些审核结果可用于构成修正活动的基础。 Azure 策略不同于基于角色的访问控制 (RBAC);Azure 策略用于限制资源及其配置, RBAC 用于限制对 Azure 用户的特权访问。

是强制执行特定策略还是正在审核策略的影响, 会持续监视策略符合性, 并为管理员提供总体和特定于资源的符合性信息。 向 Azure 安全中心提供 azure 策略符合性数据, 构成安全分数的组成部分。

## <a name="key-design-considerations"></a>关键设计考虑因素

实现事件日志策略时, ACSC ISM 突出显示以下注意事项:

* 集中式日志记录设施
* 要记录的特定事件
* 事件日志保护
* 事件日志保留期
* 事件日志审核

除了收集和管理日志以外, ISM 还建议对组织的 IT 环境进行日常的漏洞评估。

### <a name="centralised-logging"></a>集中式日志记录

任何日志记录解决方案应尽可能将捕获的日志合并到单个数据存储库中。 这不仅降低了操作复杂性, 还阻止了创建多个数据仓库, 并使从多个源收集的数据并非在一起, 从而可以识别任何关联事件。 这对于检测和管理任何网络安全事件的作用域至关重要。

所有具有 Azure Monitor 的 Azure 客户都满足此要求。 此产品不仅为所有 Azure 资源在 Azure 中提供集中式日志记录存储库, 还可让你将数据流式传输到 Azure 事件中心。 Azure 事件中心提供完全托管的实时数据引入服务。 将数据传输到 Azure 事件中心 Azure Monitor 后, 还可以轻松地将数据连接到现有的受支持的安全信息和事件管理 (SIEM) 存储库和其他第三方监视工具。

Microsoft 还提供其自己的 Azure native SIEM 解决方案, 即 Azure Sentinel。 Azure Sentinel 支持各种数据连接器, 可用于监视整个企业中的安全事件。 通过将数据从受支持的[数据连接器](https://docs.microsoft.com/azure/sentinel/connect-data-sources)、Azure Sentinel 的内置机器学习和 Kusto 查询语言组合在一起, 可以通过单个解决方案来检测警报、威胁可见性和主动搜寻,与威胁响应。 Sentinel 还提供了一项搜寻和笔记本功能, 使安全管理员可以在 reuseable 操作手册中记录可在组织内部共享的所有步骤。 安全管理员甚至可以使用内置的[用户分析](https://docs.microsoft.com/azure/sentinel/user-analytics)来调查单个命名用户的操作。

### <a name="logged-events-and-log-detail"></a>记录的事件和日志详细信息

ISM 提供了应包含在任何日志记录策略中的事件日志类型的详细列表。 任何捕获的日志都必须包含充分的细节, 才能在进行分析和调查时进行任何实际使用。

在 Azure 中收集的日志属于以下三种类别之一:

* **控制和管理日志**:这些日志提供有关 Azure 资源管理器创建、更新和删除操作的信息。

* **数据平面日志**:其中包含作为 Azure 资源使用的一部分引发的事件。 包括 Windows 事件日志等源, 包括系统、安全和应用程序日志。

* 已**处理事件**:这些事件包含有关在 Azure 代表客户自动处理的事件和警报的信息。 处理事件的一个示例是 Azure 安全中心警报。

通过部署适用于 Windows 和 Linux 的虚拟机代理, Azure 虚拟机监视功能得到了增强。 这会显著增加收集的日志记录信息的广度。 可以将此代理的部署配置为通过 Azure 安全中心自动执行。

Microsoft 提供有关特定于 Azure 资源的日志及其[架构](https://docs.microsoft.com/azure/security/fundamentals/log-audit)的详细信息。

### <a name="log-retention-and-protection"></a>日志保留和保护

 必须安全地存储事件日志, 以确保所需的保留期。 ISM 建议将日志保留最少七年。 Azure 提供了多种方法来确保收集的日志的持续时间。 默认情况下, Azure 日志事件存储了90天。 Azure Monitor 捕获的日志数据可在 Azure 存储帐户上移动并存储, 以供长期保留。 存储在 Azure 存储帐户上的活动日志可保留一定的天数, 如果需要, 还可无限期保留。

用于存储 Azure 日志事件的 azure 存储帐户可以进行异地冗余, 并且可以使用 Azure 备份进行备份。 一旦 Azure 备份捕获后, 任何包含日志的备份删除操作都需要管理批准, 并保留14天的备份, 允许恢复。 Azure 备份允许受保护实例的9999副本, 提供了27年以上的每日备份。

应使用基于角色的访问控制来控制对用于 Azure 日志记录的资源的访问。 Azure Monitor、Azure 存储帐户和 Azure 备份应配置基于角色的访问控制, 以确保日志中包含的数据的安全。

### <a name="log-auditing"></a>日志审核

日志的真实值在 realised 后被并非。 使用自动化和手动分析, 并熟悉可用的工具, 可帮助你检测和管理违反组织安全策略和网络安全事件的行为。 Azure Monitor 提供了一组丰富的工具, 用于分析收集的日志。 然后, 可以在多个格式的系统、visualised 或传送之间共享此分析的结果。

存储在 Azure Monitor 中的日志数据保存在 Log Analytics 工作区中。 所有分析都以查询开始。 Azure Monitor 查询是用 Kusto 查询语言编写的。 查询构成了从 Azure 仪表板到警报规则的所有 Azure Monitor 输出的基础。

![Azure 日志查询概述](media/queries-overview.png)

可以通过使用监视解决方案来增强日志的审核。 这些是预打包的解决方案, 其中包含集合逻辑、查询和数据 visualisation 视图。 Microsoft 在 Azure Marketplace 中提供了许多监视解决方案和产品供应商[提供](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory)的其他解决方案。

### <a name="vulnerability-assessment-and-management"></a>漏洞评估和管理

ISM 指出, 例行漏洞评估和管理是必不可少的。 你的 IT 环境不断发展, 而外部安全威胁会不断变化。 通过 Azure 安全中心, 你可以执行自动漏洞评估, 并获得有关如何规划和执行修正活动的指导。

Azure 安全中心的安全评分提供了一个建议列表, 在应用这些建议后, 将提高环境的安全性。 列表按从最高到最低的整体安全分数的影响进行排序。 按影响对列表排序使你可以专注于最高优先级的建议, 以提高安全性。

Azure 策略还在持续漏洞评估中扮演着重要部分。 Azure 策略中提供的策略类型包括强制资源标记和值, 以限制可在其中创建资源的 Azure 区域, 同时阻止创建特定的资源类型。 可以将一组 Azure 策略分组到计划中。 计划用于应用相关的 Azure 策略, 在将其作为一个组一起应用时, 构成特定安全性或符合性目标的基础。

Azure 策略包含一个不断增长的内置策略定义库。 Azure 门户还提供了创作你自己的自定义 Azure 策略定义的选项。 找到现有库中的策略或创建新策略后, 可以将策略分配给 Azure 资源。 这些分配可以在资源管理层次结构中的不同级别[范围内](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)。 策略分配是继承的, 这意味着范围内的所有子资源都接收相同的策略分配。 还可以根据需要从作用域内策略分配中排除资源。

所有部署的 Azure 策略都有助于组织的安全分数。 在高度订购的环境中, 可以创建和部署自定义 Azure 策略定义, 以提供定制给特定工作负载的审核信息。

## <a name="getting-started"></a>开始使用

若要开始使用 Azure 安全中心并充分利用 Azure Monitor、顾问和策略, Microsoft 建议执行以下初始步骤:

* 启用 Azure 安全中心
* 升级到标准层
* 启用 Microsoft Monitoring Agent 自动预配到受支持的 Azure 虚拟机
* 在安全中心仪表板上查看、prioritise 和缓解安全建议和警报

## <a name="next-steps"></a>后续步骤

若要详细了解如何实现管理和控制 Azure 澳大利亚资源, 请参阅[Azure 策略和 Azure 蓝图](azure-policy.md), 以确保策略和法规符合性。
