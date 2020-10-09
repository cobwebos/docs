---
title: 适用于 B2B 消息的自定义跟踪架构
description: 在 Azure 逻辑应用中创建自定义跟踪架构来监视 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76903050"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>创建用于监视 Azure 逻辑应用中的端到端工作流的自定义跟踪架构

Azure 逻辑应用提供内置跟踪功能，你可以为工作流的各个部分启用该功能。 但是，你可以设置自定义跟踪，用以记录工作流从头到尾的事件，例如，包含逻辑应用程序、BizTalk Server、SQL Server 或任何其他层的工作流。 本文提供的自定义代码可以用于逻辑应用外部的层。

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

| 属性 | 必须 | 类型 | 说明 |
|----------|----------|------|-------------|
| sourceType | 是 | String | 运行源的类型，允许的值如下：`Microsoft.Logic/workflows`、`custom` |
| source | 是 | 字符串或 JToken | 如果源类型为 `Microsoft.Logic/workflows`，则源信息必须遵循此架构。 如果源类型为 `custom`，则架构为 JToken。 |
| systemId | 是 | String | 逻辑应用系统 ID |
| runId | 是 | String | 逻辑应用运行 ID |
| operationName | 是 | String | 操作（例如操作或触发器）的名称 |
| repeatItemScopeName | 是 | String | 如果操作处于 `foreach` 或 `until` 循环内，则重复项名称 |
| repeatItemIndex | 是 | Integer | 表示操作处于 `foreach` 或 `until` 循环内，并且是重复的项索引编号。 |
| trackingId | 否 | String | 用来关联消息的跟踪 ID |
| correlationId | 否 | String | 用来关联消息的相关性 ID |
| ClientRequestId | 否 | String | 客户端可以填充此属性来关联消息 |
| EventLevel | 是 | String | 事件的级别 |
| EventTime | 是 | DateTime | 事件的时间，采用 UTC 格式：*YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | 是 | String | 跟踪记录的类型，仅允许以下值：`custom` |
| 记录 (record) | 是 | JToken | 仅采用 JToken 格式的自定义记录类型 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 了解有关[用 Azure Monitor 日志监视 B2B 消息的](../logic-apps/monitor-b2b-messages-log-analytics.md)详细信息