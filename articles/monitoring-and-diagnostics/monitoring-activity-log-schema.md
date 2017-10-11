---
title: "Azure 活动日志事件架构 | Microsoft Docs"
description: "了解发送到活动日志中的数据的事件架构"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.openlocfilehash: a4ceb822e0ec3e1c1dc31ece1db761834e795f6c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="azure-activity-log-event-schema"></a>Azure 活动日志事件架构
通过 Azure 活动日志，可以深入了解 Azure 中发生的任何订阅级别事件。 本文介绍了每种数据类别的事件架构。

## <a name="administrative"></a>管理
此类别包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 此类别中的事件类型的示例包括“创建虚拟机”和“删除网络安全组”。用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为“写入”、“删除”或“操作”，则该操作的开始、成功或失败记录都会记录在管理类别中。 管理类别还包括任何对订阅中基于角色的访问控制进行的更改。

### <a name="sample-event"></a>示例事件
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| authorization |包含事件的 RBAC 属性的 Blob。 通常包括“action”、“role”和“scope”属性。 |
| caller |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的电子邮件地址。 |
| channels |以下值之一：“Admin”、“Operation” |
| 声明 |Active Directory 使用 JWT 令牌来验证用户或应用程序，以在资源管理器中执行此操作。 |
| correlationId |通常为字符串格式的 GUID。 共享 correlationId 的事件属于同一 uber 操作。 |
| description |事件的静态文本说明。 |
| eventDataId |事件的唯一标识符。 |
| httpRequest |描述 Http 请求的 Blob。 通常包括“clientRequestId”、“clientIpAddress”和“method”（HTTP 方法。 例如 PUT）。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose” |
| resourceGroupName |受影响资源的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称 |
| resourceId |受影响资源的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| status |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus |通常为相应 REST 调用的 HTTP 状态代码，但也可能包括用于描述子状态的其他字符串，例如以下常用值：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）、Gateway Timeout（HTTP 状态代码：504）。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="service-health"></a>服务运行状况
此类别包含对任何发生在 Azure 中的服务运行状况事件的记录。 此类别的一个事件类型示例是“美国东部的 SQL Azure 正处于故障时间”。 服务运行状况事件分 5 种：必需操作、辅助恢复、事件、维护、信息或安全性，仅当订阅中存在受事件影响的资源时，它们才出现。

### <a name="sample-event"></a>示例事件
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>属性说明
元素名称 | 说明
-------- | -----------
channels | 为以下值之一：“Admin”、“Operation”
correlationId | 通常为字符串格式的 GUID。 属于同一 uber 操作的事件通常共享相同的 correlationId。
description | 事件说明。
eventDataId | 事件的唯一标识符。
eventName | 事件的标题。
级别 | 事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose”
resourceProviderName | 受影响资源的资源提供程序的名称。 如果未知，则为 NULL。
resourceType| 受影响资源的资源类型。 如果未知，则为 NULL。
subStatus | 对于服务运行状况事件，通常为 NULL。
eventTimestamp | 生成日志事件并将其提交至活动日志时的时间戳。
submissionTimestamp |   事件在活动日志中可用时的时间戳。
subscriptionId | 记录此事件的 Azure 订阅。
status | 描述操作状态的字符串。 某些常见值为：Active、Resolved。
operationName | 操作的名称。 通常是 Microsoft.ServiceHealth/incident/action。
category | "ServiceHealth"
resourceId | 如果已知，则为受影响资源的资源 ID。 否则将提供订阅 ID。
Properties.title | 此通信的本地化标题。 默认语言为英语。
Properties.communication | 带 HTML 标记的通信的经过本地化的详细信息。 默认为英语。
Properties.incidentType | 可能的值：AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | 标识与此事件关联的事件。 使用此属性将与某一事件相关的事件关联起来。
Properties.impactedServices | 转义 JSON blob，描述受事件影响的服务和区域。 服务列表，每个服务具有 ServiceName 和一个 ImpactedRegions（其中每个具有一个 RegionName）列表。
Properties.defaultLanguageTitle | 英语通信
Properties.defaultLanguageContent | html 标记或纯文本格式的英语通信
Properties.stage | 对于 AssistedRecovery、ActionRequired、Information、Incident、Security，可能的值为：Active、Resolved。 对于 Maintenance，可能值为：Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | 与此事件关联的通信。

