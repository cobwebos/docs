---
title: 使用 OMS Log Analytics 警报 REST API
description: Log Analytics 警报 REST API 允许用户在属于 Operations Management Suite (OMS) 一部分的 Log Analytics 中创建和管理警报。  本文提供了用于执行不同操作的 API 和几个示例的详细信息。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 9097ca13bf4f65db4b0924044a9c0f075e3703af
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128888"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>在 Log Analytics 中通过 REST API 创建和管理警报规则
Log Analytics 警报 REST API 允许在 Operations Management Suite (OMS) 中创建和管理警报。  本文详细介绍了该 API 并提供了几个执行不同操作的示例。

Log Analytics 搜索 REST API 为 RESTful，可通过 Azure 资源管理器 REST API 访问。 在此文档中，你会发现使用 [ARMClient](https://github.com/projectkudu/ARMClient) 通过 PowerShell 命令行访问 API 的示例。ARMClient 是可简化 Azure 资源管理器 API 调用的开源命令行工具。 ARMClient 和 PowerShell 的使用是访问 Log Analytics 搜索 API 的许多选项之一。 使用这些工具可以利用 RESTful Azure 资源管理器 API 对 OMS 工作区进行调用并执行它们所包含的搜索命令。 API 以 JSON 格式输出搜索结果，从而允许通过编程以许多不同的方式来使用搜索结果。

## <a name="prerequisites"></a>先决条件
目前，仅可以使用 Log Analytics 中已保存的搜索来创建警报。  有关详细信息，请参阅[日志搜索 REST API](log-analytics-log-search-api.md)。

## <a name="schedules"></a>计划
已保存的搜索可以有一个或多个计划。 计划定义搜索的运行频率以及进行条件识别的时间间隔。
计划具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 时间间隔 |搜索的运行频率。 以分钟为度量单位。 |
| QueryTimeSpan |计算条件的时间间隔。 必须等于或大于间隔。 以分钟为度量单位。 |
| 版本 |正在使用的 API 版本。  目前应始终设置为 1。 |

例如，考虑一个间隔为 15 分钟和时间跨度为 30 分钟的事件查询。 在这种情况下，将每隔 15 分钟运行一次查询，如果条件在超过 30 分钟的时间内持续解析为 true，则会触发警报。

### <a name="retrieving-schedules"></a>检索计划
使用 Get 方法检索已保存搜索的所有计划。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

结合使用 Get 方法和计划 ID 检索已保存搜索的特定计划。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

下面是一个响应计划的示例。

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>创建计划
结合使用 Put 方法和唯一计划 ID 创建一个新计划。  请注意，两个计划 ID 不能一样，即使它们与不同的已保存搜索相关联。  在 OMS 控制台中创建计划时，将为计划 ID 创建一个 GUID。

> [!NOTE]
> 所有已保存的搜索、计划和使用 Log Analytics API 创建的操作的名称必须小写。

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>编辑计划
结合使用 Put 方法和相同已保存搜索的现有计划 ID 来修改该计划。  请求正文必须包含该计划的 etag。

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>删除计划
结合使用 Delete 方法和计划 ID 删除计划。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>操作
一个计划可以有多个操作。 操作可以定义一个或多个要执行的进程，例如发送邮件或启动 Runbook，也可以定义确定搜索结果与某些条件何时匹配的阈值。  某些操作将同时定义这两者，以便达到阈值时执行这些进程。

所有操作具有下表中的属性。  不同类型的警报具有不同的其他属性，如下所述。

| 属性 | 说明 |
|:--- |:--- |
| Type |操作的类型。  目前可能的值为警报和 Webhook。 |
| 名称 |警报的显示名称。 |
| 版本 |正在使用的 API 版本。  目前应始终设置为 1。 |

### <a name="retrieving-actions"></a>检索操作

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 检索或添加操作。

使用 Get 方法检索计划的所有操作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

结合使用 Get 方法和计划 ID 检索计划的特定操作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>创建或编辑操作
结合使用 Put 方法和计划唯一的操作 ID 可创建新操作。  在 OMS 控制台中创建操作时，GUID 是针对操作 ID。

> [!NOTE]
> 所有已保存的搜索、计划和使用 Log Analytics API 创建的操作的名称必须小写。

结合使用 Put 方法和相同已保存搜索的现有操作 ID 来修改该计划。  请求正文必须包含该计划的 etag。

创建新操作的请求格式因操作类型而异，因此在以下各节中提供了这些示例。

### <a name="deleting-actions"></a>删除操作

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 检索或添加操作。

结合使用 Delete 方法和操作 ID 可删除操作。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>警报操作
一个计划应具有一个且只能有一个警报操作。  警报操作具有下表中的一个或多个部分。  下面对各部分进行了详细描述。

| 部分 | 说明 | 使用情况 |
|:--- |:--- |:--- |
| 阈值 |用于确定何时运行操作的条件。| 每个警报所必需的，无论是在警报扩展到 Azure 之前还是之后。 |
| Severity |当触发时用来对警报进行分类的标签。| 每个警报所必需的，无论是在警报扩展到 Azure 之前还是之后。 |
| 操作组 |在其中指定所需操作的 Azure 操作组的 ID，例如 - 电子邮件、SMS、语音呼叫、Webhook、自动化 Runbook、ITSM 连接器，等等。| 警报扩展到 Azure 后所必需的|
| 自定义操作|修改有关从操作组中选择操作的标准输出| 对于每个警报都是可选的，可以在警报扩展到 Azure 后使用。 |
| EmailNotification |向多个收件人发送邮件。 | 如果警报扩展到 Azure，则不是必需的|
| 补救 |在 Azure 自动化中启动 Runbook，以尝试纠正发现的问题。 |如果警报扩展到 Azure，则不是必需的|
| Webhook 操作 | 将来自警报的数据以 JSON 形式推送到所需的服务 |如果警报扩展到 Azure，则不是必需的|

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。

#### <a name="thresholds"></a>阈值
一项警报操作应具有一个且只能有一个阈值。  当已保存搜索的结果匹配与该搜索关联的操作中的阈值时，将运行该操作中的任何其他进程。  操作也可以只包含一个阈值，以便与不包含阈值的其他类型的操作一起使用。

阈值具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 运算符 |阈值比较运算符。 <br> gt = 大于 <br> lt = 小于 |
| 值 |阈值的数值。 |

例如，考虑一个间隔为 15 分钟、时间跨度为 30 分钟并且阈值大于 10 的事件查询。 在这种情况下，将每隔 15 分钟运行一次查询，如果返回在 30 分钟时间跨度内创建的 10 个事件，则会触发警报。

下面是响应仅具备一个阈值的操作的示例。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新阈值操作。  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

结合使用 Put 方法和现有操作 ID 可修改计划的阈值操作。  请求正文必须包含操作的 etag。

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics 允许你将警报归类到各个类别，以便更轻松地进行管理和会审。 定义的警报严重性是：信息性、警告和严重。 它们如下所示映射到 Azure 警报的常规严重性级别：

|Log Analytics 严重性级别  |Azure 警报严重性级别  |
|---------|---------|
|严重 |Sev 0|
|警告 |Sev 1|
|informational | Sev 2|

下面是针对仅具有阈值和严重性的操作的一个示例响应。 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

可以组合使用 Put 方法与唯一操作 ID 来为计划创建具有严重性的新操作。  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

可以组合使用 Put 方法与现有操作 ID 来为计划修改严重性操作。  请求正文必须包含操作的 etag。

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>操作组
Azure 中的所有警报都使用操作组作为用来处理操作的默认机制。 使用操作组，可以将操作指定一次，然后将操作组关联到 Azure 中的多个警报。 不需要一再重复声明相同的操作。 操作组支持多个操作 - 包括电子邮件、SMS、语音呼叫、ITSM 连接、自动化 Runbook、Webhook URI，等等。 

对于已将其警报扩展到 Azure 中的用户- 一个计划现在应当将操作组详细信息与阈值一起传递，以便能够创建警报。 在创建警报前，需要先在操作组中定义电子邮件详细信息、Webhook URL、Runbook 自动化详细信息以及其他操作；可以在门户中[通过 Azure Monitor 创建操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)，也可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)。

