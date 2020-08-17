---
title: Azure Monitor 文档中的新增内容
description: Azure Monitor 文档每月都有重大更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: 10d4cb8160d0bf9111903b193574e9e820ded221
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827735"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor 文档中有哪些新增内容？

本文提供新的或已经过重大更新的 Azure Monitor 文章列表。 在每个月的第一周，本文将会刷新，以包含上个月的文章更新。

## <a name="july-2020"></a>2020 年 7 月

### <a name="general"></a>常规
- [部署 Azure Monitor](deploy-scale.md) - 用于 VM 的 Azure Monitor 加入内容的重构。
- [使用 Azure 专用链接将网络安全地连接到 Azure Monitor](platform/private-link-security.md) - 增加了有关限制的部分。

### <a name="alerts"></a>警报
- [Azure Monitor 警报的操作规则](platform/alerts-action-rules.md) - 增加了 CLI 进程。
- [在 Azure 门户中创建和管理操作组](platform/action-groups.md) - 已更新以反映 UI 中的更改。
- [在 Azure Monitor Log Analytics 中保存的查询](log-query/saved-queries.md) - 新文章。
- [排除 Azure Monitor 中的日志警报故障](platform/alerts-troubleshoot-log.md) - 增加了有关警报规则配额的部分。
- [排除 Azure 指标警报故障](platform/alerts-troubleshoot-metric.md) - 增加了有关尚未发出的自定义指标的警报规则部分。
- [了解指标警报在 Azure Monitor 中的工作原理](platform/alerts-metric-overview.md) - 添加了有关选择聚合粒度的建议。

### <a name="application-insights"></a>Application Insights
- [Azure Web 应用扩展的发行说明 - Application Insights](app/web-app-extension-release-notes.md) - 新文章。
- [Application Insights 资源的资源管理器模板示例](samples/resource-manager-app-resource.md) - 新文章。
- [排除 Azure Application Insights Profiler 的问题](app/profiler-troubleshooting.md) - 增加了有关在 Azure 应用服务上运行 ASP.NET Core 应用探查器时出现的 bug 的注释。 

### <a name="containers"></a>容器
- [Azure Monitor 中的容器日志警报](insights/container-insights-log-alerts.md) - 新文章。
- [Azure Monitor 中的容器指标警报](insights/container-insights-metric-alerts.md) - 新文章。

### <a name="logs"></a>日志
- [Azure Monitor 客户托管的密钥](platform/customer-managed-keys.md) - 增加了错误消息和用于查询的 CMK 配置部分。
- [Azure Monitor HTTP 数据收集器 API](platform/data-collector-api.md) - 增加了 Python 3 示例。
- [在 Azure Monitor 中优化日志查询](log-query/query-optimization.md) - 增加了有关在使用子查询时避免多次数据扫描的部分。
- [教程：Log Analytics 查询入门](log-query/get-started-portal.md) - 增加了视频。

### <a name="platform-logs"></a>平台日志
- [创建诊断设置以将平台日志和指标发送到不同目标](platform/diagnostic-settings.md) - 增加了视频。
- [Azure Monitor 的资源管理器模板示例](samples/resource-manager-samples.md) - 增加了使用日志目标类型的 ARM 示例。 

### <a name="solutions"></a>解决方案
- [Azure Monitor 中的监视解决方案](insights/solutions.md) - 增加了 CLI 进程。
- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更改了停用日期。

### <a name="virtual-machines"></a>虚拟机

用于 VM 的 Azure Monitor 内容重构的新文章和更新的文章

- [什么是用于 VM 的 Azure Monitor？](insights/vminsights-overview.md)
- [为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](insights/vminsights-configure-workspace.md)
- [将 Linux 计算机连接到 Azure Monitor](platform/agent-linux.md)
- [为混合环境启用 Azure Monitor](insights/vminsights-enable-hybrid.md)
- [在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor](insights/vminsights-enable-portal.md)
- [使用 Azure Policy 启用用于 VM 的 Azure Monitor](insights/vminsights-enable-at-scale-policy.md)
- [启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md)
- [使用 PowerShell 启用用于 VM 的 Azure Monitor](insights/vminsights-enable-powershell.md)
- [使用资源管理器模板启用用于 VM 的 Azure Monitor](insights/vminsights-enable-resource-manager.md)
- [使用 PowerShell 或模板启用用于 VM 的 Azure Monitor](insights/vminsights-enable-at-scale-powershell.md)