## <a name="alert"></a>警报
此类别包含 Azure 警报的所有激活记录。 可在此类别中看到的事件类型示例如“过去 5 分钟内，myVM 上的 CPU 百分比已超过 80%”。 许多 Azure 系统都具有警报概念 - 可定义某种类型的规则，并在条件匹配该规则时接收通知。 每当支持的 Azure 警报类型“激活”或满足生成通知的条件时，激活记录也会推送到此类别的活动日志中。

### <a name="sample-event"></a>示例事件

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| caller | 始终是 Microsoft.Insights/alertRules |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或警报引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |警报事件的静态文本说明。 |
| eventDataId |警报事件的唯一标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose” |
| resourceGroupName |受影响资源的资源组的名称（如果是指标警报）。 对其它警报类型而言，这是包含警报本身的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称（如果是指标警报）。 对其它警报类型而言，这是警报本身的资源提供程序的名称。 |
| resourceId | 受影响资源的资源 ID 的名称（如果是指标警报）。 对其它警报类型而言，这是警报资源本身的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| status |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对警报而言通常为 NULL。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

### <a name="properties-field-per-alert-type"></a>每种警报类型的属性字段
该属性字段包含不同的值，具体取决于警报事件的源。 两种常见警报事件提供程序是活动日志警报和指标警报。

#### <a name="properties-for-activity-log-alerts"></a>活动日志警报的属性
| 元素名称 | 说明 |
| --- | --- |
| properties.subscriptionId | 激活活动日志预警规则的活动日志事件的订阅 ID。 |
| properties.eventDataId | 激活活动日志预警规则的活动日志事件的事件数据 ID。 |
| properties.resourceGroup | 激活活动日志预警规则的活动日志事件的资源组。 |
| properties.resourceId | 激活活动日志预警规则的活动日志事件的资源 ID。 |
| properties.eventTimestamp | 激活活动日志预警规则的活动日志事件的事件时间戳。 |
| properties.operationName | 激活活动日志预警规则的活动日志事件的操作名称。 |
| properties.status | 激活活动日志预警规则的活动日志事件的状态。|

#### <a name="properties-for-metric-alerts"></a>指标警报的属性
| 元素名称 | 说明 |
| --- | --- |
| properties.RuleUri | 指标预警规则自身的资源 ID。 |
| properties.RuleName | 指标预警规则的名称。 |
| properties.RuleDescription | 对指标预警规则的说明（如预警规则中的定义）。 |
| properties.Threshold | 计算指标预警规则所用的阈值。 |
| properties.WindowSizeInMinutes | 计算指标预警规则所用的窗口大小。 |
| properties.Aggregation | 在指标预警规则中定义的聚合类型。 |
| properties.Operator | 计算指标预警规则所用的条件运算符。 |
| properties.MetricName | 计算指标预警规则所用的指标的指标名称。 |
| properties.MetricUnit | 计算指标预警规则所用的指标的指标单位。 |

## <a name="autoscale"></a>自动缩放
此类别包含基于在订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的事件记录。 可在此类别中看到的事件类型示例如“自动缩放扩大操作失败”。 使用自动缩放，可在支持的资源类型中，通过自动缩放设置基于日期和/或负载（指标）数据来自动增加或减少实例的数量。 满足增加或减少条件时，开始、成功或失败的事件将记录到此类别中。

### <a name="sample-event"></a>示例事件
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | 说明 |
| --- | --- |
| caller | 始终是 Microsoft.Insights/autoscaleSettings |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或自动缩放引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |自动缩放事件的静态文本说明。 |
| eventDataId |自动缩放事件的唯一标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose” |
| resourceGroupName |自动缩放设置的资源组名称。 |
| resourceProviderName |自动缩放设置的资源提供程序名称。 |
| resourceId |自动缩放设置的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| properties.Description | 有关自动缩放引擎执行的操作的详细说明。 |
| properties.ResourceName | 受影响资源（正在执行缩放操作的资源）的资源 ID |
| properties.OldInstancesCount | 自动缩放操作生效前的实例数量。 |
| properties.NewInstancesCount | 自动缩放操作生效后的实例数量。 |
| properties.LastScaleActionTime | 自动缩放操作发生时的时间戳。 |
| status |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对自动缩放而言通常为 NULL。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="next-steps"></a>后续步骤
* [详细了解活动日志（以前称为审核日志）](monitoring-overview-activity-logs.md)
* [将 Azure 活动日志流式传输到事件中心](monitoring-stream-activity-logs-event-hubs.md)
