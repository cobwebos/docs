---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 284aad7dd5b51268b1c8ff8a02f4489d6f1cd3d9
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279475"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的配置设置。

### <a name="durable-functions-1x"></a>Durable Functions 1。x

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
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2-0-host-json"></a>Durable Functions 2。x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则函数应用的默认任务中心名称是 **DurableFunctionsHub**。 有关详细信息，请参阅[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)。

|属性  |默认 | Description |
|---------|---------|---------|
|hubName|DurableFunctionsHub|可以使用备用[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)名称将多个 Durable Functions 应用程序彼此隔离，即使这些应用程序使用同一存储后端。|
|controlQueueBatchSize|32|要从控制队列中一次性拉取的消息数。|
|controlQueueBufferThreshold|256|可以在内存中一次缓冲的控制队列消息数，在此时间点，调度程序将等待，然后再将任何其他消息出列。|
|partitionCount |4|控制队列的分区计数。 可以是 1 到 16 之间的正整数。|
|controlQueueVisibilityTimeout |5 分钟|已取消排队的控制队列消息的可见性超时。|
|workItemQueueVisibilityTimeout |5 分钟|已取消排队的工作项队列消息的可见性超时。|
|maxConcurrentActivityFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的活动函数的最大数目。|
|maxConcurrentOrchestratorFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的业务流程协调程序函数的最大数目。|
|maxQueuePollingInterval|30 秒|*Hh： mm： ss*格式的最大控制和工作项队列轮询间隔。 较高的值可能会导致更高的消息处理延迟。 由于增加了存储事务，因此值越低，存储成本就越高。|
|azureStorageConnectionStringName |AzureWebJobsStorage|应用设置的名称，其中的 Azure 存储连接字符串用于管理基础的 Azure 存储资源。|
|trackingStoreConnectionStringName||用于历史记录和实例表的连接字符串的名称。 如果未指定，则使用 `azureStorageConnectionStringName` 连接。|
|trackingStoreNamePrefix||指定 `trackingStoreConnectionStringName` 时用于历史记录和实例表的前缀。 如果未设置，将 `DurableTask`默认前缀值。 如果未指定 `trackingStoreConnectionStringName`，则 "历史记录" 和 "实例" 表将使用 `hubName` 值作为其前缀，并且将忽略 `trackingStoreNamePrefix` 的任何设置。|
|traceInputsAndOutputs |false|一个指示是否跟踪函数调用的输入和输出的值。 跟踪函数执行事件时的默认行为是在函数调用的序列化输入和输出中包括字节数。 此行为提供有关输入和输出内容的最小信息，而无需扩展日志或无意中公开敏感信息。 将此属性设置为 true 会导致默认函数日志记录将函数输入和输出的整个内容都记录下来。|
|logReplayEvents|false|一个值，该值指示是否将业务流程重播事件写入到 Application Insights。|
|eventGridTopicEndpoint ||Azure 事件网格自定义主题终结点的 URL。 设置此属性时，业务流程生命周期通知事件会发布到此终结点。 此属性支持应用设置解析。|
|eventGridKeySettingName ||应用设置的名称，该设置包含的密钥用于在 `EventGridTopicEndpoint` 上通过 Azure 事件网格自定义主题进行身份验证。|
|eventGridPublishRetryCount|0|发布到事件网格主题失败时要重试的次数。|
|eventGridPublishRetryInterval|5 分钟|事件网格发布重试间隔（采用 *hh:mm:ss* 格式）。|
|eventGridPublishEventTypes||要发布到事件网格的事件类型的列表。 如果未指定，则将发布所有事件类型。 允许的值包括 `Started`、`Completed`、`Failed``Terminated`。|
|useGracefulShutdown|false|效果启用正常关闭，以减少主机关闭进程内函数执行失败的几率。|

其中的许多设置都是为了优化性能。 有关详细信息，请参阅[性能和缩放](../articles/azure-functions/durable-functions-perf-and-scale.md)。
