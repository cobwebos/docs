---
title: "Azure Functions 的 host.json 参考"
description: "Azure Functions host.json 文件的参考文档。"
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: e836ccd204ff06e1eb0494cb392e781f29fdf421
ms.contentlocale: zh-cn
ms.lasthandoff: 09/29/2017

---

# <a name="hostjson-reference-for-azure-functions"></a>Azure Functions 的 host.json 参考

*host.json* 元数据文件包含对函数应用的所有函数产生影响的全局配置选项。 本文列出可用的设置。 http://json.schemastore.org/host 上提供了 JSON 架构。

[应用设置](functions-app-settings.md)和 [local.settings.json](functions-run-local.md#local-settings-file) 文件中提供了其他全局配置选项。

## <a name="sample-hostjson-file"></a>示例 host.json 文件

以下示例 *host.json* 文件中指定的所有可能的选项。

```javascript
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
    "functionTimeout": "00:05:00",
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

本文的以下各部分解释了每个顶级属性。 除非另有说明，否则其中的所有属性都是可选的。

## <a name="aggregator"></a>aggregator

指定在[计算 Application Insights 的指标](functions-monitoring.md#configure-the-aggregator)时要聚合多少个函数调用。 

```javascript
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|batchSize|1000|要聚合的最大请求数。| 
|flushTimeout|00:00:30|要聚合的最大时间段。| 

达到两个限制中的第一个限制时，会聚合函数调用。

## <a name="applicationinsights"></a>applicationInsights

控制 [Application Insights 中的采样功能](functions-monitoring.md#configure-sampling)。

```javascript
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|isEnabled|false|启用或禁用采样。| 
|maxTelemetryItemsPerSecond|5|开始采样所要达到的阈值。| 

## <a name="eventhub"></a>eventHub

[事件中心触发器和绑定](functions-bindings-event-hubs.md)的配置设置。

```javascript
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxBatchSize|64|每个接收循环收到的最大事件计数。|
|prefetchCount|不适用|基础 EventProcessorHost 将要使用的默认 PrefetchCount。| 
|batchCheckpointFrequency|1|创建 EventHub 游标检查点之前要处理的事件批数。| 

## <a name="functions"></a>functions

作业宿主要运行的函数列表。  空数组表示运行所有函数。  仅供在[本地运行](functions-run-local.md)时使用。 在函数应用中，请使用 *function.json* `disabled` 属性，而不要在 *host.json* 中使用此属性。

```javascript
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指示所有函数的超时持续时间。 在消耗计划中，有效范围为 1 秒至 10 分钟，默认值为 5 分钟。 在应用服务计划中没有限制，默认值为 null（表示无超时）。

```javascript
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

[http 触发器和绑定](functions-bindings-http-webhook.md)的配置设置。

```javascript
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 
        "dynamicThrottlesEnabled": false
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|routePrefix|api|应用到所有路由的路由前缀。 使用空字符串可删除默认前缀。 |
|maxOutstandingRequests|-1|在任意给定时间搁置的未完成请求数上限（-1 表示不受限制）。 限制包括已排队但尚未开始执行的请求，以及正在执行的所有请求。 超出此限制的任何传入请求将被拒绝，并返回 429“太忙”响应。 调用方可以使用该响应来采取基于时间的重试策略。 此设置仅控制作业宿主执行路径中发生的排队。 其他队列（例如 ASP.NET 请求队列）不受此设置的影响。 |
|maxConcurrentRequests|-1|要并行执行的 HTTP 函数数目上限（-1 表示不受限制）。 例如，如果在并发性较高时 HTTP 函数使用过多的系统资源，则可以设置限制。 或者，如果函数向第三方服务发出出站请求，则可能需要限制这些调用的速率。|
|dynamicThrottlesEnabled|false|导致请求处理管道定期检查系统性能计数器。 计数器包括连接、线程、进程、内存和 CPU。 如果任何计数器超出内置阈值 (80%)，则在计数器恢复正常级别之前，会拒绝请求并返回 429“太忙”响应。|

## <a name="id"></a>id

作业宿主的唯一 ID。 可以是不带短划线的小写 GUID。 在本地运行时必须指定。 在 Azure Functions 中运行时，如果省略 `id`，会自动生成 ID。

```javascript
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

控制 [ILogger 对象](functions-monitoring.md#write-logs-in-c-functions)或 [context.log](functions-monitoring.md#write-logs-in-javascript-functions) 写入的日志的筛选。

```javascript
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|categoryFilter|不适用|指定按类别进行筛选| 
|defaultLevel|信息|对于 `categoryLevels` 数组中未指定的任何类别，会将此级别和更高级别的日志发送到 Application Insights。| 
|categoryLevels|不适用|一个类别数组，指定每个类别的、要发送到 Application Insights 的最低日志级别。 此处指定的类别控制以相同值开头的所有类别，较长的值优先。 在前面的示例 *host.json* 文件中，将在 `Information` 级别记录以“Host.Aggregator”开头的所有类别的日志。 在 `Error` 级别记录以“Host”开头的其他所有类别（例如“Host.Executor”）的日志。| 

## <a name="queues"></a>queues

[存储队列触发器和绑定](functions-bindings-storage-queue.md)的配置设置。

```javascript
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxPollingInterval|60000|队列轮询的最大间隔时间，以毫秒为单位。| 
|visibilityTimeout|0|消息处理失败时的重试间隔时间。| 
|batchSize|16|要并行检索和处理的队列消息数。 最大值为 32。| 
|maxDequeueCount|5|在将某个消息移到有害队列之前，尝试处理该消息的次数。| 
|newBatchThreshold|batchSize/2|提取新消息批所要达到的阈值。| 

## <a name="servicebus"></a>serviceBus

[服务总线触发器和绑定](functions-bindings-service-bus.md)的配置设置。

```javascript
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|消息泵应该对回调发起的最大并发调用数。 | 
|prefetchCount|不适用|基础 MessageReceiver 将要使用的默认 PrefetchCount。| 
|autoRenewTimeout|00:05:00|自动续订消息锁的最长持续时间。| 

## <a name="singleton"></a>singleton

单一实例锁行为的配置设置。 有关详细信息，请参阅[有关单一实例支持的 GitHub 问题](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```javascript
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|占用函数级锁的时间段。 锁自动续订。| 
|listenerLockPeriod|00:01:00|占用侦听器锁的时间段。| 
|listenerLockRecoveryPollingInterval|00:01:00|在启动时无法获取侦听器锁的情况下，用于恢复侦听器锁的时间间隔。| 
|lockAcquisitionTimeout|00:01:00|运行时尝试获取锁的最长时间。| 
|lockAcquisitionPollingInterval|不适用|尝试获取锁的间隔时间。| 

## <a name="tracing"></a>tracing

使用 `TraceWriter` 对象创建的日志的配置设置。 请参阅 [C# 日志记录](functions-reference-csharp.md#logging)和 [Node.js 日志记录](functions-reference-node.md#writing-trace-output-to-the-console)。 

```javascript
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|consoleLevel|info|控制台日志记录的跟踪级别。 选项包括：`off`、`error`、`warning`、`info` 和 `verbose`。|
|fileLoggingMode|debugOnly|文件日志记录的跟踪级别。 选项包括 `never`、`always` 和 `debugOnly`。| 

## <a name="watchdirectories"></a>watchDirectories

应该监视其更改情况的一组[共享代码目录](functions-reference-csharp.md#watched-directories)。  确保当这些目录中的代码发生更改时，函数会拾取这些更改。

```javascript
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何更新 host.json 文件](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [查看环境变量中的全局设置](functions-app-settings.md)

