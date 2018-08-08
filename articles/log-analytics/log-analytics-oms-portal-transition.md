---
title: OMS 门户即将转移到 Azure | Microsoft Docs
description: OMS 门户即将被淘汰，其所有功能将转移到 Azure 门户。 本文提供有关此项过渡的详细信息。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1a8ccc818cafac4867cb533c83f297af61a21836
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309096"
---
# <a name="oms-portal-moving-to-azure"></a>OMS 门户即将转移到 Azure
Log Analytics 客户不断地反映，他们需要通过单个用户体验来监视和管理本地与 Azure 工作负荷。 你们也许知道，Azure 门户是所有 Azure 服务的中心，提供丰富的管理体验和功能，例如，为固定资源提供仪表板、为查找资源提供智能搜索，以及为资源管理提供标记。 用于整合和简化监视与管理工作流，我们已开始将 OMS 门户功能添加到 Azure 门户。 我们很高兴地宣布，OMS 门户的大部分功能现已并入 Azure 门户。 事实上，某些新功能（例如流量分析）只在 Azure 门户中提供。 两个门户的功能差距不大，有几个解决方案仍在转移到 Azure 门户。 如果你不使用这些功能，使用 Azure 门户就能完成在 OMS 门户中所能完成的任何工作，而且 Azure 门户的功能更强大。 如果你尚未使用 Azure 门户，我们建议立即开始使用！ 

我们预计在 2018 年 8 月之前弥补两个门户之间的剩余差距。 根据客户的反馈，我们会告知淘汰 OMS 门户的时间表。 我们很高兴能够转移到 Azure 门户，并且希望实现轻松过渡。 但我们也知道，变化会带来一定的困扰。 欢迎向 **LAUpgradeFeedback@microsoft.com** 发送任何问题、反馈或忧虑。 本文的剩余部分介绍此项过渡的关键场景、当前差距和路线图。 

## <a name="progress"></a>Progress
下面是自本文先前版本发布以来已完成的更新。

### <a name="july-27"></a>7 月 27 日