若要添加操作组到警报的关联，请在警报定义中指定操作组的唯一 Azure 资源管理器 ID。 下面提供了一个示例展示：

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

可以组合使用 Put 方法与唯一操作 ID 来为计划关联已经存在的操作组。  下面是用法的一个示例展示。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

可以组合使用 Put 方法与现有操作 ID 来为 计划修改关联的操作组。  请求正文必须包含操作的 etag。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>自定义操作
默认情况下，操作遵循用于通知的标准模板和格式。 但是，用户可以自定义某些操作，即使它们是由操作组控制的。 目前可以自定义电子邮件主题和 Webhook 有效负载。

##### <a name="customize-e-mail-subject-for-action-group"></a>自定义操作组的电子邮件主题
默认情况下，警报的电子邮件主题是：<WorkspaceName> 的警报通知 <AlertName>。 但这可以自定义，因此你可以指定词语或标签，以便轻松在收件箱中利用筛选规则。 自定义的电子邮件标题详细信息需要随操作组详细信息一起发送，如以下示例中所示。

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

可以组合使用 Put 方法与唯一操作 ID 来为计划将已经存在的操作组与自定义项进行关联。  下面是用法的一个示例展示。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

可以组合使用 Put 方法与现有操作 ID 来为 计划修改关联的操作组。  请求正文必须包含操作的 etag。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>自定义操作组的 Webhook 有效负载
默认情况下，通过 Log Analytics 的操作组发送的 Webhook 具有固定结构。 但是，可以使用受支持的特定变量来自定义 JSON 有效负载，以满足 Webhook 终结点的要求。 有关详细信息，请参阅[日志警报规则的 Webhook 操作](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)。 

