---
title: include 文件
description: include 文件
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272744"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|数据类型|描述|
|----|----|----|
|**警报显示名称**|String|警报的显示名称。|
|**AlertType**|String|警报的类型。 相同类型的警报应具有相同的值。 此字段是表示警报类型的键控字符串，而不是警报实例的类型。 来自同一检测逻辑/分析的所有警报实例都应具有相同的警报类型的值。|
|**泄露的实体**|String|与此警报最相关的资源的显示名称。|
|**说明**|String|警报的说明。|
|**结束时间Utc**|DateTime|警报中包含的最后一个事件或活动的时间。  该字段应为符合 ISO8601 格式（包括 UTC 时区信息）的字符串。|
|**实体**|IE500（IEntity）|与警报相关的实体列表。 此列表可以容纳不同类型的实体的混合。 实体类型可以是"实体"部分中定义的任何类型。 也可以发送不在下面列表中的实体，但不保证它们将被处理（警报不会失败，使用新类型的实体进行验证）。|
|**扩展属性**|字典（字符串，字符串）|提供程序可能（可选）在此处包含自定义字段。|
|**意图**|枚举|警报后面的终止链相关意图。 有关支持的值的列表以及 Azure 安全中心支持的终止链意图的说明，请参阅[意图](../articles/security-center/alerts-reference.md#intentions)。<br/>此字段可能具有多个值（用逗号分隔）。|
|**正在发生**|Bool|此字段确定警报是事件（多个警报的复合分组）还是单个警报。 字段的默认值为"false"（表示它是单个警报）。|
|**ProcessingEndTime**|DateTime|持有警报的原始产品中的最终用户可访问警报的时间。|
|ProductName****|String|发布此警报的产品的名称（Azure 安全中心、Azure ATP、微软防御者 ATP、O365 ATP、MCAS 等）。|
|**补救步骤**|列表<String>|要执行手动操作项来修复警报。|
|**资源标识符**|列表（资源标识符）|可用于将警报定向到正确的产品暴露组（租户、工作区、订阅等）的资源标识符。 每个警报可以有多个不同类型的标识符。|
|**严重性**|枚举|提供程序报告的警报的严重性。 可能的值：信息、低、中和高。|
|**开始时间Utc**|DateTime|警报中包含的第一个事件或活动的时间。 该字段应为符合 ISO8601 格式（包括 UTC 时区信息）的字符串。|
|**状态**|枚举|警报的生命周期状态。<br/>支持的状态为：新建、已解决、已取消、未知。<br/>指定受支持选项以外的值的警报将分配状态为"未知"。<br/>未指定值的警报将分配状态为"新建"。|
|**系统警报 Id**|String|警报标识符。|
|**TimeGenerated**|DateTime|警报提供程序生成警报的时间。 如果未由内部警报提供程序报告，则产品可以选择分配产品接收的时间进行处理。  该字段应为符合 ISO8601 格式（包括 UTC 时区信息）的字符串。|
|**供应商名称**|String|引发警报的供应商的名称。|
|||
