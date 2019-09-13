---
title: Durable Functions 预览版功能 - Azure Functions
description: 了解 Durable Functions 的预览版功能。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933246"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 预览版 (Azure Functions)

Durable Functions 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。 如果你不熟悉 Durable Functions，请参阅[概述文档](durable-functions-overview.md)。

Durable Functions 1.x 是 Azure Functions 的 GA（正式版）功能，但还包含目前以公共预览版提供的多个子功能。 本文介绍最新发布的预览版功能，并详细介绍其工作原理和用法。

> [!NOTE]
> 这些预览版功能随附在 Durable Functions 2.0 发行版中。该发行版目前是一个**预览版质量的发行版**，其中包含多项中断性变更。 可以在 nuget.org 上找到 Azure Functions Durable 扩展包的内部版本，其版本格式为 **2.0.0-betaX**。 这些内部版本不适用于任何生产工作负荷，后续的发行版可能包含其他中断性变更。

## <a name="breaking-changes"></a>中断性变更

Durable Functions 2.0 中引入了几项中断性变更。 现有的应用程序在不更改代码的情况下预期不会与 Durable Functions 2.0 兼容。 本部分列出了其中的部分更改：

### <a name="hostjson-schema"></a>Host.json 架构

以下代码片段演示了 host.json 的新架构。 要注意的主要更改是新的子节：

* 存储特定的配置的 `"storageProvider"`（和 `"azureStorage"` 子节）
* 用于跟踪和记录配置的 `"tracking"`
* `"notifications"`事件网格通知`"eventGrid"`配置的 (和子节)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

随着 Durable Functions 2.0 的不断稳定，host.json `durableTask` 节中会引入更多的更改。 有关这些更改的详细信息，请参阅[此 GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/641)。

### <a name="public-interface-changes"></a>公共接口更改

Durable Functions 支持的各种“上下文”对象包含适合在单元测试中使用的抽象基类。 作为 Durable Functions 2.0 的一部分，这些抽象基类已由接口取代。 直接使用具体类型的函数代码不受影响。

下表显示了主要更改：

| 旧类型 | 新类型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

如果抽象基类包含虚拟方法，则这些虚拟方法已由 `DurableContextExtensions` 中定义的扩展方法取代。

## <a name="entity-functions"></a>实体函数

从 Durable Functions v 2.0.0 开始，我们引入了一个新的[实体函数](durable-functions-entities.md)概念。

实体函数定义用于读取和更新较小状态片段（称为“持久实体”）的操作。 与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。 与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

根据初始用户反馈，我们稍后为 Durable Functions v 2.0.0-beta1 中的实体添加了对基于类的编程模型的支持。

若要了解详细信息，请参阅[实体函数](durable-functions-entities.md)一文。

## <a name="durable-http"></a>持久 HTTP

从 Durable Functions v 2.0.0-beta2 开始，我们引入了新的[持久 HTTP](durable-functions-http-features.md)功能，使你能够：

* 直接从业务流程函数调用 HTTP Api （有一些记录的限制）
* 实现自动客户端 HTTP 202 状态轮询
* [Azure 托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的内置支持

若要了解详细信息，请参阅[HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="alternate-storage-providers"></a>备用的存储提供程序

Durable Task Framework 目前支持多个存储提供程序，包括 [Azure 存储](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、[Azure 服务总线](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[内存中仿真器](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)和试验性的 [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) 提供程序。 但是，到目前为止，适用于 Azure Functions 的 Durable Task 扩展仅支持 Azure 存储提供程序。 从 Durable Functions 2.0 开始，即将添加对备用存储提供程序的支持（首先是 Redis 提供程序）。

> [!NOTE]
> Durable Functions 2.0 仅支持与 .NET Standard 2.0 兼容的提供程序。

### <a name="emulator"></a>仿真器

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) 提供程序是适合用于本地测试方案的本地内存非持久性存储提供程序。 可以使用以下精简的 **host.json** 架构对其进行配置：

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis（试验性）

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) 提供程序将所有业务流程状态保存到配置的 Redis 群集中。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` 必须引用应用设置或环境变量的名称。 该应用设置或环境变量应包含“服务器:端口”格式的 Redis 连接字符串值。 例如，用于连接本地 Redis 群集的 `localhost:6379`。

> [!NOTE]
> Redis 提供程序目前是试验性的，仅支持单个节点上运行的函数应用。 我们不保证 Redis 提供程序会推出正式版，在将来的版本中可能会将其删除。
