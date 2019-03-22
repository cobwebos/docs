---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58137640"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的配置设置。

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则函数应用的默认任务中心名称是 **DurableFunctionsHub**。 有关详细信息，请参阅[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)。

|属性  |默认 | 描述 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|可以使用备用[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)名称将多个 Durable Functions 应用程序彼此隔离，即使这些应用程序使用同一存储后端。|
|controlQueueBatchSize|32|要从控制队列中一次性拉取的消息数。|
|partitionCount |4|控制队列的分区计数。 可以是 1 到 16 之间的正整数。|
|controlQueueVisibilityTimeout |5 分钟|已取消排队的控制队列消息的可见性超时。|
|workItemQueueVisibilityTimeout |5 分钟|已取消排队的工作项队列消息的可见性超时。|
|maxConcurrentActivityFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的活动函数的最大数目。|
|maxConcurrentOrchestratorFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的业务流程协调程序函数的最大数目。|
|maxQueuePollingInterval|30 秒|最大的控制和工作项队列中的轮询间隔*hh: mm:* 格式。 较高的值可能导致更高版本的消息处理延迟。 较低的值可能导致更高的存储事务由于存储成本较高。|
|azureStorageConnectionStringName |AzureWebJobsStorage|应用设置的名称，其中的 Azure 存储连接字符串用于管理基础的 Azure 存储资源。|
|trackingStoreConnectionStringName||若要使用的历史记录和实例表的连接字符串的名称。 如果未指定，`azureStorageConnectionStringName`使用连接。|
|trackingStoreNamePrefix||要使用的历史记录和实例的前缀表时`trackingStoreConnectionStringName`指定。 如果未设置，默认的前缀值将`DurableTask`。 如果`trackingStoreConnectionStringName`未指定，则将使用历史记录和实例表`hubName`值作为其前缀和任何设置`trackingStoreNamePrefix`将被忽略。|
|traceInputsAndOutputs |false|一个指示是否跟踪函数调用的输入和输出的值。 跟踪函数执行事件时的默认行为是在函数调用的序列化输入和输出中包括字节数。 此行为可提供有关输入和输出外观而无需扩展日志或无意中公开敏感信息的最少信息。 将此属性设置为 true 会导致默认函数日志记录将函数输入和输出的整个内容都记录下来。|
|logReplayEvents|false|一个值，该值指示是否将业务流程重播事件写入到 Application Insights。|
|eventGridTopicEndpoint ||Azure 事件网格自定义主题终结点的 URL。 在设置此属性，业务流程生命周期通知事件发布到此终结点。 此属性支持应用设置解析。|
|eventGridKeySettingName ||应用设置的名称，该设置包含的密钥用于在 `EventGridTopicEndpoint` 上通过 Azure 事件网格自定义主题进行身份验证。|
|eventGridPublishRetryCount|0|发布到事件网格主题失败时要重试的次数。|
|eventGridPublishRetryInterval|5 分钟|事件网格发布重试间隔（采用 *hh:mm:ss* 格式）。|
|eventGridPublishEventTypes||若要将发布到事件网格的事件类型的列表。 如果未指定，将发布所有事件类型。 允许值包括`Started`， `Completed`， `Failed`， `Terminated`。|

许多这些设置是为了优化性能。 有关详细信息，请参阅[性能和缩放](../articles/azure-functions/durable-functions-perf-and-scale.md)。