自定义的 Webhook 详细信息需要随操作组详细信息一起发送，并且将应用于在操作组内指定的所有 Webhook URI，如以下示例中所示。

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

可以组合使用 Put 方法与唯一操作 ID 来为计划将已经存在的操作组与自定义项进行关联。  下面是用法的一个示例展示。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

可以组合使用 Put 方法与现有操作 ID 来为 计划修改关联的操作组。  请求正文必须包含操作的 etag。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>电子邮件通知
向一个或多个收件人发送邮件的电子邮件通知。  它们具有下表中的属性。

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制诸如电子邮件通知之类的操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 检索或添加操作。
   

| 属性 | 说明 |
|:--- |:--- |
| 收件人 |邮件地址列表。 |
| 主题 |邮件主题。 |
| 附件 |当前不支持附件，因此其值始终为“None”。 |

下面是响应仅具备一个阈值的电子邮件通知操作的示例。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新电子邮件操作。  下面的示例创建了具备一个阈值的电子邮件通知，因此当已保存搜索的结果超出阈值时会发送邮件。

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

结合使用 Put 方法和现有操作 ID 可修改计划的电子邮件操作。  请求正文必须包含操作的 etag。

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>修正操作
修正在 Azure 自动化中启动 Runbook，尝试纠正警报发现的问题。  必须为用于修正操作的 Runbook 创建 Webhook，并在 WebhookUri 属性中指定 URI。  使用 OMS 控制台创建此操作时，会自动为 Runbook 创建新的 Webhook。

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制诸如使用 runbook 进行修正之类的操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 检索或添加操作。

修正具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| RunbookName |Runbook 的名称。 它必须与 OMS 工作区自动化解决方案中配置的自动化帐户中已发布的 Runbook 相匹配。 |
| WebhookUri |Webhook 的 URI。 |
| Expiry |Webhook 的到期日期和时间。  如果 Webhook 没有到期日期和时间，那么这可以是任何有效的未来日期。 |

下面是响应仅具备一个阈值的修正操作的示例。

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新的修正操作。  下面的示例创建了具备一个阈值的修正，因此当已保存搜索的结果超出该阈值时便会启动 Runbook。

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

结合使用 Put 方法和现有操作 ID 可修改计划的修正操作。  请求正文必须包含操作的 etag。

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>示例
下面是创建新电子邮件警报的完整示例。  这会创建一个新计划以及包含阈值和电子邮件的操作。

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Webhook 操作
Webhook 操作通过调用 URL 和提供要发送的负载（可选）启动进程。  Webhook 操作与修正操作类似，不同之处在于它们是用于 Webhook，可能调用 Azure 自动化 Runbook 之外的进程。  此外，它们还提供了额外的选项，即提供要发送到远程进程的负载。

> [!NOTE]
> 从 2018 年 5 月 14 日开始，工作区中的所有警报都将自动扩展到 Azure。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制诸如 Webhook 之类的操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 检索或添加操作。


Webhook 操作没有阈值，但应添加到具有带阈值的警报操作的计划中。  

下面是响应 Webhook 操作和包含阀值的关联警报操作的示例。

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>创建或编辑 Webhook 操作
结合使用 Put 方法和唯一操作 ID 为计划创建新的 Webhook 操作。  下面的示例创建了 Webhook 操作和包含一个阈值的警报操作，因此已保存搜索的结果超出该阈值时会触发 Webhook。

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

结合使用 Put 方法和现有操作 ID 修改计划的 Webhook 操作。  请求正文必须包含操作的 etag。

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>后续步骤
* 在 Log Analytics 中使用 [REST API 执行日志搜索](log-analytics-log-search-api.md)。
* 了解 [Azure 警报中的日志警报](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

