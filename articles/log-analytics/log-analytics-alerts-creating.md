---
title: "在 OMS Log Analytics 中创建警报 | Microsoft Docs"
description: "Log Analytics 中的警报标识 OMS 存储库中的重要信息，还可主动通知你存在问题或调用操作以尝试更正问题。  本文介绍如何创建警报规则和它们可执行的不同操作的详细信息。"
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
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="working-with-alert-rules-in-log-analytics"></a>使用 Log Analytics 中的警报规则
警报通过警报规则创建，警报规则定期自动运行日志搜索。  如果结果符合特定的条件，警报规则将创建警报记录。  然后，该规则可自动运行一个或多个操作以主动通知你存在警报或调用另一个进程。   

本文介绍使用 OMS 门户创建和编辑警报规则的过程。  有关不同的设置以及如何实现所需逻辑的详细信息，请参阅[了解 Log Analytics 中的警报](log-analytics-alerts.md)。

>[!NOTE]
> 目前无法使用 Azure 门户创建或修改警报规则。 

## <a name="create-an-alert-rule"></a>创建警报规则

若要使用 OMS 门户创建警报规则，首先请针对应该调用警报的记录创建日志搜索。  **警报**按钮使你可以创建和配置警报的规则将会可用。

>[!NOTE]
> 当前在 OMS 工作区中最多可以创建 250 个警报规则。 