### <a name="visualizations"></a>可视化效果
- [升级 Log Analytics 仪表板可视化效果](log-query/dashboard-upgrade.md) - 已更新刷新频率。
- [可视化 Azure Monitor 中的数据](visualizations.md) - 增加了视频。


## <a name="june-2020"></a>2020 年 6 月

### <a name="general"></a>常规
- [部署 Azure Monitor](deploy-scale.md) - 新文章。
- [Azure Monitor 客户管理的密钥](platform/customer-managed-keys.md) - 更新的 billingtype 属性。 添加了 PowerShell 命令。

### <a name="agents"></a>代理
- [Log Analytics 代理概述](platform/log-analytics-agent.md) - 添加了 Python 2 要求。

### <a name="alerts"></a>警报
- [如何在警报规则或操作规则的目标资源移动到其他 Azure 区域时进行更新](platform/alerts-resource-move.md) - 新文章。
- [排查 Azure 指标警报问题](platform/alerts-troubleshoot-metric.md) - 新文章。
- [在 Azure Monitor 中排查日志警报问题](platform/alerts-troubleshoot-metric.md) - 新文章。
  
### <a name="application-insights"></a>Application Insights
- [适用于 JavaScript Web 应用的 Azure Application Insights](app/javascript.md) - JavaScript SDK 更新部分。 更新了代码段以报告加载失败。
- [为探查器和 Snapshot Debugger 配置 BYOS（自带存储）](app/profiler-bring-your-own-storage.md) - 新文章。
- [使用 OpenCensus Python 在 Azure Application Insights 中跟踪导入请求](app/opencensus-python-request.md) - 更新了 OpenCensus 的日志记录和配置。
- [使用 Azure Application Insights 监视实时 ASP.NET Web 应用](app/monitor-performance-live-website-now.md) - 更新了 Application Insights 状态监视器 v1 的删除日期。
- [使用 Application Insights 监视 Node.js 服务](app/nodejs.md) - 包括从早期版本和 SDK 配置迁移的多个更新
- [使用 Azure Monitor 监视 Python 应用程序（预览版）](app/opencensus-python.md) - 添加了有关配置 Azure Monitor 导出程序的部分。
- [在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能](app/codeless-overview.md) - 新文章。
- [对 JavaScript Web 应用的 SDK 加载失败进行故障排查](app/javascript-sdk-load-failure.md) - 新文章。

### <a name="containers"></a>容器
- [如何停止监视混合 Kubernetes 群集](insights/container-insights-optout-hybrid.md) - 添加了有关已启用 Arc 的 Kubernetes 的部分。
- [使用用于容器的 Azure Monitor 配置已启用 Azure Arc 的 Kubernetes 群集](insights/container-insights-enable-arc-enabled-clusters.md) - 新文章。
- [使用用于容器的 Azure Monitor 配置 Azure Red Hat OpenShift v4.x](insights/container-insights-azure-redhat4-setup.md) - 已更新先决条件。
- [为容器实时数据设置 Azure Monitor（预览版）](insights/container-insights-livedata-setup.md) - 删除了关于 Azure 美国政府不提供此功能的说明。

### <a name="insights"></a>洞察力
- [常见问题解答 - Azure 中的网络性能监视器解决方案](insights/network-performance-monitor-faq.md) - 添加了 ExpressRoute 监视器的常见问题解答。

### <a name="logs"></a>日志
- [删除和恢复 Azure Log Analytics 工作区](platform/delete-workspace.md) - 添加了 PowerShell 命令。 更新了疑难解答。
- [在 Azure Monitor 中管理 Log Analytics 工作区](platform/manage-access.md) - 在 RBAC 部分添加了不允许的表的示例。
- [管理 Azure Monitor 日志的使用情况和成本](platform/manage-cost-storage.md) - 有关数据大小计算的其他详细信息。 更新了数据卷警报的配置。 有关 Azure Sentinel 收集的安全数据的详细信息。 数据上限的说明。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](platform/logicapp-flow-connector.md) - 添加了连接器限制。

