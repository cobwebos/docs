---
title: Azure 事件中心诊断日志 | Microsoft 文档
description: 了解如何为 Azure 中的事件中心设置诊断日志。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: d4ed53cf1f0d21e488631434b60309602169b59a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004644"
---
# <a name="event-hubs-diagnostic-logs"></a>事件中心诊断日志

可以查看两种类型的 Azure 事件中心日志：

* **[活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**：这些日志包含对作业执行的操作的相关信息。 始终启用这些日志。
* **[诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**：可配置诊断日志，更深入地了解作业发生的所有情况。 诊断日志涵盖从创建作业开始到删除作业为止的所有活动，其中包括作业运行时发生的更新和活动。

## <a name="enable-diagnostic-logs"></a>启用诊断日志

诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1.  在[Azure 门户](https://portal.azure.com)中的“监视 + 管理”下，点击“诊断日志”。

    ![在窗格中导航到诊断日志](./media/event-hubs-diagnostic-logs/image1.png)

2.  单击想要监视的资源。

3.  单击“启用诊断”。

    ![启用诊断日志](./media/event-hubs-diagnostic-logs/image2.png)

4.  对于“状态”，单击“打开”。

    ![更改诊断日志的状态](./media/event-hubs-diagnostic-logs/image3.png)

5.  设置所需的存档目标；例如存储帐户、事件中心或 Azure Log Analytics。

6.  保存新的诊断设置。

新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”窗格上配置的存档目标中。

有关配置诊断的详细信息，请参阅 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。

## <a name="diagnostic-logs-categories"></a>诊断日志类别

事件中心会捕获两种类别的诊断日志：

* **存档日志**：与事件中心存档相关（特别是与存档错误相关）的日志。
* **运行日志**：有关事件中心操作期间发生的事件的信息，具体而言，就是包括事件中心创建在内的操作类型、所使用的资源和操作的状态。

## <a name="diagnostic-logs-schema"></a>诊断日志架构

所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用以下部分所述的格式。

### <a name="archive-logs-schema"></a>存档日志架构

存档日志 JSON 字符串包括下表列出的元素：

名称 | Description
------- | -------
TaskName | 失败的任务的说明。
ActivityId | 用于跟踪的内部 ID。
trackingId | 用于跟踪的内部 ID。
resourceId | Azure 资源管理器资源 ID。
eventHub | 事件中心的完整名称（包括命名空间名称）。
partitionId | 写入到的事件中心分区。
archiveStep | ArchiveFlushWriter
startTime | 失败开始时间。
失败 | 发生失败的次数。
durationInSeconds | 失败持续时间。
message | 错误消息。
category | ArchiveLogs

以下代码是存档日志 JSON 字符串的示例：

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>运行日志架构

运行日志 JSON 字符串包括下表列出的元素：

名称 | Description
------- | -------
ActivityId | 用于跟踪目的的内部 ID。
EventName | 操作名称。  
resourceId | Azure 资源管理器资源 ID。
SubscriptionId | 订阅 ID。
EventTimeString | 操作时间。
EventProperties | 操作属性。
状态 | 操作状态。
调用方 | 操作的调用方（Azure 门户或管理客户端）。
category | OperationalLogs

以下代码是运行日志 JSON 字符串的示例：

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>后续步骤
* [事件中心简介](event-hubs-what-is-event-hubs.md)
* [事件中心 API 概述](event-hubs-api-overview.md)
* [事件中心入门](event-hubs-dotnet-standard-getstarted-send.md)