1. 从 OMS“概述”页中，单击“**日志搜索**”。
2. 创建新的日志搜索查询，或选择已保存的日志搜索。 
3. 单击页面顶部的“**警报**”，以打开“**添加警报规则**”屏幕。
4. 使用下面[警报规则的详细信息](#details-of-alert-rules)中的信息配置警报规则。
6. 单击“**保存**”完成警报规则。  该警报将立即开始运行。


## <a name="edit-an-alert-rule"></a>编辑警报规则
可以在 Log Analytics“**设置**”的“**警报**”菜单中，获得所有警报规则的列表。  

![管理警报](./media/log-analytics-alerts/configure.png)

1. 在 OMS 控制台中，选择“**设置**”磁贴。
2. 选择“**警报**”。

可以从此视图执行多个操作。

* 通过选择规则旁的“**关闭**”可禁用规则。
* 通过单击警报规则旁的铅笔图标可编辑警报规则。
* 通过单击警报规则旁的“**X**”图标可删除警报规则。 

## <a name="details-of-alert-rules"></a>警报规则的详细信息
在 OMS 门户中创建或编辑警报规则时，需要使用“添加警报规则”或“编辑警报规则”页。  下表描述了此屏幕中的字段。

![警报规则](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>警报信息
这是警报规则及其创建的警报的基本设置。

| 属性 | 说明 |
|:--- |:---|
| Name | 用于标识警报规则的唯一名称。 此名称包含在规则创建的任何警报中。  |
| 说明 | 警报规则的可选说明。 |
| 严重性 |此规则创建的任何警报的严重性。 |

### <a name="search-query-and-time-window"></a>搜索查询和时间范围
搜索查询和时间范围，返回确定是否应创建任何警报时要评估的记录。

| 属性 | 说明 |
|:--- |:---|
| 搜索查询 | 这是要运行的查询。  此查询返回的记录用于确定是否创建警报。<br><br>选择“**使用当前搜索查询**”以使用当前查询，或从列表中选择现有的已保存搜索。  文本框中提供了查询语法，可以根据需要对其修改。 |
| 时间范围 |指定查询的时间范围。  查询仅返回此当前时间范围内创建的记录。  这可以是介于 5 分钟到 24 小时之间的任何值。  该值应大于或等于警报的频率。  <br><br> 例如，如果时间范围设置为 60 分钟，并且在下午 1:15 运行查询，则仅返回下午 12:15 和 1:15 PM 之间创建的记录。 |

如果提供警报规则的时间范围，则会显示该时间范围内符合搜索条件的现有记录数。  这可以帮助你确定将提供期望的结果数的频率。

### <a name="schedule"></a>计划
定义搜索查询的运行频率。

| 属性 | 说明 |
|:--- |:---|
| 警报频率 | 指定应运行查询的频率。 可以是介于 5 分钟到 24 小时之间的任何值。 应等于或小于时间范围。  如果该值大于时间范围，则会有记录缺失的风险。<br><br>例如，假设时间范围为 30 分钟，频率为 60 分钟。  如果查询在下午 1:00 运行，则会返回中午 12:30 和下午 1:00 之间的记录。  下次运行查询的时间是下午 2:00，会返回下午 1:30 到 2:00 之间的记录。  在下午 1:00 和 1:30 之间创建的任何记录不会获得评估。 |


### <a name="generate-alert-based-on"></a>基于以下项生成警报
定义将针对搜索查询的结果计算的条件，以确定是否应创建警报。  这些详细信息将因所选的警报规则的类型而异。  可以从[了解 Log Analytics 中的警报](log-analytics-alerts.md)中获取不同警报规则类型的详细信息。

| 属性 | 说明 |
|:--- |:---|
| 阻止警报 | 如果打开警报规则的阻止功能，则新建警报之后会在定义的时间段内禁用该规则的操作。 此规则仍在运行中，并且会在满足条件的情况下创建警报记录。 这是为了让你有时间以更正此问题，而无需运行重复操作。 |

#### <a name="number-of-results-alert-rules"></a>结果警报规则数

| 属性 | 说明 |
|:--- |:---|
| 结果数 |如果查询返回的记录数**大于**或**小于**提供的值，则创建警报。  |

#### <a name="metric-measurement-alert-rules"></a>指标度量警报规则

| 属性 | 说明 |
|:--- |:---|
| 聚合值 | 要视为违规，结果中每个聚合值必须超过的阈值。 |
| 基于以下项触发警报 | 要创建的警报的违规次数。  可以为结果集中的任何违规组合指定**总违规数**，或指定**连续违规数**以要求违规必须在连续采样时发生。 |

### <a name="actions"></a>操作
达到阈值时，警报规则始终创建[警报记录](#alert-records)。  还可以定义一个或多个要运行的响应，例如，发送电子邮件或启动 Runbook。



#### <a name="email-actions"></a>电子邮件操作
电子邮件操作会将内含警报详细信息的电子邮件发送给一位或多位收件人。

| 属性 | 说明 |
|:--- |:---|
| 电子邮件通知 |如果希望在触发警报时发送电子邮件，则指定“**是**”。 |
| 使用者 |电子邮件主题。  不能修改邮件正文。 |
| 收件人 |所有电子邮件收件人的地址。  如果指定多个地址，则用分号 (;) 分隔地址。 |

#### <a name="webhook-actions"></a>Webhook 操作
使用 Webhook 操作可通过单个 HTTP POST 请求调用外部进程。

| 属性 | 说明 |
|:--- |:---|
| Webhook |如果想要在触发警报时调用 webhook，则指定“**是**”。 |
| Webhook URL |Webhook 的 URL。 |
| 包含自定义 JSON 负载 |如果想要将默认负载替换为自定义负载，请选择此选项。 |
| 输入自定义 JSON 负载 |Webhook 的自定义负载。  如需详细信息，请参阅上一节。 |

#### <a name="runbook-actions"></a>Runbook 操作
Runbook 操作可在 Azure 自动化中启动 Runbook。 

>[!NOTE]
> 要启用此操作，必须在工作区中安装自动化解决方案。 


| 属性 | 说明 |
|:--- |:---|
| Runbook | 如果想要在触发警报则时启动 Azure 自动化 Runbook，请指定“**是**”。  |
| 自动化帐户 | 指定要从中选择 Runbook 的自动化帐户。  这是与工作区链接的操作帐户。 |
| 选择 Runbook | 选择创建警报时要启动的 Runbook。 |
| 运行位置 | 选择“Azure”可在云中运行 Runbook。  选择“混合辅助角色”可在安装了[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md )的代理上运行 Runbook。  |




## <a name="next-steps"></a>后续步骤
* 安装[警报管理解决方案](log-analytics-solution-alert-management.md)，以分析 Log Analytics 中创建的警报和从 System Center Operations Manager (SCOM) 收集的警报。
* 深入了解有关可生成警报的[日志搜索](log-analytics-log-searches.md)。
* 完成[配置 Webook](log-analytics-alerts-webhooks.md) 和警报规则的演练步骤。  
* 了解如何[在 Azure 自动化中编写 Runbook](https://azure.microsoft.com/documentation/services/automation)，以修正警报标识的问题。