### <a name="metrics"></a>指标
- [Azure Monitor 按资源类型支持的指标](platform/metrics-supported.md) - 更新了 SQL Server 指标。


### <a name="platform-logs"></a>平台日志

- [诊断设置的资源管理器模板示例](samples/resource-manager-diagnostic-settings.md) - 对活动日志诊断设置的修复。
- [使用 Azure 门户将 Azure 活动日志发送到 Log Analytics 工作区](learn/quick-collect-activity-log-portal.md) - 新文章。
- [使用 Azure 资源管理器模板将 Azure 活动日志发送到 Log Analytics 工作区](learn/quick-collect-activity-log-arm.md) - 新文章。

来自平台日志内容重构和整合的新文章和更新文章

- [将 Azure 资源日志存档到存储帐户](./platform/resource-logs.md#send-to-azure-storage)
- [Azure 活动日志事件架构](platform/activity-log-schema.md)
- [Azure 活动日志](platform/activity-log.md)
- [Azure Monitor CLI 示例](samples/cli-samples.md)
- [Azure Monitor PowerShell 示例](samples/powershell-samples.md)
- [Azure 监视 REST API 演练](platform/rest-api-walkthrough.md)
- [Azure 资源日志支持的服务和架构](./platform/resource-logs-schema.md)
- [Azure 资源日志](platform/resource-logs.md)
- [收集和分析 Azure Monitor 中的 Azure 活动日志](./platform/activity-log.md)
- [收集 Log Analytics 工作区中的 Azure 资源日志](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [创建诊断设置以将平台日志和指标发送到不同目标](platform/diagnostic-settings.md)
- [导出 Azure 活动日志](./platform/activity-log.md#legacy-collection-methods)
- [Azure 平台日志概述](platform/platform-logs-overview.md)
- [将 Azure 平台日志流式传输到事件中心](./platform/resource-logs.md#send-to-azure-event-hubs)
- [在 Azure Monitor 中查看 Azure 活动日志事件](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>虚拟机
- [在 Azure 门户中启用用于 VM 的 Azure Monitor](./insights/vminsights-enable-portal.md) - 更新为包含 Azure Arc。
- [启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md) - 更新为包含 Azure Arc。
- [什么是用于 VM 的 Azure Monitor？](insights/vminsights-overview.md) - 更新为包含 Azure Arc。


### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿数据源](platform/workbooks-data-sources.md) - 添加了“警报和自定义终结点”部分。
- [对 Azure Monitor 基于工作簿的见解进行故障排查](insights/troubleshoot-workbooks.md) - 新文章。
- [升级 Log Analytics 仪表板可视化效果](log-query/dashboard-upgrade.md) - 新文章。



## <a name="may-2020"></a>2020 年 5 月

### <a name="general"></a>常规

- [Azure Monitor常见问题解答](faq.md) - 添加了指标部分。
- [Azure Monitor 客户托管密钥](platform/customer-managed-keys.md) - 准备正式发布的各种更改。
- [Azure Monitor 的内置策略定义](samples/policy-samples.md) - 新文章。
- [客户拥有的用于日志引入的存储帐户](platform/private-storage.md) - 新文章。
- [管理 Azure Monitor 日志的使用情况和成本](platform/manage-cost-storage.md) - 添加了群集按比例计费。
- [使用 Azure 专用链接将网络安全地连接到 Azure Monitor](platform/private-link-security.md) - 新文章。


#### <a name="new-resource-manager-template-samples"></a>新的资源管理器模板示例 
- [用于 Azure Monitor 的资源管理器模板示例](samples/resource-manager-samples.md)
- [用于操作组的资源管理器模板示例](samples/resource-manager-action-groups.md)
- [用于代理的资源管理器模板示例](samples/resource-manager-agent.md)
- [用于容器的 Azure Monitor 的资源管理器模板示例](samples/resource-manager-container-insights.md)
- [用于 VM 的 Azure Monitor 的资源管理器模板示例](samples/resource-manager-vminsights.md)
- [用于诊断设置的资源管理器模板示例](samples/resource-manager-diagnostic-settings.md)
- [用于 Log Analytics 工作区的资源管理器模板示例](samples/resource-manager-workspace.md)
- [用于日志查询的资源管理器模板示例](samples/resource-manager-log-queries.md)
- [用于日志查询警报规则的资源管理器模板示例](samples/resource-manager-alerts-log.md)
- [用于指标警报规则的资源管理器模板示例](samples/resource-manager-alerts-metric.md)
- [用于工作簿的资源管理器模板示例](samples/resource-manager-workbooks.md)

### <a name="agents"></a>代理
- [安装并配置 Windows Azure 诊断扩展 (WAD)](platform/diagnostics-extension-windows-install.md) - 添加了有关配置诊断的详细信息。
- [Log Analytics 代理概述](platform/log-analytics-agent.md) - 添加了受支持的 Linux 版本。

### <a name="application-insights"></a>Application Insights

- [使用 Application Insights 监视 Azure Functions 上运行的应用程序 - Azure Monitor](app/monitor-functions.md) - 新文章。
- [使用 Azure Application Insights 监视 Node.js 服务](app/nodejs.md) - 常规更新，包括有关从以前版本迁移的新部分。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 添加了 Webhook 和美国政府的 IP 地址。
- [使用 Application Insights 监视 Azure Kubernetes 服务 (AKS) 上的应用程序 - Azure Monitor](app/kubernetes-codeless.md) - 新文章。
- [排除无数据问题 - 用于 .NET 的 Application Insights](app/asp-net-troubleshoot-no-data.md) - 添加了有关使用 dotnet-trace 收集日志的部分。
- [使用 Azure Monitor 中的应用程序更改分析查找 Web 应用问题](app/change-analysis.md) -“更改分析”功能中的多个更新。

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>基于工作区的应用程序预览版的新的和更新的文章
- [基于工作区的 Azure Monitor Application Insights 资源架构](app/apm-tables.md)
- [新建基于工作区的 Azure Monitor Application Insights 资源](app/create-workspace-resource.md)
- [Azure Monitor 日志查询中的 app() 表达式](log-query/app-expression.md)
- [Azure Monitor Log Analytics 中的日志查询范围](log-query/scope.md)
- [使用 Azure Monitor 跨资源进行查询](log-query/cross-workspace-query.md)
- [Azure Monitor 日志记录中的标准属性](platform/log-standard-properties.md)
- [Azure Monitor 日志的结构](log-query/logs-structure.md)





### <a name="containers"></a>容器
- [如何启用用于容器的 Azure Monitor](insights/container-insights-onboard.md) - 更新了防火墙配置表。
- [如何为指标更新用于容器的 Azure Monitor](insights/container-insights-update-metrics.md) - 使用托管标识收集指标的更新。
- [用于容器的 Azure Monitor 的监视成本](insights/container-insights-cost.md) - 新文章。
- [设置用于容器实时数据（预览版）的 Azure Monitor](insights/container-insights-livedata-setup.md) - 支持新的群集角色绑定。

### <a name="insights"></a>洞察力
- [适用于 Azure Cache for Redis（预览版）的 Azure Monitor](insights/redis-cache-insights-overview.md) - 新文章。
- [使用 Azure Monitor for Key Vault（预览版）监视 Key Vault](./insights/key-vault-insights-overview.md) - 新文章。

### <a name="logs"></a>日志
- [使用 PowerShell 创建和配置 Log Analytics](platform/powershell-workspace-configuration.md) - 添加了故障排除部分。
- [在 Azure 门户中创建 Log Analytics 工作区](learn/quick-create-workspace.md) - 添加了故障排除部分。
- [使用 Azure CLI 创建 Log Analytics 工作区](learn/quick-create-workspace-cli.md) - 添加了故障排除部分。
- [删除和恢复 Azure Log Analytics 工作区](platform/delete-workspace.md) - 更新了有关恢复已删除工作区的信息。
- [Azure Monitor 日志查询中的函数](log-query/functions.md) - 删除了有关不包含其他函数的函数的注释。
- [Azure Monitor 日志的结构](log-query/logs-structure.md) - 阐明了 Application Insights 表的属性说明。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](platform/logicapp-flow-connector.md) - 添加了限制部分。
- [使用 PowerShell 创建和配置 Log Analytics 工作区](platform/powershell-workspace-configuration.md) - 添加了故障排除部分。


### <a name="metrics"></a>指标
- [Azure Monitor 按资源类型支持的指标](platform/metrics-supported.md) - 阐明了来宾指标和指标路由。 

### <a name="solutions"></a>解决方案
- [使用 Azure Monitor 优化 Active Directory 环境](insights/ad-assessment.md) - 向支持的版本中添加了 Windows Server 2019。
- [使用 Azure Monitor 优化 SQL Server 环境](insights/sql-assessment.md) - 添加到了支持的 SQL Server 版本。


### <a name="virtual-machines"></a>虚拟机
- [启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md) - 添加到了支持的 Ubuntu Server 版本。 为 Log Analytics 工作区添加了支持的区域。
- [如何使用用于 VM 的 Azure Monitor 绘制性能图表](insights/vminsights-performance.md) - 为不可用的指标添加了限制部分。

### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿和 Azure 资源管理器模板](platform/workbooks-automate.md) - 添加了用于部署工作簿模板的资源管理器更新。
- [Azure Monitor 工作簿组](platform/workbooks-groups.md) - 新文章。
- [Azure Monitor 工作簿 - 使用 JSONPath 转换 JSON 数据](platform/workbooks-jsonpath.md) - 新文章。


## <a name="april-2020"></a>2020 年 4 月

### <a name="general"></a>常规

- [Azure Monitor 客户托管密钥](platform/customer-managed-keys.md) - 添加了关于异步操作的部分
- [在 Azure Monitor 中管理 Log Analytics 工作区](platform/manage-access.md) - 更新了自定义日志部分。

### <a name="alerts"></a>警报

- [Azure Monitor 警报的操作规则](platform/alerts-action-rules.md) - 添加了视频。
- [Azure 中的警报和通知监视概述](platform/alerts-overview.md) - 添加了视频。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights 中的应用程序映射](app/app-map.md) - 为 Java 代理添加了云角色名称配置。
- [Azure Application Insights .NET 代理 API 参考](app/status-monitor-v2-api-reference.md) - 合并了 API 参考。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了 App Insights 和 Log Analytics API、操作组 Webhook、Azure 美国政府版的 IP 地址。
- [在任何位置监视 Java 应用程序](app/java-standalone-config.md) - 新文章。
- [在任何环境中监视 Java 应用程序](app/java-in-process-agent.md) - 新文章。
- [监视在任何环境中运行的 Java 应用程序](app/java-standalone-arguments.md) - 新文章。
- [监视本地运行的 Java 应用程序](app/java-on-premises.md) - 新文章。
- [在 Visual Studio 中删除 Application Insights](app/remove-application-insights.md) - 新文章。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 修复了 Python 中的固定速率采样。

### <a name="containers"></a>容器

- [使用用于容器的 Azure Monitor 配置 Azure Red Hat OpenShift v4.x](insights/container-insights-azure-redhat4-setup.md) - 新文章。
- [如何手动修复 ServiceNow 同步问题](platform/itsmc-resync-servicenow.md) - 新文章。
- [如何停止监视 Azure 和 Red Hat OpenShift v4 群集](insights/container-insights-optout-openshift-v4.md) - 新文章。
- [如何停止监视 Azure Red Hat OpenShift v3 群集](insights/container-insights-optout-openshift-v3.md) - 新文章。
- [如何停止监视混合 Kubernetes 群集](insights/container-insights-optout-hybrid.md) - 新文章。

### <a name="insights"></a>洞察力

- [使用用于 Key Vault 的 Azure Monitor（预览版）监视 Azure Key Vault](insights/key-vault-insights-overview.md) - 新文章。

### <a name="logs"></a>日志

- [Azure Monitor 服务限制](service-limits.md) - 添加了用户查询限制。
- [管理 Azure Monitor 日志的使用情况和成本](platform/manage-cost-storage.md) - 为日志群集添加了计费。 添加了 Kusto 查询，使那些使用旧版“按节点”定价层的客户能够确定是移到“按 GB”定价层还是“产能预留”层。

### <a name="metrics"></a>指标

- [Azure 指标资源管理器的高级功能](platform/metrics-charts.md) - 添加了聚合部分。

### <a name="workbooks"></a>工作簿

- [Azure Monitor 工作簿和 Azure 资源管理器模板](platform/workbooks-automate.md) - 添加了用于部署工作簿模板的资源管理器模板。

## <a name="march-2020"></a>2020 年 3 月

### <a name="general"></a>常规

- [Azure Monitor 概述](overview.md) - 添加了 Azure Monitor 概述视频。
- [Azure Monitor 客户托管密钥配置](platform/customer-managed-keys.md) - 常规更新。
- [Azure Monitor 数据参考](/azure/azure-monitor/reference/) - 新站点。

### <a name="alerts"></a>警报

- [在 Azure Monitor 中创建、查看和管理活动日志警报](platform/alerts-activity-log.md) - 资源管理器模板的附加说明。
- [了解指标警报在 Azure Monitor 中的工作原理](platform/alerts-metric-overview.md) - 更新了政府支持。
- [排查 Azure Monitor 警报和通知问题](platform/alerts-troubleshoot.md) - 新文章。

### <a name="application-insights"></a>Application Insights

- [使用 PowerShell 自动化 Azure Application Insights](app/powershell.md) - 添加了 ARMClient 示例。
- [从 Application Insights 连续导出遥测数据](app/export-telemetry.md) - 添加了包含导出结构详细信息的表格。
- [为 Azure 应用服务中的 .NET 应用启用 Snapshot Debugger](app/snapshot-debugger-appservice.md) - 添加了资源管理器模板示例。
- [管理 Azure Application Insights 的用量和成本](app/pricing.md) - 添加了有关数据上限警报的信息。
- [使用 Azure Monitor 监视 Python 应用程序（预览版）](app/opencensus-python.md)- 添加了标准指标。
- [JavaScript 应用程序的源映射支持 - Azure Monitor Application Insights](app/source-map-support.md) - 新文章。

### <a name="containers"></a>容器

- [Azure Monitor 常见问题解答](faq.md) - 更新了用于容器的 Azure Monitor 的内容。
- [使用用于容器的 Azure Monitor 配置 GPU 监视](insights/container-insights-gpu-monitoring.md) - 新文章。

### <a name="insights"></a>洞察力

- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更新了弃用日期。

### <a name="logs"></a>日志

- [在 Azure Monitor 中优化日志查询](log-query/query-optimization.md) - 添加了 XML 和 JSON 分析的 CPU 条件。
- [删除和恢复 Azure Log Analytics 工作区](platform/delete-workspace.md) - 添加了故障排除方法。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](platform/logicapp-flow-connector.md) - 更新了新 Azure Monitor 连接器的内容。

### <a name="metrics"></a>指标

- [即将弃用 Azure 门户中的磁盘指标](platform/portal-disk-metrics-deprecation.md) - 新文章。
- [教程 - 在 Azure Monitor 中创建指标图表](learn/tutorial-metrics-explorer.md) - 添加了视频。

### <a name="platform-logs"></a>平台日志

- [在 Azure Monitor 中收集和分析 Azure 活动日志](./platform/activity-log.md) - 重新撰写了该文章，以更好地解释如何使用诊断设置收集活动日志。

### <a name="virtual-machines"></a>虚拟机

- [使用 Azure Monitor 监视 Azure 虚拟机](insights/monitor-vm-azure.md) - 新文章。
- [快速入门：使用 Azure Monitor 监视 Azure 虚拟机](learn/quick-monitor-azure-vm.md) - 更新了该文章，在其中添加了用于 VM 的 Azure Monitor 的内容。
- [用于 VM 的 Azure Monitor 发出的警报](insights/vminsights-alerts.md) - 新文章。
- [启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md) - 更新了代理下载链接。

用于 VM 的 Azure Monitor 正式版常规更新

- [什么是用于 VM 的 Azure Monitor？](insights/vminsights-overview.md)
- [用于 VM 的 Azure Monitor（正式版）常见问题解答](insights/vminsights-ga-release-faq.md) 
- [使用 Azure Policy 启用用于 VM 的 Azure Monitor](./insights/vminsights-enable-policy.md) 
- [如何使用用于 VM 的 Azure Monitor 绘制性能图表](insights/vminsights-performance.md)
- [如何从用于 VM 的 Azure Monitor 查询日志](insights/vminsights-log-search.md)
- [使用用于 VM 的 Azure Monitor 查看应用依赖项](insights/vminsights-maps.md) 

### <a name="visualizations"></a>可视化效果

- [从 Azure Monitor 可视化数据](visualizations.md) - 更新了该文章，指出即将按计划弃用视图设计器。

## <a name="february-2020"></a>2020 年 2 月

### <a name="agents"></a>代理

通过重新编写诊断扩展内容进行了多处更新。

- [Azure 监视代理概述](platform/agents-overview.md) - 重新构造了各个表格来更好地阐明每个代理的特有功能。
- [Azure 诊断扩展概述](platform/diagnostics-extension-overview.md) - 全部进行了重新编写。
- [在 Azure Monitor 中使用适用于 IIS 的 Blob 存储和适用于事件的表存储](platform/diagnostics-extension-logs.md) - 针对更新和澄清进行了常规重新编写。
- [安装并配置 Windows Azure 诊断扩展 (WAD)](platform/diagnostics-extension-windows-install.md) - 新文章。 
- [Windows 诊断扩展架构](platform/diagnostics-extension-schema-windows.md) - 已重新组织。
- [将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心](platform/diagnostics-extension-stream-event-hubs.md) - 全部进行了重新编写和更新。
- [在 Azure 存储中存储和查看诊断数据](../cloud-services/diagnostics-extension-to-storage.md) - 全部进行了重新编写和更新。
- [适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md) - 更好地阐明了与 Log Analytics 代理的关系。
- [适用于 Linux 的 Azure Monitor 虚拟机扩展](../virtual-machines/extensions/oms-linux.md) - 更好地阐明了与 Log Analytics 代理的关系。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights 中的连接字符串](app/sdk-connection-string.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案

#### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

- [将 Azure Active Directory 与 Azure Kubernetes 服务集成](../aks/azure-ad-integration-cli.md) - 添加了有关以下事项的说明：创建客户端应用程序来支持启用了 RBAC 的群集以支持将 Azure Monitor 用于容器。

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

- [使用诊断设置收集 Azure 活动日志 - Azure Monitor](./platform/activity-log.md) - 有关已更改的属性的附加信息。
- [导出 Azure 活动日志](./platform/activity-log.md#legacy-collection-methods) - 根据 UI 变化更新了此文章。 

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

- [用于容器的 Azure Monitor 常见问题解答](./faq.md) - 添加了有关“映像”和“名称”字段的问题。
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

- [将 Azure 资源日志存档到存储帐户](./platform/resource-logs.md#send-to-azure-storage)
- [Azure 活动日志事件架构](platform/activity-log-schema.md)
- [Azure Monitor 服务限制](service-limits.md)
- [收集和分析 Log Analytics 工作区中的 Azure 活动日志](./platform/activity-log.md)
- [使用诊断设置（预览版）收集 Azure 活动日志 - Azure Monitor](./platform/activity-log.md)
- [跨 Azure 租户将 Azure 活动日志收集到 Log Analytics 工作区中](platform/activity-log-collect-tenants.md)
- [收集 Log Analytics 工作区中的 Azure 资源日志](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [使用资源管理器模板在 Azure 中创建诊断设置](platform/diagnostic-settings-template.md)
- [创建诊断设置以收集 Azure 中的日志和指标](platform/diagnostic-settings.md)
- [导出 Azure 活动日志](./platform/activity-log.md#legacy-collection-methods)
- [Azure 平台日志概述](platform/platform-logs-overview.md)
- [将 Azure 监视数据流式传输到事件中心](platform/stream-monitoring-data-event-hubs.md)
- [将 Azure 平台日志流式传输到事件中心](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>快速入门和教程

- [在 Azure Monitor 中创建指标图表](learn/tutorial-metrics-explorer.md) - 新文章。
- [从 Azure 资源收集资源日志并使用 Azure Monitor 进行分析](learn/tutorial-resource-logs.md) - 新文章。
- [使用 Azure Monitor 监视 Azure 资源](learn/quick-monitor-azure-resource.md) - 新文章。
   
## <a name="next-steps"></a>后续步骤

- 若要贡献 Azure Monitor 文档，请参阅[文档供稿人指南](/contribute/)。