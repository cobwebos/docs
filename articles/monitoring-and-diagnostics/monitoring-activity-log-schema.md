---
title: Azure 活动日志事件架构
description: 了解发送到活动日志中的数据的事件架构
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: 9c1f4699f067ece3108813d28ff834c68f44316d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003825"
---
# <a name="azure-activity-log-event-schema"></a>Azure 活动日志事件架构
通过 Azure 活动日志，可以深入了解 Azure 中发生的任何订阅级别事件。 本文介绍了每种数据类别的事件架构。 数据架构各有不同，具体取决于是在门户、PowerShell、CLI，或直接通过 REST API 读取数据，还是[使用日志配置文件将数据流式传输到存储或事件中心](./monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。 以下示例显示的是通过门户、PowerShell、CLI 和 REST API 获得的架构。 本文末尾提供了这些属性到 [Azure 诊断日志架构](./monitoring-diagnostic-logs-schema.md)的映射。

## <a name="administrative"></a>管理
此类别包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 此类别中的事件类型的示例包括“创建虚拟机”和“删除网络安全组”。用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为“写入”、“删除”或“操作”，则该操作的开始、成功或失败记录都会记录在管理类别中。 管理类别还包括任何对订阅中基于角色的访问控制进行的更改。

### <a name="sample-event"></a>示例事件
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | Description |
| --- | --- |
| authorization |包含事件的 RBAC 属性的 Blob。 通常包括“action”、“role”和“scope”属性。 |
| caller |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的电子邮件地址。 |
| channels |以下值之一：“Admin”、“Operation” |
| 声明 |Active Directory 使用 JWT 令牌来验证用户或应用程序，以在资源管理器中执行此操作。 |
| correlationId |通常为字符串格式的 GUID。 共享 correlationId 的事件属于同一 uber 操作。 |
| description |事件的静态文本说明。 |
| eventDataId |事件的唯一标识符。 |
| httpRequest |描述 Http 请求的 Blob。 通常包括“clientRequestId”、“clientIpAddress”和“method”（HTTP 方法。 例如 PUT）。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |受影响资源的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称 |
| resourceId |受影响资源的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| 属性 |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
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
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
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
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
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
请参阅[服务运行状况通知](./monitoring-service-notifications.md)一文，获取有关属性的值的说明。

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
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
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
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
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
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | Description |
| --- | --- |
| caller | 始终是 Microsoft.Insights/alertRules |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或警报引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |警报事件的静态文本说明。 |
| eventDataId |警报事件的唯一标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |受影响资源的资源组的名称（如果是指标警报）。 对其它警报类型而言，这是包含警报本身的资源组的名称。 |
| resourceProviderName |受影响资源的资源提供程序的名称（如果是指标警报）。 对其它警报类型而言，这是警报本身的资源提供程序的名称。 |
| resourceId | 受影响资源的资源 ID 的名称（如果是指标警报）。 对其它警报类型而言，这是警报资源本身的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| 属性 |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
| status |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对警报而言通常为 NULL。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

### <a name="properties-field-per-alert-type"></a>每种警报类型的属性字段
该属性字段包含不同的值，具体取决于警报事件的源。 两种常见警报事件提供程序是活动日志警报和指标警报。

#### <a name="properties-for-activity-log-alerts"></a>活动日志警报的属性
| 元素名称 | Description |
| --- | --- |
| properties.subscriptionId | 激活活动日志预警规则的活动日志事件的订阅 ID。 |
| properties.eventDataId | 激活活动日志预警规则的活动日志事件的事件数据 ID。 |
| properties.resourceGroup | 激活活动日志预警规则的活动日志事件的资源组。 |
| properties.resourceId | 激活活动日志预警规则的活动日志事件的资源 ID。 |
| properties.eventTimestamp | 激活活动日志预警规则的活动日志事件的事件时间戳。 |
| properties.operationName | 激活活动日志预警规则的活动日志事件的操作名称。 |
| properties.status | 激活活动日志预警规则的活动日志事件的状态。|

#### <a name="properties-for-metric-alerts"></a>指标警报的属性
| 元素名称 | Description |
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
此类别包含基于在订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的事件记录。 可在此类别中看到的事件类型示例如“自动缩放扩展操作失败”。 使用自动缩放，可在支持的资源类型中，通过自动缩放设置基于日期和/或负载（指标）数据来自动增加或减少实例的数量。 满足纵向扩展或缩减条件时，开始、成功或失败的事件将记录到此类别中。

### <a name="sample-event"></a>示例事件
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
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
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
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
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | Description |
| --- | --- |
| caller | 始终是 Microsoft.Insights/autoscaleSettings |
| channels | 始终是“Admin, Operation” |
| 声明 | 具有 SPN（服务主体名称）的 JSON blob，或自动缩放引擎资源类型。 |
| correlationId | 字符串格式的 GUID。 |
| description |自动缩放事件的静态文本说明。 |
| eventDataId |自动缩放事件的唯一标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”和“Informational” |
| resourceGroupName |自动缩放设置的资源组名称。 |
| resourceProviderName |自动缩放设置的资源提供程序名称。 |
| resourceId |自动缩放设置的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| 属性 |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |
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

## <a name="security"></a>安全
此类别包含 Azure 安全中心生成的任何警报记录。 可在此类别中看到的事件类型示例为“执行了可疑的双扩展名文件”。

### <a name="sample-event"></a>示例事件
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>属性说明
| 元素名称 | Description |
| --- | --- |
| channels | 始终为“运行” |
| correlationId | 字符串格式的 GUID。 |
| description |安全事件的静态文本说明。 |
| eventDataId |安全事件的唯一标识符。 |
| eventName |安全事件的友好名称。 |
| id |安全事件的唯一资源标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”或“Informational” |
| resourceGroupName |资源的资源组名称。 |
| resourceProviderName |Azure 安全中心的资源提供程序名称。 始终为“Microsoft.Security”。 |
| resourceType |生成安全事件的资源的类型，如“Microsoft.Security/locations/alerts” |
| resourceId |安全警报的资源 ID。 |
| operationId |在多个事件（对应于单个操作）之间共享的 GUID。 |
| operationName |操作的名称。 |
| 属性 |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 这些属性将因安全警报的类型而异。 有关来自安全中心的警报类型的说明，请参阅[此页](../security-center/security-center-alerts-type.md)。 |
| properties.Severity |严重性级别。 可能的值为“High”、“Medium”或“Low”。 |
| status |描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 对于安全事件通常为 null。 |
| eventTimestamp |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |

## <a name="recommendation"></a>建议
此类别包含为服务生成的任何新建议的记录。 建议的示例将为“使用可用性集提高容错能力”。 可以生成 4 种类型的建议事件：高可用性、性能、安全性和成本优化。 

### <a name="sample-event"></a>示例事件
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>属性说明
| 元素名称 | Description |
| --- | --- |
| channels | 始终为“运行” |
| correlationId | 字符串格式的 GUID。 |
| description |建议事件的静态文本说明 |
| eventDataId | 建议事件的唯一标识符。 |
| category | 始终为“Recommendation” |
| id |建议事件的唯一资源标识符。 |
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”或“Informational” |
| operationName |操作的名称。  始终为“Microsoft.Advisor/generateRecommendations/action”|
| resourceGroupName |资源的资源组名称。 |
| resourceProviderName |此建议适用的资源的资源提供程序名称，例如“MICROSOFT.COMPUTE” |
| resourceType |此建议适用的资源的资源类型名称，例如“MICROSOFT.COMPUTE/virtualmachines” |
| resourceId |此建议适用的资源的资源 ID |
| status | 始终为“Active” |
| submissionTimestamp |事件可供查询的时间戳。 |
| subscriptionId |Azure 订阅 ID。 |
| 属性 |`<Key, Value>` 对集（即字典），描述建议的详细信息。|
| properties.recommendationSchemaVersion| 在活动日志条目中发布的建议属性的架构版本 |
| properties.recommendationCategory | 建议的类别。 可能的值为“High Availability”、“Performance”、“Security”和“Cost” |
| properties.recommendationImpact| 建议的影响。 可能的值为“High”、“Medium”、“Low” |
| properties.recommendationRisk| 建议的风险。 可能的值为“Error”、“Warning”、“None” |

## <a name="mapping-to-diagnostic-logs-schema"></a>映射到诊断日志架构

将 Azure 活动日志流式传输到存储帐户或事件中心命名空间时，数据将遵循 [Azure 诊断日志架构](./monitoring-diagnostic-logs-schema.md)。 以下是从上述架构到诊断日志架构的属性映射：

| 诊断日志架构属性 | 活动日志 REST API 架构属性 | 说明 |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId、resourceType 和 resourceGroupName 都是从 resourceId 推断而来。 |
| operationName | operationName.value |  |
| category | 操作名称的一部分 | 操作类型分类：“写入”/“删除”/“操作” |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | 不适用 | 始终为 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | 声明和授权属性 |  |
| 级别 | 级别 |  |
| location | 不适用 | 处理事件的位置。 这不是资源所在位置，而是处理事件的位置。未来更新中将删除此属性。 |
| 属性 | properties.eventProperties |  |
| properties.eventCategory | category | 如果不存在 properties.eventCategory，则 category 是“管理” |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | 属性 |  |


## <a name="next-steps"></a>后续步骤
* [详细了解活动日志（以前称为审核日志）](monitoring-overview-activity-logs.md)
* [将 Azure 活动日志流式传输到事件中心](monitoring-stream-activity-logs-event-hubs.md)
