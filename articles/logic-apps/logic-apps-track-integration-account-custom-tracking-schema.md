---
title: B2B 消息的自定义跟踪架构
description: 创建自定义跟踪架构以在 Azure 逻辑应用中监视 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903050"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>创建用于监视 Azure 逻辑 A 中的端到端工作流的自定义跟踪架构

Azure 逻辑应用提供内置跟踪，你可以为工作流的各个部分启用这些跟踪。 但是，可以设置自定义跟踪，用于记录从开始到工作流的末尾的事件，例如包括逻辑应用、BizTalk Server、SQL Server 或任何其他层的工作流。 本文提供了可在逻辑应用外的层中使用的自定义代码。

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

| properties | 必选 | 键入 | description |
|----------|----------|------|-------------|
| sourceType | 用户帐户控制 | String | 使用以下允许值的运行源类型： "`Microsoft.Logic/workflows`"，`custom` |
| 源程序 | 用户帐户控制 | String 或 JToken | 如果源类型为 `Microsoft.Logic/workflows`，则源信息必须遵循此架构。 如果源类型为 `custom`，则架构为 JToken。 |
| systemId | 用户帐户控制 | String | 逻辑应用系统 ID |
| runId | 用户帐户控制 | String | 逻辑应用运行 ID |
| operationName | 用户帐户控制 | String | 操作的名称，例如操作或触发器 |
| repeatItemScopeName | 用户帐户控制 | String | 如果操作位于 `foreach`或 `until` 循环内，则重复项名称 |
| repeatItemIndex | 用户帐户控制 | 整数 | 指示操作位于 `foreach` 或 `until` 循环内，是重复项索引号。 |
| trackingId | No | String | 用于关联消息的跟踪 ID |
| Id | No | String | 关联消息的相关 ID |
| clientRequestId | No | String | 客户端可以填充此属性以关联消息 |
| eventLevel | 用户帐户控制 | String | 事件级别 |
| eventTime | 用户帐户控制 | DateTime | 事件的时间（UTC 格式： *YYYY-MM-yyyy-mm-ddthh： MM： SS. 00000Z* |
| recordType | 用户帐户控制 | String | 仅具有此允许值的音轨记录的类型： `custom` |
| 记录 | 用户帐户控制 | JToken | 仅具有 JToken 格式的自定义记录类型 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 了解有关[用 Azure Monitor 日志监视 B2B 消息的](../logic-apps/monitor-b2b-messages-log-analytics.md)详细信息