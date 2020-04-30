---
title: 设置诊断日志 - Azure 事件中心 | Microsoft Docs
description: 了解如何为 Azure 中的事件中心设置活动日志和诊断日志。
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
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509751"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>设置 Azure 事件中心的诊断日志

可以查看两种类型的 Azure 事件中心日志：

* **[活动日志](../azure-monitor/platform/platform-logs-overview.md)**：这些日志包含对作业执行的操作的相关信息。 这些日志始终启用。 可以通过在 Azure 门户的事件中心命名空间的左窗格中选择 "**活动日志**" 来查看活动日志条目。 例如： "创建或更新命名空间"，"创建或更新事件中心"。

    ![事件中心命名空间的活动日志](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[诊断日志](../azure-monitor/platform/platform-logs-overview.md)**：可以配置诊断日志，以便更深入地了解作业发生的所有情况。 诊断日志涵盖从创建作业开始到删除作业为止的所有活动，其中包括作业运行时发生的更新和活动。

    以下部分说明如何为事件中心命名空间启用诊断日志。

## <a name="enable-diagnostic-logs"></a>启用诊断日志
诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1.  在 [Azure 门户](https://portal.azure.com)中，导航到你的事件中心命名空间。 
2. 在左窗格中选择 "**监视**" 下的 "**诊断设置**"，然后选择 " **+ 添加诊断设置**"。 

    !["诊断设置" 页-添加诊断设置](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. 在 "**类别详细信息**" 部分中，选择要启用的**诊断日志类型**。 稍后会在本文中找到有关这些类别的详细信息。 
5. 在 "**目标详细信息**" 部分中，设置所需的存档目标（目标）;例如，存储帐户、事件中心或 Log Analytics 工作区。

    !["添加诊断设置" 页](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  在工具栏上选择 "**保存**" 以保存诊断设置。

    新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”窗格上配置的存档目标中****。

    有关配置诊断的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="diagnostic-logs-categories"></a>诊断日志类别

事件中心捕获以下类别的诊断日志：

- **存档日志**：与事件中心存档相关（特别是与存档错误相关）的日志。
- **操作日志**：有关事件中心操作期间发生的事件的信息，具体而言，就是包括事件中心创建在内的操作类型、所使用的资源和操作的状态。
- **自动缩放日志**：有关对事件中心命名空间执行的自动缩放操作的信息。 
- **Kafka 协调器日志**-有关与事件中心相关的 Kafka 协调器操作的信息。 
- **Kafka 用户日志**：有关与事件中心相关的 Kafka 用户操作的信息。 
- **事件中心虚拟网络（VNet）连接事件**：有关事件中心虚拟网络连接事件的信息。 
- **客户托管的密钥用户日志**：有关与客户托管密钥相关的操作的信息。 


    所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用以下部分所述的格式。

## <a name="archive-logs-schema"></a>存档日志架构

存档日志 JSON 字符串包括下表列出的元素：

名称 | 说明
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
消息 | 错误消息。
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

## <a name="operational-logs-schema"></a>操作日志架构

运行日志 JSON 字符串包括下表列出的元素：

名称 | 说明
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

## <a name="autoscale-logs-schema"></a>自动缩放日志架构
自动缩放日志 JSON 包含下表中列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| trackingId | 用于跟踪的内部 ID |
| resourceId | 内部 ID，其中包含 Azure 订阅 ID 和命名空间名称 |
| 消息 | 信息性消息，提供有关自动膨胀操作的详细信息。 此消息包含给定命名空间的吞吐量单位的以前值和当前值，并触发了 TU 的陀螺形。 |

## <a name="kafka-coordinator-logs-schema"></a>Kafka 协调器日志架构
Kafka 协调器日志 JSON 包含下表中列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| requestId | 请求 ID，用于跟踪目的 |
| resourceId | 内部 ID，其中包含 Azure 订阅 ID 和命名空间名称 |
| operationName | 在组协调期间完成的操作的名称 |
| clientId | 客户端 ID |
| namespaceName | 命名空间名称 | 
| subscriptionId | Azure 订阅 ID |
| 消息 | 信息性消息，提供有关在使用者组协调期间完成的操作的详细信息。 |

## <a name="kafka-user-error-logs-schema"></a>Kafka 用户错误日志架构
Kafka 用户错误日志 JSON 包含下表中列出的元素：

| 名称 | 说明 |
| ---- | ----------- |
| trackingId | 跟踪 ID，用于跟踪目的。 |
| namespaceName | 命名空间名称 |
| eventhub | 事件中心名称 |
| partitionId | Partition ID |
| groupId | 组 ID |
| ClientId | 客户端 ID |
| resourceId | 内部 ID，其中包含 Azure 订阅 ID 和命名空间名称 |
| 消息 | 信息性消息，提供有关错误的详细信息 |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>事件中心虚拟网络连接事件架构

事件中心虚拟网络（VNet）连接事件 JSON 包含下表中列出的元素：

| 名称 | 说明 |
| ---  | ----------- | 
| subscriptionId | Azure 订阅 ID |
| namespaceName | 命名空间名称 |
| ipAddress | 连接到事件中心服务的客户端的 IP 地址 |
| action | 事件中心服务在评估连接请求时执行的操作。 支持的操作是**AcceptConnection**和**RejectConnection**。 |
| reason | 提供操作执行的原因 |
| 计数 | 给定操作的出现次数 |
| resourceId | 内部资源 ID，其中包含订阅 ID 和命名空间名称。 |

## <a name="customer-managed-key-user-logs"></a>客户托管的密钥用户日志
客户托管的密钥用户日志 JSON 包括下表中列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| category | 消息类别的类型。 它是下列值之一：**错误**和**信息** |
| resourceId | 内部资源 ID，包括 Azure 订阅 ID 和命名空间名称 |
| keyVault | Key Vault 资源的名称 |
| key | Key Vault 密钥的名称。 |
| version | Key Vault 密钥的版本 |
| operation | 为请求服务而执行的操作的名称 |
| code | 状态代码 |
| 消息 | 消息，提供有关错误或信息性消息的详细信息 |



## <a name="next-steps"></a>后续步骤
- [事件中心简介](event-hubs-what-is-event-hubs.md)
- [事件中心 API 概述](event-hubs-api-overview.md)
- 事件中心入门
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
