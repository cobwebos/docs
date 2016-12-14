---
title: "Log Analytics 中的警报 | Microsoft Docs"
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
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7f8603c9ddfd3f99ea38ad07b7a8a0a553e5e4dd


---
# <a name="alerts-in-log-analytics"></a>Log Analytics 中的警报
Log Analytics 中的警报标识 OMS 存储库中的重要信息。  警报规则根据计划自动运行日志搜索，并在结果匹配特定条件的情况下，创建警报记录。  然后，该规则可自动运行一个或多个操作来主动向你通知警报或调用另一个进程。   

![Log Analytics 警报](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>创建警报规则
若要创建警报规则，需要首先针对应调用警报的记录创建日志搜索。  然后，就可以使用“**警报**”按钮以创建和配置警报规则。

1. 从 OMS“概述”页中，单击“**日志搜索**”。
2. 创建新的日志搜索查询，或选择已保存的日志搜索。 
3. 单击页面顶部的“**警报**”，以打开“**添加警报规则**”屏幕。
4. 请参阅下表，详细了解有关用于配置警报的各个选项。
5. 如果提供警报规则的时间范围，则将显示该时间范围内符合搜索条件的现有记录数。  这可以帮助你确定将为你提供期望的结果数的频率。
6. 单击“**保存**”完成警报规则。  该警报将立即开始运行。

![添加警报规则](media/log-analytics-alerts/add-alert-rule.png)

| 属性 | 说明 |
|:--- |:--- |
| **警报信息** | |
| Name |用于标识警报规则的唯一名称。 |
| 严重性 |此规则创建的警报的严重性。 |
| 搜索查询 |选择“**使用当前搜索查询**”以使用当前查询，或从列表中选择现有的已保存搜索。  文本框中提供了查询语法，你可以根据需要对其修改。 |
| 时间范围 |指定查询的时间范围。  查询仅返回此当前时间范围内创建的记录。  这可以是介于 5 分钟到 24 小时之间的任何值。  该值应大于或等于警报的频率。  <br><br> 例如，如果时间范围设置为 60 分钟，并且在下午 1:15 运行查询时，则仅返回下午 12:15 和 1:15 PM 之间创建的记录。 |
| **计划** | |
| 阈值 |何时创建警报的条件。  如果查询返回的记录数与此条件匹配，则创建警报。 |
| 警报频率 |指定应运行查询的频率。  可以是介于 5 分钟到 24 小时之间的任何值。  应等于或小于时间范围。 |
| 阻止警报 |如果打开警报规则的阻止功能，则新建警报之后将在定义的时间段内禁用该规则的操作。  此规则仍在运行中，并且会在满足条件的情况下，创建警报记录。  这是为了让你有时间更正问题，而无需运行重复操作。 |
| **操作** | |
| 电子邮件通知 |如果希望在触发警报时发送电子邮件，则指定“**是**”。 |
| 使用者 |电子邮件主题。  不能修改邮件正文。 |
| 收件人 |所有电子邮件收件人的地址。  如果指定多个地址，则用分号 (;) 分隔地址。 |
| Webhook |如果想要在触发警报时调用 webhook，则指定“**是**”。 |
| Webhook URL |Webhook 的 URL。 |
| 包含自定义 JSON 负载 |如果想要将默认负载替换为自定义负载，请选择此选项。 |
| 输入自定义 JSON 负载 |Webhook 的自定义负载。  如需详细信息，请参阅上一节。 |
| Runbook |如果想要在触发警报则时启动 Azure 自动化 Runbook，请指定“**是**”。 |
| 选择 Runbook |从自动化解决方案中配置的自动化帐户中的 Runbook，选择要启动的 Runbook。 |
| 运行位置 |选择“**Azure**”以在 Azure 云中运行 Runbook。  选择“**混合辅助角色**”以在本地环境中的“[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)”上运行 Runbook。 |

## <a name="manage-alert-rules"></a>管理警报规则
可以在 Log Analytics“**设置**”的“**警报**”菜单中，获得所有警报规则的列表。  

![管理警报](./media/log-analytics-alerts/configure.png)

1. 在 OMS 控制台中，选择“**设置**”磁贴。
2. 选择“**警报**”。

可以从此视图执行多个操作。

* 通过选择规则旁的“**关闭**”可禁用规则。
* 通过单击警报规则旁的铅笔图标可编辑警报规则。
* 通过单击警报规则旁的“**X**”图标可删除警报规则。 

## <a name="setting-time-windows"></a>设置时间范围
### <a name="event-alerts"></a>事件警报
事件包括数据源（如 Windows 事件日志、Syslog 和自定义记录）。  你可能需要在创建特定错误事件时，或在特定时间范围内创建多个错误事件时创建警报。

若要对单个事件发出警报，请将结果数设置为大于 0，并将频率和时间范围都设置为 5 分钟。  这样便会每 5 分钟运行一次查询，并检查自上次运行该查询以来已创建的某一事件的出现次数。  较长的频率可能会延长收集事件和创建警报之间的时间。

某些应用程序可能会记录不一定引发警报的偶然错误。  例如，应用程序可能会重试造成错误事件的过程，而下一次就会成功。  在这种情况下，你可能不想创建警报，除非在特定时间范围内创建多个事件。  

在某些情况下，你可能想要在某个事件不存在的情况下创建警报。  例如，进程可能记录常规事件以指明其运行正常。  如果它不在特定时间范围内记录某个事件，则应创建警报。  在这种情况下，应将阈值设置为“*小于 1*”。

### <a name="performance-alerts"></a>性能警报
与事件类似，[性能数据](log-analytics-data-sources-performance-counters.md)存储为 OMS 存储库中的记录。  每个记录中的值是过去 30 分钟测量到的平均值。  如果你想要在性能计数器超过特定阈值时发出警报，则该阈值应包括在查询中。

例如，如果想要在处理器运行率超过 90% 的时间长达 30 分钟时发出警报，则需要使用类似 *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* 的查询，并将警报规则的阈值设置为“*大于 0*”。  

 不论你收集每个计数器的频率如何，都会每隔 30 分钟聚合一次“[性能记录](log-analytics-data-sources-performance-counters.md)”，因此，小于 30 分钟的时间范围可能不会返回任何记录。  将时间范围设置为 30 分钟，可确保对每个连接的源获得单一记录，以代表该时间段内平均值。

## <a name="alert-actions"></a>警报操作
除了创建警报记录，还可以配置警报规则，以自动运行一个或多个操作。  操作可以主动向你通知警报，或者调用某些进程，以尝试更正检测到的问题。  以下各部分描述了当前可用的操作。

### <a name="email-actions"></a>电子邮件操作
电子邮件操作会发送内含警报详细信息的电子邮件给一位或多位收件人。  你可以指定邮件主题，但其内容是由 Log Analytics 构造的标准格式。  除了日志搜索所返回的最多 10 条记录的详细信息之外，邮件内容还包括摘要信息（如警报名称）。  此外，还包含 Log Analytics 中将从查询中返回整个记录集的日志搜索的链接。   邮件发件人为 *Microsoft Operations Management Suite (OMS) 团队&lt;noreply@oms.microsoft.com&gt;*。 

### <a name="webhook-actions"></a>Webhook 操作
Webhook 操作可让你通过单个 HTTP POST 请求调用外部进程。  被调用的服务应支持 Webhook，并确定将如何使用接收的任何负载。  只要此请求采用 API 理解的格式，你还可以调用不专门支持 Webhook 的 REST API。  使用 Webhook 来响应警报的示例有会使用 [Slack](http://slack.com) 之类的服务来发送包含警报详细信息的消息，或在 [PagerDuty](http://pagerduty.com/) 之类的服务中创建事件。  

有关使用 Webhook 来调用示例服务，以创建警报规则的完整操作步骤，请参阅 [Log Analytics 警报中的 Webhook](log-analytics-alerts-webhooks.md)。

Webhooks 包括 URL 和 JSON 格式的负载（即发送到外部服务的数据）。  默认情况下，负载将包含下表中的值。  可以选择将此负载替换为你自己的自定义负载。  在这种情况下，可以使用下表中每个参数的变量，将其值包含在自定义负载中。

| 参数 | 变量 | 说明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警报规则的名称。 |
| AlertThresholdOperator |#thresholdoperator |警报规则的阈值运算符。  *大于*或*小于*。 |
| AlertThresholdValue |#thresholdvalue |警报规则的阈值。 |
| LinkToSearchResults |#linktosearchresults |指向 Log Analytics 日志搜索的链接，该搜索会从创建警报的查询返回记录。 |
| ResultCount |#searchresultcount |搜索结果中的记录数。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |查询结束时间（UTC 格式）。 |
| SearchIntervalInSeconds |#searchinterval |警报规则的时间范围。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |查询开始时间（UTC 格式）。 |
| SearchQuery |#searchquery |警报规则所使用的日志搜索查询。 |
| SearchResults |请参阅下文 |查询所返回的 JSON 格式的记录。  限制为前 5000 条记录。 |
| WorkspaceID |#workspaceid |OMS 工作区的 ID。 |

例如，可以指定以下自定义有效负载，其中包含名为 *text* 的单一参数。  该 Webhook 调用的服务将需要此参数。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此示例的有效负载会在发送到 Webhook 时解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若在自定义有效负载中包含搜索结果，请将下行添加为 json 负载中的最高级别属性。  

    "IncludeSearchResults":true

例如，若要创建只包含警报名称和搜索结果的自定义负载，则可以使用以下代码。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


你可以在 [Log Analytics 警报 Webhook 示例](log-analytics-alerts-webhooks.md)中使用 Webhook 来启动外部服务，逐步完成创建规则的整个示例。

### <a name="runbook-actions"></a>Runbook 操作
Runbook 操作可在 Azure 自动化中启动 Runbook。  若要使用此类型的操作，必须在 OMS 工作区中安装和配置[自动化解决方案](log-analytics-add-solutions.md)。  如果在创建新的警报规则时未安装该解决方案，则将显示用于安装它的链接。  可以从在自动化解决方案中配置的自动化帐户中的 Runbook 进行选择。

Runbook 操作使用 [Webhook](../automation/automation-webhooks.md) 来启动 Runbook。  在创建警报规则时，系统会自动为 Runbook 创建新的 Webhook，其名称为 **OMS Alert Remediation** 且后跟一个 GUID。  

你无法直接填充 Runbook 的任何参数，但 [$WebhookData 参数](../automation/automation-webhooks.md)将包含警报的详细信息，包括创建该警报的日志搜索结果。  Runbook 需要将 **$WebhookData** 定义为参数，以使其访问警报属性。  在 **$WebhookData** 的 **RequestBody** 属性中，警报数据将在称为 **SearchResults** 的单一属性中以 json 格式提供。  这些数据具有下表所列的属性。

| 节点 | 说明 |
|:--- |:--- |
| id |搜索的路径和 GUID。 |
| __metadata |有关警报的信息，包括记录数和搜索结果状态。 |
| value |搜索结果中每个记录的单独条目。  条目的详细信息将匹配记录的属性和值。 |

例如，下面的 Runbook 会提取日志搜索返回的记录，并基于每个记录的类型分配不同属性。  请注意，该 Runbook 以从 json 转换 **RequestBody** 开始，因此，它可以作为 PowerShell 中的对象。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>警报记录
Log Analytics 中警报规则创建的警报记录的“**类型**”为“**警报**”，“**SourceSystem**”为“**OMS**”。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 类型 |*Alert* |
| SourceSystem |*OMS* |
| AlertSeverity |警报的严重级别。 |
| AlertName |警报的名称。 |
| 查询 |运行的查询的文本。 |
| QueryExecutionEndTime |查询的时间范围结束点。 |
| QueryExecutionStartTime |查询的时间范围开始点。 |
| TimeGenerated |警报的创建日期和时间。 |

还有其他类型的由[警报管理解决方案](log-analytics-solution-alert-management.md)和 [Power BI 导出内容](log-analytics-powerbi.md)创建的警报记录。  它们的“**类型**”都为“**警报**”，但其“**SourceSystem**”不同。

## <a name="next-steps"></a>后续步骤
* 安装[警报管理解决方案](log-analytics-solution-alert-management.md)，以分析 Log Analytics 中创建的警报和从 System Center Operations Manager (SCOM) 收集的警报。
* 深入了解有关可生成警报的[日志搜索](log-analytics-log-searches.md)。
* 完成[配置 Webook](log-analytics-alerts-webhooks.md) 和警报规则的演练步骤。  
* 了解如何[在 Azure 自动化中编写 Runbook](https://azure.microsoft.com/documentation/services/automation)，以修正警报标识的问题。




<!--HONumber=Nov16_HO3-->


