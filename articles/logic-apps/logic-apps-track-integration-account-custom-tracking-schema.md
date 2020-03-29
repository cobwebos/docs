---
title: B2B 消息的自定义跟踪架构
description: 创建自定义跟踪架构以监视 Azure 逻辑应用中的 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903050"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>创建自定义跟踪架构，监视 Azure 逻辑 A 中的端到端工作流

Azure 逻辑应用具有内置跟踪功能，您可以为工作流的某些部分启用该跟踪。 但是，您可以设置自定义跟踪，从工作流的开头到末尾记录事件，例如，包括逻辑应用、BizTalk Server、SQL Server 或任何其他层的工作流。 本文提供的自定义代码可以用于逻辑应用外部的层。

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
         "repeatItemIndex": ,
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
         "record": {}
      }
   ]
}
```

| properties | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| sourceType | 是 | String | 具有这些允许值的运行源的类型： `Microsoft.Logic/workflows``custom` |
| source | 是 | 字符串或 JToken | 如果源类型为`Microsoft.Logic/workflows`，则源信息需要遵循此架构。 如果源类型为`custom`，则架构是 JToken。 |
| systemId | 是 | String | 逻辑应用系统 ID |
| runId | 是 | String | 逻辑应用运行 ID |
| operationName | 是 | String | 操作的名称，例如操作或触发器 |
| repeatItemScopeName | 是 | String | 如果操作在 或`foreach``until`循环内，请重复项目名称 |
| repeatItemIndex | 是 | Integer | 指示操作在`foreach`或`until`循环中，是重复的物料索引编号。 |
| trackingId | 否 | String | 跟踪 ID 以关联邮件 |
| correlationId | 否 | String | 关联 ID 以关联消息 |
| ClientRequestId | 否 | String | 客户端可以填充此属性以关联消息 |
| EventLevel | 是 | String | 事件级别 |
| EventTime | 是 | DateTime | UTC 格式的事件时间 *：YYYY-MM-DDTHH：MM：SS.00000Z* |
| recordType | 是 | String | 仅具有此允许值的跟踪记录类型：`custom` |
| 记录 (record) | 是 | JToken | 仅具有 JToken 格式的自定义记录类型 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 了解有关使用[Azure 监视器日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)的信息