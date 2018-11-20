---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251237"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的配置设置。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则函数应用的默认任务中心名称是 **DurableFunctionsHub**。 有关详细信息，请参阅[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)。

|属性  |默认 | Description |
|---------|---------|---------|
|HubName|DurableFunctionsHub|可以使用备用[任务中心](../articles/azure-functions/durable-functions-task-hubs.md)名称将多个 Durable Functions 应用程序彼此隔离，即使这些应用程序使用同一存储后端。|
|ControlQueueBatchSize|32|要从控制队列中一次性拉取的消息数。|
|PartitionCount |4|控制队列的分区计数。 可以是 1 到 16 之间的正整数。|
|ControlQueueVisibilityTimeout |5 分钟|已取消排队的控制队列消息的可见性超时。|
|WorkItemQueueVisibilityTimeout |5 分钟|已取消排队的工作项队列消息的可见性超时。|
|MaxConcurrentActivityFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的活动函数的最大数目。|
|MaxConcurrentOrchestratorFunctions |10 倍于当前计算机上的处理器数|可以在单个主机实例上并发处理的业务流程协调程序函数的最大数目。|
|AzureStorageConnectionStringName |AzureWebJobsStorage|应用设置的名称，其中的 Azure 存储连接字符串用于管理基础的 Azure 存储资源。|
|TraceInputsAndOutputs |false|一个指示是否跟踪函数调用的输入和输出的值。 跟踪函数执行事件时的默认行为是在函数调用的序列化输入和输出中包括字节数。 这样提供的有关输入和输出情况的信息是最少的，不会导致日志膨胀，也不会无意中将敏感信息公开给日志。 将此属性设置为 true 会导致默认函数日志记录将函数输入和输出的整个内容都记录下来。|
|LogReplayEvents|false|一个值，该值指示是否将业务流程重播事件写入到 Application Insights。|
|EventGridTopicEndpoint ||Azure 事件网格自定义主题终结点的 URL。 设置此属性后，业务流程生命周期通知事件就会发布到此终结点。 此属性支持应用设置解析。|
|EventGridKeySettingName ||应用设置的名称，该设置包含的密钥用于在 `EventGridTopicEndpoint` 上通过 Azure 事件网格自定义主题进行身份验证。|
|EventGridPublishRetryCount|0|发布到事件网格主题失败时要重试的次数。|
|EventGridPublishRetryInterval|5 分钟|事件网格发布重试间隔（采用 *hh:mm:ss* 格式）。|

许多这样的项用于优化性能。 有关详细信息，请参阅[性能和缩放](../articles/azure-functions/durable-functions-perf-and-scale.md)。