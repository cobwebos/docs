---
title: "Log Analytics 新日志搜索常见问题解答 | Microsoft Docs"
description: "本文提供有关将 Log Analytics 升级到新的查询语言的常见问题解答。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Log Analytics 新日志搜索常见问题解答和已知问题

本文提供包括有关[将 Log Analytics 升级到新的查询语言](log-analytics-log-search-upgrade.md)的常见问题解答和已知问题。  决定升级工作区前，请通读全文。


## <a name="alerts"></a>警报

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>问：我有大量警报规则。 升级后，是否需要采用新的语言再次创建这些规则？  
不需要，在升级过程中，警报规则会自动转换为新的搜索语言。  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>问：我有关于 webhook 和 runbook 操作的预警规则。 升级后这些规则还会继续工作吗？

不会，webhook 和 runbook 操作中发生了一些更改，这可能要求对有效负载的处理方式进行一些更改。 我们所做的这些更改旨在标准化各种输出格式和减小有效负载的大小。 有关这些格式的详细信息，请参阅[将操作添加到 Log Analytics 中的警报规则](log-analytics-alerts-actions.md)。


## <a name="computer-groups"></a>计算机组

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>问：我在尝试使用计算机组时收到了错误。  其语法是否已经更改？
是的，工作区升级后，使用计算机组的语法将更改。  请参阅 [Log Analytics 日志搜索中的计算机组](log-analytics-computer-groups.md)了解详细信息。


## <a name="dashboards"></a>仪表板

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>问：是否可以在升级后的工作区中使用仪表板？
升级后，我们会开始弃用“我的仪表板”。  可以继续使用工作区升级前添加到仪表板的任何磁贴，但无法编辑这些磁贴或创建新磁贴。  可以继续使用[视图设计器](log-analytics-view-designer.md)（具有更丰富的功能集）创建和编辑视图，也可在 Azure 门户中创建仪表板。


## <a name="log-searches"></a>日志搜索

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>问：已将搜索保存到升级的工作区之外。 是否可以自动将这些搜索转换到新的搜索语言？
可以在“日志搜索”页使用语言转换器工具转换每个搜索。  目前无法在不升级工作区的情况下自动转换多个搜索。

