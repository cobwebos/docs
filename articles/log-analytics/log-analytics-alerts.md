---
title: "OMS Log Analytics 中的警报概述 | Microsoft 文档"
description: "Log Analytics 中的警报标识 OMS 存储库中的重要信息，并可以主动向你通知问题，或调用操作以尝试更正问题。  本文介绍如何创建警报规则和它们可执行的不同操作的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>使用警报响应 Log Analytics 中的问题

Log Analytics 中的警报标识 Log Analytics 存储库中的重要信息。  可以使用警报在出现问题时获得主动通知，或者采用自动操作来应对问题。  本文概述了如何创建和使用警报。  


>[!NOTE]
> 有关公共预览版中当前存在的指标测量的警报规则的信息，请参阅 [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/)（公共预览中新的指标测量警报规则类型！）。

## <a name="alert-rules"></a>警报规则

警报通过警报规则创建，警报规则定期自动运行日志搜索。  如果日志搜索的结果符合特定条件，则会创建警报记录。  该规则还可自动运行一个或多个操作，主动向用户提供警报通知或调用另一个进程。  

![Log Analytics 警报](media/log-analytics-alerts/overview.png)


警报规则通过以下详细信息定义。

- **日志搜索**。  这是每次触发警报规则时都会运行的查询。  此查询返回的记录将用于确定是否创建了警报。
- **时间窗口**。  指定查询的时间范围。  查询仅返回在当前时间的这个范围内创建的记录。  这可以是介于 5 分钟到 24 小时之间的任何值。 例如，如果时间范围设置为 60 分钟，并且在下午 1:15 运行查询，则仅返回下午 12:15 和 1:15 PM 之间创建的记录。
- **频率**。  指定应运行查询的频率。 可以是介于 5 分钟到 24 小时之间的任何值。 应等于或小于时间范围。  如果该值大于时间窗口，则会有记录缺失的风险。<br>例如，考虑一下时间窗口为 30 分钟，频率为 60 分钟的情况。  如果查询在下午 1:00 运行，则会返回中午 12:30 和下午 1:00 之间的记录。  下次运行查询的时间是下午 2:00，将会返回下午 1:30 到 2:00 之间的记录。  在下午 1:00 和 1:30 之间创建的任何记录不会获得评估。
- **阈值**。  如果从日志搜索返回的记录数超出阈值，则会创建警报。

## <a name="creating-alert-rules"></a>创建警报规则
可以使用多种方法创建和修改警报规则。  有关详细指南，请参阅以下文章。  

- 使用 [OMS 门户](log-analytics-alerts-creating.md)创建警报规则。
- 使用 [Resource Manager 模板](log-analytics-template-workspace-configuration.md)创建警报规则。
- 使用 [REST API](log-analytics-api-alerts.md) 创建警报规则。

## <a name="alert-actions"></a>警报操作

除了创建警报记录，警报规则还可以在创建警报时执行一个或多个操作。  可以通过操作发送邮件以响应警报，也可以启动一个过程，以便采取纠正措施。  

也可利用操作提升 Log Analytics 在其他服务中的功能。  例如，Log Analytics 目前不提供通过短信或电话通知用户的功能。  但是，用户可以使用警报规则中的 Webhook 操作调用提供这些功能的服务，例如 [PagerDuty](https://www.pagerduty.com/)。  用户可以参阅[通过在 OMS Log Analytics 中创建警报 Webhook 操作向 Slack 发送短信](log-analytics-alerts-webhooks.md)中提供的示例，了解如何使用 [Slack](https://slack.com/) 创建可发送短信的 Webhook。

下表列出了可执行的操作。  可参阅[在 Log Analytics 中向警报规则添加操作](log-analytics-alerts-actions.md)，了解所有这些内容。 

| 操作 | 说明 |
|:--|:--|
| 电子邮件  |     将内含警报详细信息的电子邮件发送给一位或多位收件人。 |
| Webhook | 通过单个 HTTP POST 请求调用外部进程。 |
| Runbook | 在 Azure 自动化中启动 Runbook。 |


## <a name="alerting-scenarios"></a>警报方案

### <a name="events"></a>事件
若要对单个事件发出警报，请在创建规则时将结果数设置为“大于 0”，并将频率和时间窗口都设置为“5 分钟”。  这样便会每 5 分钟运行一次查询，并检查自上次运行该查询以来已创建的某一事件的出现次数。  较长的频率可能会延长收集事件和创建警报之间的时间。  可以使用类似下面的查询，指定感兴趣的事件。

    Type=Event Source=MyApplication EventID=7019 

某些应用程序可能会记录不一定引发警报的偶然错误。  例如，应用程序可能会重试导致错误事件的进程，而下一次就会成功。  在这种情况下，你可能不想创建警报，除非在特定时间范围内创建多个事件。  为此，可使用同一查询，但需将阈值设置为更高的值。  例如，若要对 30 分钟内发生 5 个事件这种情况发出警报，可将频率设置为“5 分钟”，将时间窗口设置为“30 分钟”，将结果数设置为“大于 4”。    

在某些情况下，你可能想要在某个事件不存在的情况下创建警报。  例如，进程可能记录常规事件以指明其运行正常。  如果它不在特定时间范围内记录某个事件，则应创建警报。  在这种情况下，应将结果数设置为“小于 1”。

### <a name="performance-alerts"></a>性能警报
与事件类似，[性能数据](log-analytics-data-sources-performance-counters.md)作为记录存储在 Log Analytics 存储库中。  如果你想要在性能计数器超过特定阈值时发出警报，则该阈值应包括在查询中。

例如，若希望处理器在使用率超过 90% 时发出警报，可以使用如下查询，并将警报规则的结果数设置为“大于 0”。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

若希望处理器在特定时间窗口的平均使用率超过 90% 时发出警报，则可使用如下所示的 [measure 命令](log-analytics-search-reference.md#commands)进行查询，并将警报规则的阈值设置为“大于 0”。 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>警报记录
Log Analytics 中警报规则创建的警报记录的“**类型**”为“**警报**”，“**SourceSystem**”为“**OMS**”。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 类型 |*Alert* |
| SourceSystem |*OMS* |
| AlertName |警报的名称。 |
| AlertSeverity |警报的严重级别。 |
| LinkToSearchResults |指向 Log Analytics 日志搜索的链接，该搜索会从创建警报的查询返回记录。 |
| 查询 |运行的查询的文本。 |
| QueryExecutionEndTime |查询的时间范围结束点。 |
| QueryExecutionStartTime |查询的时间范围开始点。 |
| ThresholdOperator | 警报规则所使用的运算符。 |
| ThresholdValue | 警报规则所使用的值。 |
| TimeGenerated |警报的创建日期和时间。 |

还有其他类型的由[警报管理解决方案](log-analytics-solution-alert-management.md)和 [Power BI 导出内容](log-analytics-powerbi.md)创建的警报记录。  它们的“**类型**”都为“**警报**”，但其“**SourceSystem**”不同。


## <a name="next-steps"></a>后续步骤
* 使用 [OMS 门户](log-analytics-alerts-creating.md)创建警报规则。
* 安装[警报管理解决方案](log-analytics-solution-alert-management.md)，以分析 Log Analytics 中创建的警报和从 System Center Operations Manager (SCOM) 收集的警报。
* 深入了解有关可生成警报的[日志搜索](log-analytics-log-searches.md)。
* 完成[配置 Webook](log-analytics-alerts-webhooks.md) 和警报规则的演练步骤。  
* 了解如何[在 Azure 自动化中编写 Runbook](https://azure.microsoft.com/documentation/services/automation)，以修正警报标识的问题。