- 现在，[DNS Analytics](log-analytics-dns.md) 可在 Azure 门户中完全正常运行。
- [更新管理](../automation/automation-update-management.md)已更新，可在 Azure 门户中完全正常运行。 有关详细信息，请参阅[将 OMS 更新部署迁移到 Azure](../automation/migrate-oms-update-deployments.md)。
- 现在，[警报](#changes-to-alerts)已完全扩展到 Azure 门户中。
- 现在，会为所有工作区自动启用[自定义日志预览版功能](log-analytics-data-sources-custom-logs.md)。

## <a name="what-will-change"></a>会发生哪些变化？ 
随着 OMS 门户的弃用，我们将宣布以下变化。 其中的每项变化将在下面的相应部分做出详细描述。

- 只能在 Azure 门户中创建新工作区。
- 新警报管理体验将取代警报管理解决方案。
- 将使用 Azure 基于角色的访问控制在 Azure 门户中进行用户访问管理。
- 不再需要 Application Insights 连接器，因为可通过跨工作区查询启用相同的功能。
- 将弃用 OMS 移动应用。 
- NSG 解决方案将被取代为通过流量分析解决方案提供的增强功能。
- 从 System Center Operations Manager 到 Log Analytics 的新连接需要更新的管理包。


## <a name="current-known-gaps"></a>当前已知差距 
目前存在的一些功能差距使得我们仍需保持使用 OMS 门户。 这些差距即将得到弥补，到时会相应地更新本文档。 有关扩展和更改的现行通告，另请参阅 [Azure 更新](https://azure.microsoft.com/updates/?product=log-analytics)。

- 以下解决方案目前不能在 Azure 门户中完全正常运行。 在这些解决方案得到更新之前，请继续在经典门户中使用它们。

    - Windows 分析解决方案（[升级就绪情况](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started)、[设备健康状况](https://docs.microsoft.com/windows/deployment/update/device-health-monitor)和[更新符合性](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started)）
    - [Surface Hub](log-analytics-surface-hubs.md)

-  若要允许用户访问 Azure 中的 Log Analytics 资源，必须通过 [Azure 基于角色的访问](#user-access-and-role-migration)向用户授予访问权限。


## <a name="what-should-i-do-now"></a>现在应该做些什么？  
请参阅 [Log Analytics 用户从 OMS 门户过渡到 Azure 门户时的常见问题](../log-analytics/log-analytics-oms-portal-faq.md)，了解有关如何过渡到 Azure 门户的信息。 如果[上述差距](#current-known-gaps)不适用于你的环境，应考虑开始使用 Azure 门户作为主要体验。 欢迎向 LAUpgradeFeedback@microsoft.com 发送任何反馈、问题或忧虑。

## <a name="new-workspaces"></a>新工作区
从 7 月 29 日开始，不再能够使用 OMS 门户创建新工作区。 请遵照[在 Azure 门户中创建 Log Analytics 工作区](log-analytics-quick-create-workspace.md)中的指导，在 Azure 门户中创建新工作区。

## <a name="changes-to-alerts"></a>警报更改 

### <a name="alert-extension"></a>警报扩展  

> [!NOTE]
> 现在，警报已完全扩展到 Azure 门户中。 可在 OMS 门户中查看现有警报规则，但只能在 Azure 门户中对其进行管理。

警报正在[扩展到 Azure 门户](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 完成此过渡后，只能在 Azure 门户中针对警报执行管理操作。 现有的警报将继续列在 OMS 门户中。 如果使用 Log Analytics 警报 REST API 或 Log Analytics 警报资源模板以编程方式访问警报，需要在 API 调用、Azure 资源管理器模板和 PowerShell 命令中使用操作组而非操作。

### <a name="alert-management-solution"></a>警报管理解决方案
无需使用[警报管理解决方案](log-analytics-solution-alert-management.md)，可以使用 [Azure Monitor 的统一警报界面](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)来可视化和管理警报。 此新体验聚合来自 Azure 中多个源的警报，包括来自 Log Analytics 的日志警报。 可以查看警报的分布情况，通过智能组利用相关警报的自动分组，并在应用丰富的筛选器时查看多个订阅的警报。 在 2018 年 6 月 4 日开始发布的预览版中已提供所有这些功能。 警报管理解决方案不会在 Azure 门户中提供。 

警报管理解决方案收集的数据（具有某种警报类型的记录）继续在 Log Analytics 中提供，前提是为工作区安装了该解决方案。 从 2018 年 8 月开始，会实现将警报从统一警报体验流式传输到工作区，到时会取代上述功能。 预期会发生一些架构更改，日后会发布相关通告。

## <a name="user-access-and-role-migration"></a>用户访问权限和角色迁移
与 OMS 门户中的访问权限管理相比，Azure 门户的访问权限管理功能是更丰富且更强大，但确实需要完成某些转换。 有关 Log Analytics 中访问权限管理的详细信息，请参阅[管理工作区](log-analytics-manage-access.md#manage-accounts-and-users)。

从 7 月 30 日开始，将访问控制权限从 OMS 门户自动转换为 Azure 门户权限。 转换完成后，OMS 门户用户管理部分会将用户路由到 Azure 中的访问控制 (IAM)。 

在转换期间，系统将检查在 OMS 门户中拥有权限的每个用户或安全组，并确定该用户是否在 Azure 中拥有相同的级别或权限。 如果缺少权限，系统将为相关的工作区和解决方案分配以下角色。

| OMS 门户权限 | Azure 角色 |
|:---|:---|
| ReadOnly | Log Analytics 读者 |
| 参与者 | Log Analytics 参与者 |
| 管理员 | 所有者 |

为了确保不会向用户过度分配权限，系统不会在资源组级别自动分配这些权限。 因此，工作区管理员必须在资源组或订阅级别手动为自己分配所有者或参与者角色才能执行以下操作。

- 添加或删除解决方案
- 定义新的自定义视图
- 管理警报 

在某些情况下，自动转换无法应用权限，并提示管理员手动分配权限。

## <a name="oms-mobile-app"></a>OMS 移动应用
OMS 移动应用将随 OMS 门户一起淘汰。 无需使用 OMS 移动应用来访问有关 IT 基础结构、仪表板和保存的查询的信息，可以直接通过移动设备中的浏览器访问 Azure 门户。 若要获取警报，应配置 [Azure 操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)来接收短信或语音呼叫形式的通知

## <a name="application-insights-connector-and-solution"></a>Application Insights 连接器和解决方案
使用 [Application Insights 连接器](log-analytics-app-insights-connector.md)可将 Application Insights 数据引入 Log Analytics 工作区。 需要进行这种数据复制才能实现跨基础结构和应用程序数据的可见性。

由于支持[跨资源查询](log-analytics-cross-workspace-search.md)，因此不再需要使用这种方法来复制数据。 在这种情况下，现有的 Application Insights 解决方案将被弃用。 从 7 月开始，无法将新的 Application Insights 资源链接到 Log Analytics 工作区。 在 2018 年 11 月之前，现有的链接和仪表板将继续正常运行。


## <a name="azure-network-security-group-analytics"></a>Azure 网络安全组分析
[Azure 网络安全组分析解决方案](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics)将被最近推出的[流量分析](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)取代。在流量分析中可以洞察云网络上的用户和应用程序活动。 流量分析可帮助你审核组织的网络活动、保护应用程序和数据、优化工作负荷性能和保持符合性。 

此解决方案分析 NSG 流日志，并提供以下项目的见解。

- Azure 和 Internet、公有云区域、VNET 和子网之间的网络流量流。
- 网络上的应用程序和协议，而无需探测器或专用流收集设备。
- 最活跃的通信方、聊天应用程序、云中的 VM 对话、流量热点。
- VNET 上的流量源和流量目标、关键业务服务和应用程序之间的相互关系。
- 安全性，包括恶意流量、向 Internet 开放的端口、尝试访问 Internet 的应用程序或 VM。
- 容量利用率，帮助消除过度预配或利用不足的问题。

可以继续依赖使用诊断设置将 NSG 日志发送到 Log Analytics，使现有的已保存搜索、警报和仪表板可继续工作。 在收到进一步的通告之前，已安装该解决方案的客户可以继续使用它。 从 8 月 15 日开始，“网络安全组分析”解决方案将从市场中删除，并通过社区以 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)的形式提供。

## <a name="system-center-operations-manager"></a>System Center Operations Manager
如果已[将 Operations Manager 管理组连接到 Log Analytics](log-analytics-om-agents.md)，该管理组可继续工作，而无需进行任何更改。 不过，对于新连接，必须遵照[使用 Microsoft System Center Operations Manager 管理包来配置 Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/) 中的指导。

## <a name="next-steps"></a>后续步骤
- 有关从 OMS 门户转移到 Azure 门户的指导，请参阅 [Log Analytics 用户从 OMS 门户过渡到 Azure 门户时的常见问题](log-analytics-oms-portal-faq.md)。