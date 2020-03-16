---
title: Azure Monitor 文档中的新增内容
description: Azure Monitor 文档每月都有重大更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: b42acdf64612da6837bc67752f7a22169ddef7e2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673323"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor 文档中有哪些新增内容？
本文提供新的或已经过重大更新的 Azure Monitor 文章列表。 在每个月的第一周，本文将会刷新，以包含上个月的文章更新。

## <a name="march-2020"></a>2020 年 3 月

### <a name="agents"></a>代理
通过重新编写诊断扩展内容进行了多处更新。

- [Azure 监视代理概述](platform/agents-overview.md) - 重新构造了各个表格来更好地阐明每个代理的特有功能。
- [Azure 诊断扩展概述](platform/diagnostics-extension-overview.md) - 全部进行了重新编写。
- [在 Azure Monitor 中使用适用于 IIS 的 Blob 存储和适用于事件的表存储](platform/diagnostics-extension-logs.md) - 针对更新和澄清进行了常规重新编写。
- [安装并配置 Windows Azure 诊断扩展 (WAD)](platform/diagnostics-extension-windows-install.md) - 新文章。 
- [Windows 诊断扩展架构](platform/diagnostics-extension-schema-windows.md) - 已重新组织。
- [将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心](platform/diagnostics-extension-stream-event-hubs.md) - 全部进行了重新编写和更新。
- [在 Azure 存储中存储和查看诊断数据](platform/diagnostics-extension-to-storage.md) - 全部进行了重新编写和更新。
- [适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md) - 更好地阐明了与 Log Analytics 代理的关系。
- [适用于 Linux 的 Azure Monitor 虚拟机扩展](../virtual-machines/extensions/oms-linux.md) - 更好地阐明了与 Log Analytics 代理的关系。




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights 中的连接字符串](app/sdk-connection-string.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案

#### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
- [将 Azure Active Directory 与 Azure Kubernetes 服务集成](../aks/azure-ad-integration.md) - 添加了有关以下事项的说明：创建客户端应用程序来支持启用了 RBAC 的群集以支持将 Azure Monitor 用于容器。

#### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
- [用于 VM 的 Azure Monitor（正式版）常见问题解答](insights/vminsights-ga-release-faq.md) - 对性能数据的存储方式相关内容进行了更改。

#### <a name="office-365"></a>Office 365
- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更新了弃用日期。


### <a name="logs"></a>日志
- [优化 Azure Monitor 中的日志查询](log-query/query-optimization.md) - 新文章。
- [管理 Azure Monitor 日志的使用情况和成本](platform/manage-cost-storage.md) - 改进了示例查询来帮助你更好地了解使用情况。

### <a name="metrics"></a>指标
- [可通过诊断设置导出的 Azure Monitor 平台指标](platform/metrics-supported-export-diagnostic-settings.md) - 添加了有关针对 null 值和零值的行为更改的内容。


### <a name="visualizations"></a>可视化效果
增加了多篇新文章来提供视图设计器到工作簿转换指南。

- [Azure Monitor 视图设计器到工作簿转换指南](platform/view-designer-conversion-overview.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换选项](platform/view-designer-conversion-options.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿磁贴转换](platform/view-designer-conversion-tiles.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换摘要和访问](platform/view-designer-conversion-access.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换常见任务](platform/view-designer-conversion-tasks.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换示例](platform/view-designer-conversion-examples.md) - 新文章。




## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>常规
- [Azure Monitor 监视哪些内容？](monitor-reference.md) - 新文章。

### <a name="agents"></a>代理
- [使用 Azure Log Analytics 代理收集日志数据](platform/log-analytics-agent.md) - 更新了网络防火墙要求表。


### <a name="alerts"></a>警报
- [在 Azure 门户中创建和管理操作组](platform/action-groups.md) - 删除了不再需要的 v2 功能设置。
- [使用资源管理器模板创建指标警报](platform/alerts-metric-create-templates.md) - 添加了 *ignoreDataBefore* 参数的示例。  添加了有关多条件规则的约束。
- [使用 Log Analytics 警报 REST API](platform/api-alerts.md) - 更正了 JSON 示例。


### <a name="application-insights"></a>Application Insights
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了“可用性测试”部分，介绍如何使用 Azure 网络安全组添加入站端口规则以允许流量。
- [排查 Azure Application Insights Profiler 的问题](app/profiler-troubleshooting.md) - 更新了一般故障排除方法。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 根据客户的反馈更新并重新组织了该文章，以提高可读性。


### <a name="data-security"></a>数据安全
- [Azure Monitor 客户管理的密钥配置](platform/customer-managed-keys.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案

#### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
- [配置用于容器的 Azure Monitor 代理数据收集](insights/container-insights-agent-config.md) - 添加了有关在 Azure Red Hat OpenShift 上升级代理的详细信息，并添加了更多信息来区分代理的升级方法。
- [为用于容器的 Azure Monitor 创建性能警报](insights/container-insights-alerts.md) - 修订了信息，并更新了使用工作区上下文警报针对工作区中存储的性能数据创建警报的步骤。
- [使用用于容器的 Azure Monitor 监视 Kubernetes](insights/container-insights-analyze.md) - 更新了概述文章，以及有关 Windows Kubernetes 群集支持的分析文章。
- [使用用于容器的 Azure Monitor 配置 Azure Red Hat OpenShift 群集](insights/container-insights-azure-redhat-setup.md) - 添加了有关在 Azure Red Hat OpenShift 上升级代理的详细信息，并添加了更多信息来区分代理的升级方法。
- [使用用于容器的 Azure Monitor 配置混合 Kubernetes 群集](insights/container-insights-hybrid-setup.md) - 更新了此文章，以反映 Kubelet cAdvisor 中添加的对安全端口 10250 的支持。
- [如何管理用于容器的 Azure Monitor 代理](insights/container-insights-manage-agent.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。
- [配置用于容器的 Azure Monitor Prometheus 集成](insights/container-insights-prometheus-integration.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。
- [如何更新用于容器的 Azure Monitor 以启用指标](insights/container-insights-update-metrics.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。


#### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
- [用于 VM 的 Azure Monitor（正式版）常见问题解答](insights/vminsights-ga-release-faq.md) - 添加了有关将工作区和代理升级到新版本的信息。

#### <a name="office-365"></a>Office 365
- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 添加了有关在 Azure Sentinel 中迁移到 Office 365 解决方案的详细信息和常见问题解答。 删除了“加入”部分。



### <a name="logs"></a>日志
- [管理 Azure Monitor 中的 Log Analytics 工作区](platform/manage-access.md) - 对“非操作”做了更新。
- [管理 Azure Monitor 日志的用量和成本](platform/manage-cost-storage.md) - 在“定价模型”部分添加了有关数据量计算的阐释。
- [使用 Azure 资源管理器模板创建和配置 Log Analytics 工作区](platform/template-workspace-configuration.md) - 更新了模板，添加了新定价层。


### <a name="platform-logs"></a>平台日志
- [使用诊断设置收集 Azure 活动日志 - Azure Monitor](platform/diagnostic-settings-legacy.md) - 有关已更改的属性的附加信息。
- [导出 Azure 活动日志](platform/activity-log-export.md) - 根据 UI 变化更新了此文章。 





## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>代理
- [将 Linux 计算机连接到 Azure Monitor](platform/agent-linux.md) - 新文章。

### <a name="alerts"></a>警报
- [使用资源管理器模板创建指标警报](platform/alerts-metric-create-templates.md) - 添加了自定义指标的示例。
- [在 Azure Monitor 中创建具有动态阈值的警报](platform/alerts-dynamic-thresholds.md) - 添加了有关解释动态阈值图表的部分。
- [Azure 中的警报和通知监视概述](platform/alerts-overview.md) - 更新了 Resource Graph 查询。
- [Azure Monitor 中的指标警报支持的资源](platform/alerts-metric-near-real-time.md) - 对支持的指标和维度做了更新。
- [从旧的 Log Analytics 警报 API 切换到新的 Azure 警报 API](platform/alerts-log-api-switch.md) - 添加了有关已修改的警报名称的说明。
- [了解指标警报在 Azure Monitor 中的工作原理](platform/alerts-metric-overview.md) - 添加了支持用于大规模监视的资源类型。

### <a name="application-insights"></a>Application Insights
- [适用于辅助角色服务应用（非 HTTP 应用）的 Application Insights](app/worker-service.md) - 在 C# 代码中添加了默认的日志记录级别。 更新了包引用版本。
- [ApplicationInsights.config 参考 - Azure](app/configuration-with-applicationinsights-config.md) - 更新了示例代码。
- [使用 PowerShell 自动化 Azure Application Insights](app/powershell.md) - 对资源管理器模板做了更新。
- [Azure Monitor Application Insights NuGet 包](app/nuget.md) - 更新了包版本。
- [创建新的 Azure Application Insights 资源](app/create-new-resource.md) - 添加了全局唯一名称的说明。
- [使用实时指标流进行诊断 - Azure Application Insights](app/live-stream.md) - 更新了 ASP.NET Core SDK 版本要求。
- [Application Insights 中的事件计数器](app/eventcounters.md) - 更新了 customMetrics 的类别和表。
- [在 Azure Application Insights 中浏览 Java 跟踪日志](app/java-trace-logs.md) - 添加了 Java 代理日志记录阈值的配置。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了实时指标流的 IP 地址。
- [监视 Azure 应用服务性能](app/azure-web-apps.md) - 添加了对 ASP.NET Core 3.0 的支持。 
- [使用 Azure Monitor（预览版）监视 Python 应用程序](app/opencensus-python.md) - 添加了有关将 OpenCensus Python 架构映射到 Azure Monitor 架构的阐释。
- [Azure Application Insights 发行说明](app/release-notes.md) - 添加了有关旧版本的说明。
- [Azure Application Insights 中的遥测通道](app/telemetry-channels.md) - 更新了在长时间断开连接时丢失数据的持续时间。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 更正了自定义 TelemetryInitializer 的代码片段。
- [在 Java Web 项目中排查 Application Insights 问题](app/java-troubleshoot.md) - 删除了有关 JDK 9 中不支持依赖项收集的陈述。

### <a name="insights-and-solutions"></a>见解和解决方案
- [用于容器的 Azure Monitor 常见问题解答](insights/container-insights-faq.md) - 添加了有关“映像”和“名称”字段的问题。
- [Azure Monitor 中的 Azure SQL Analytics 解决方案](insights/azure-sql.md) - 更新了数据库等待托管实例支持。
- [配置用于容器的 Azure Monitor 代理数据收集](insights/container-insights-agent-config.md) - 添加了 enrich_container_logs 的设置。
- [使用用于容器的 Azure Monitor 配置混合 Kubernetes 群集](insights/container-insights-hybrid-setup.md) - 添加了故障排除部分。
- [使用 Azure Monitor 监视 Active Directory 复制状态](insights/ad-replication-status.md) - 更新了.NET Framework 先决条件。
- [Azure 中的网络性能监视器解决方案](insights/network-performance-monitor.md) - 添加了支持的区域。
- [使用 Azure Monitor 优化 Active Directory 环境](insights/ad-assessment.md) - 更新了 .NET Framework 先决条件。
- [使用 Azure Monitor 优化 SQL Server 环境](insights/sql-assessment.md) - 更新了 .NET Framework 先决条件。
- [使用 Azure Log Analytics 优化 System Center Operations Manager 环境](insights/scom-assessment.md) - 更新了 .NET Framework 先决条件。
- [Azure Log Analytics 中支持使用 IT 服务管理连接器建立的连接](platform/itsmc-connections.md) - 将 New York 添加到了必备的客户端 ID 和客户端机密。

### <a name="logs"></a>日志
- [删除和恢复 Azure Log Analytics 工作区](platform/delete-workspace.md) - 添加了 PowerShell 方法。
- [设计 Azure Monitor 日志部署](platform/design-logs-deployment.md) - 提高了工作区的引入速率。

### <a name="metrics"></a>指标
- [可通过诊断设置导出的 Azure Monitor 平台指标](platform/metrics-supported-export-diagnostic-settings.md) - 新文章。

### <a name="platform-logs"></a>平台日志
在根据用于通过诊断设置配置活动日志的新功能重新组织平台日志内容的过程中，我们已更新多篇文章。

- [将 Azure 资源日志存档到存储帐户](platform/resource-logs-collect-storage.md)
- [Azure 活动日志事件架构](platform/activity-log-schema.md)
- [Azure Monitor 服务限制](service-limits.md)
- [收集和分析 Log Analytics 工作区中的 Azure 活动日志](platform/activity-log-collect.md)
- [使用诊断设置（预览版）收集 Azure 活动日志 - Azure Monitor](platform/diagnostic-settings-legacy.md)
- [跨 Azure 租户将 Azure 活动日志收集到 Log Analytics 工作区中](platform/activity-log-collect-tenants.md)
- [收集 Log Analytics 工作区中的 Azure 资源日志](platform/resource-logs-collect-workspace.md)
- [使用资源管理器模板在 Azure 中创建诊断设置](platform/diagnostic-settings-template.md)
- [创建诊断设置以收集 Azure 中的日志和指标](platform/diagnostic-settings.md)
- [导出 Azure 活动日志](platform/activity-log-export.md)
- [Azure 平台日志概述](platform/platform-logs-overview.md)
- [将 Azure 监视数据流式传输到事件中心](platform/stream-monitoring-data-event-hubs.md)
- [将 Azure 平台日志流式传输到事件中心](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>快速入门和教程

- [在 Azure Monitor 中创建指标图表](learn/tutorial-metrics-explorer.md) - 新文章。
- [从 Azure 资源收集资源日志并使用 Azure Monitor 进行分析](learn/tutorial-resource-logs.md) - 新文章。
- [使用 Azure Monitor 监视 Azure 资源](learn/quick-monitor-azure-resource.md) - 新文章。
   
## <a name="next-steps"></a>后续步骤

- 若要贡献 Azure Monitor 文档，请参阅[文档供稿人指南](https://docs.microsoft.com/contribute/)。