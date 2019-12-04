---
title: B2B 消息的自定义跟踪架构
description: 为带有 Enterprise Integration Pack 的 Azure 逻辑应用创建用于监视集成帐户中的 B2B 消息的自定义跟踪架构
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792793"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>创建用于监视 Azure 逻辑应用中的端到端工作流的自定义跟踪架构

可以为企业到企业工作流的不同部分启用内置跟踪，例如跟踪 AS2 或 X12 消息。 当创建的工作流包含逻辑应用、BizTalk Server、SQL Server 或任何其他层时，用户可以启用自定义跟踪，以便从头至尾记录工作流的事件。 

本文提供的自定义代码可以用于逻辑应用外部的层。 

## <a name="custom-tracking-schema"></a>自定义跟踪架构

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| properties | 需要 | Type | 描述 |
| --- | --- | --- | --- |
| sourceType | 是 |   | 运行的源的类型。 允许的值为“Microsoft.Logic/workflows”和“自定义”。 |
| source | 是 |   | 如果源类型为 **Microsoft.Logic/workflows**，则源信息必须遵循此架构。 如果源类型为“自定义”；架构为 JToken。 |
| systemId | 是 | 字符串 | 逻辑应用系统 ID。 |
| runId | 是 | 字符串 | 逻辑应用运行 ID。 |
| operationName | 是 | 字符串 | 操作（例如操作或触发）的名称。 |
| repeatItemScopeName | 是 | 字符串 | 如果操作位于 `foreach`/`until` 循环内，请重复项名称。 |
| repeatItemIndex | 是 | Integer | 操作是否位于 `foreach`/`until` 循环内。 指示重复的项索引。 |
| trackingId | No | 字符串 | 跟踪 ID，用于关联消息。 |
| correlationId | No | 字符串 | 相关性 ID，用于关联消息。 |
| ClientRequestId | No | 字符串 | 客户端可以填充它以关联消息。 |
| EventLevel | 是 |   | 事件的级别。 |
| EventTime | 是 |   | 事件的时间（UTC 格式 YYYY-MM-DDTHH:MM:SS.00000Z）。 |
| recordType | 是 |   | 跟踪记录的类型。 允许的值为“自定义”。 |
| record | 是 |   | 自定义记录类型。 允许的格式为 JToken。 |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 详细了解如何[监视 B2B 消息](logic-apps-monitor-b2b-message.md)
* 了解如何[在 Azure Monitor 日志中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