### <a name="question-why-are-my-query-results-not-sorted"></a>问：为什么我的查询结果未排序？
默认情况下，新的查询语言中不对结果进行排序。  使用 [sort 运算符](https://go.microsoft.com/fwlink/?linkid=856079)以便按一个或多个属性对结果进行排序。

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>问：升级后“指标”视图位于何处？
“指标”视图以图形形式表示日志搜索中的性能数据。  升级后此视图将不再可用。  可使用 [render 运算符](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator)设置时间图表中查询的输出格式。

### <a name="question-where-did-minify-go-after-i-upgraded"></a>问题：升级后“缩小”选项位于何处？
“缩小”是一个功能，提供搜索结果的汇总视图。  升级后，“缩小”选项不再显示在“日志搜索”门户中。  可使用新搜索语言中的 [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) 或 [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster) 获得与之类似的功能。 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>日志搜索 API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>问：升级后，日志搜索 API 是否会更新？
升级工作区时，旧的[日志搜索 API](log-analytics-log-search-api.md) 将不再工作。  有关新 API 的详细信息，请参阅 [Azure Log Analytics REST API](https://dev.loganalytics.io/)。


## <a name="portals"></a>门户

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>问：应使用新的高级分析门户还是继续使用日志搜索门户？
可以在 [Azure Log Analytics 中用于创建和编辑日志查询的门户](log-analytics-log-search-portals.md)参看两个门户的比较。  两者均具有明显的优势，因此可根据需求做出最佳选择。  在高级分析门户中写入查询，然后将其粘贴到其他位置（如视图设计器），这是一种很常见的做法。  执行该操作时，应了解[需要考虑的问题](log-analytics-log-search-portals.md#advanced-analytics-portal)。


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>问：升级后，收到一个“尝试运行查询”错误并且还在我的视图中看到错误。

升级后，浏览器需要访问以下地址，才能运行 Log Analytics 查询。  如果浏览器通过防火墙访问 Azure 门户，则必须允许访问这些地址。

| Uri | IP | 端口 |
|:---|:---|:---|
| portal.loganalytics.io | 动态 | 80,443 |
| api.loganalytics.io    | 动态 | 80,443 |
| docs.loganalytics.io   | 动态 | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>问：PowerBI 集成会发生改变吗？
是的。  升级工作区后，Log Analytics 数据导出到 Power BI 的过程将停止。  将禁用升级前创建的任何现有计划。  

升级后，Azure Log Analytics 会将同一平台用作 Application Insights，将 Log Analytics 查询导出到 Power BI 的过程与[将 Application Insights 查询导出到 Power BI 的过程](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)相同。  现在，导出到 Power BI 将直接调用 API 终结点。 通过此操作，最多可获得 500,000 行或 64,000,000 字节的数据，还可以导出较长查询以及自定义查询的超时时间（默认超时为 3 分钟，最长超时为 10 分钟）。

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>问：升级后，日志搜索 PowerShell cmdlet 是否会更新？
所有工作区的升级完成后，[Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) 将弃用。  使用 [Invoke-LogAnalyticsQuery cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) 在升级后的工作区中执行日志搜索。




## <a name="resource-manager-templates"></a>资源管理器模板

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>问：是否可以使用资源管理器模板创建一个已升级的工作区？
是的。  必须使用 2017-03-15-preview 的 API 版本，并且在模板中包括“功能”部分，如以下示例所示。

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>解决方案

### <a name="question-will-my-solutions-continue-to-work"></a>问：解决方案是否会继续有效？
所有解决方案在升级后的工作区中都会继续有效，尽管它们转换为新的查询语言后性能将提高。  某些已知问题已有解决方案，本部分将具体介绍。

### <a name="known-issue-perspectives-in-application-insights-connector"></a>已知问题：Application Insights 连接器中的透视
Application Insights 连接器解决方案中不再支持 [Application Insights 连接器解决方案](log-analytics-app-insights-connector.md)中的透视。  可使用视图设计器创建 Application Insights 数据的自定义视图。

### <a name="known-issue-backup-solution"></a>已知问题：备份解决方案
如果在升级工作区前安装备份解决方案，它可能不会收集数据。 卸载解决方案，然后安装最新版本。  新版解决方案不支持经典备份保管库，因此必须升级到恢复服务保管库才能继续使用此解决方案。

## <a name="upgrade-process"></a>升级过程

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>问：我有多个工作区。 是否可以同时升级所有工作区？  
不会。  升级适用于单次单个工作区。 当前没有方法可以一次性升级多个工作区。 请注意，所升级的工作区的其他用户也会受影响。  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>问：如果升级，是否将修改工作区中收集的现有日志数据？  
不会。 可用于工作区搜索的日志数据不受升级影响。 已保存的搜索、警报和视图将自动转换为新的搜索语言。  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>问：如果不升级工作区，会发生什么情况？  
旧的日志搜索将在几个月后弃用。 系统会自动升级此时间前仍未升级的工作区。

### <a name="question-can-i-revert-back-after-i-upgrade"></a>问：升级后是否可以还原？
在正式发布之前，升级后可以还原工作区。  现在，新的语言已正式发布，已删除此功能，因为我们开始停用旧的平台。


## <a name="views"></a>视图

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>问：如何使用视图设计器创建新视图？
升级之前，可以使用 OMS 门户中主仪表板上的磁贴通过视图设计器创建新视图。  工作区升级后，将删除此磁贴。  可以通过单击左侧菜单中的绿色“+”按钮，使用视图设计器在 OMS 门户中创建新视图。  可继续通过单击“视图设计器”磁贴使用 Azure 门户创建新视图。


## <a name="next-steps"></a>后续步骤

- 详细了解[将工作区升级到新的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)。
