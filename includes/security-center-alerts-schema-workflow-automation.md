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
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400964"
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

|字段|数据类型|说明|
|----|----|----|
|**AlertDisplayName**|字符串|警报的显示名称。|
|**AlertType**|字符串|警报的类型。 同一类型的警报应具有相同的值。 此字段是表示警报类型的键控字符串，而不是警报实例的类型。 同一检测逻辑/分析中的所有警报实例都应有相同的警报类型值。|
|**CompromisedEntity**|字符串|与此警报相关的资源的显示名称。|
|**描述**|字符串|警报的说明。|
|**EndTimeUtc**|DateTime|警报中包含的最后一个事件或活动的时间。  字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**实体**|IEnumerable (IEntity) |与警报相关的实体的列表。 此列表可以混合使用各种类型的实体。 实体类型可以是 "实体" 部分中定义的任何类型。 还可以发送不在下面的列表中的实体，但是不能保证会对它们进行处理 (警报不会通过新类型的实体) 验证。|
|**ExtendedProperties**|Dictionary (String，String) |提供程序 (可以选择) 在此处包含自定义字段。|
|**意向**|枚举|警报后的终止链相关意向。 有关支持的值的列表以及 Azure 安全中心支持的 kill 链式方法的说明，请参阅 [意图](../articles/security-center/alerts-reference.md#intentions)。<br/>此字段可能具有多个值 (用逗号分隔) 。|
|**IsIncident**|Bool|此字段确定警报是 (几个警报的复合分组) 还是单个警报的事件。 该字段的默认值为 "false" (这意味着它是单个警报) 。|
|**ProcessingEndTime**|DateTime|在保存警报的原始产品中，最终用户可以访问警报的时间。|
|**ProductName**|字符串| (Azure 安全中心、Azure ATP、Microsoft Defender ATP、MCAS 等) 发布此警报的产品名称。|
|**RemediationSteps**|List<String>|手动纠正警报的操作项。|
|**ResourceIdentifiers**|列出 (资源标识符) |此警报的资源标识符，可用于将警报定向到正确的产品公开组 (租户、工作区、订阅等 ) 。 每个警报可有多个不同类型的标识符。|
|**严重性**|枚举|提供程序报告的警报的严重性。 可能的值：信息、低、中和高。|
|**StartTimeUtc**|DateTime|警报中包含的第一个事件或活动的时间。 字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**Status**|枚举|警报的生命周期状态。<br/>支持的状态包括：新增、已解决、已消除、未知。<br/>指定的值不是受支持选项的警报会被分配状态 "未知"。<br/>未指定值的警报将被分配状态 "New"。|
|**SystemAlertId**|字符串|警报标识符。|
|**TimeGenerated**|DateTime|警报提供程序生成警报的时间。 如果内部警报提供程序未报告，则产品可选择分配接收时间以供产品处理。  字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**VendorName**|字符串|引发警报的供应商的名称。|
|||
