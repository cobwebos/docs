---
title: Durable Functions 中的性能和缩放 - Azure
description: Azure Functions 的 Durable Functions 扩展简介。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277903"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Durable Functions 中的性能和缩放 (Azure Functions)

若要优化性能和可伸缩性，必须了解 [Durable Functions](durable-functions-overview.md) 的独特缩放特征。

若要了解缩放行为，必须了解底层 Azure 存储提供程序的某些详细信息。

## <a name="history-table"></a>历史记录表

“历史记录”表是一个 Azure 存储表，包含任务中心内所有业务流程实例的历史记录事件。 此表的名称采用 *TaskHubName*History 格式。 当实例运行时，会在此表中添加新行。 此表的分区键派生自业务流程的实例 ID。 实例 ID 在大多数情况下是随机的，确保在 Azure 存储中以最佳方式分配内部分区。

需要运行业务流程实例时，会将“历史记录”表的相应行载入内存。 然后，这些历史记录事件将重播到业务流程协调程序函数代码中，使其恢复到以前的检查点状态。 以这种方式使用执行历史记录来重新生成状态不受[事件溯源模式](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)的影响。

## <a name="instances-table"></a>实例表

**实例**表是另一个 Azure 存储表，其中包含任务中心内所有业务流程和实体实例的状态。 创建实例时，会在此表中添加新行。 此表的分区键是业务流程实例 ID 或实体键，行键是固定常数。 对于每个业务流程或实体实例，都有一行。

此表用于满足来自 `GetStatusAsync` （.NET）和 `getStatus` （JavaScript） Api 以及[状态查询 HTTP API](durable-functions-http-api.md#get-instance-status)的实例查询请求。 它与前面所述的“历史记录”表内容保持最终一致。 以这种方式使用单独的 Azure 存储表有效满足实例查询操作不受[命令和查询责任分离 (CQRS) 模式](https://docs.microsoft.com/azure/architecture/patterns/cqrs)的影响。

## <a name="internal-queue-triggers"></a>内部队列触发器

业务流程协调程序函数和活动函数都由函数应用任务中心内的内部队列触发。 以这种方式使用队列可以提供可靠的“至少一次”消息传送保证。 Durable Functions 中有两种类型的队列：“控制队列”和“工作项队列”。

### <a name="the-work-item-queue"></a>工作项队列

Durable Functions 中的每个任务中心都有一个工作项队列。 它是一个基本队列，其行为类似于 Azure Functions 中的其他任何 `queueTrigger` 队列。 此队列每次将一条消息取消排队，可用于触发无状态活动函数。 其中的每个消息包含活动函数输入和其他元数据，例如要执行的函数。 当 Durable Functions 应用程序横向扩展到多个 VM 时，所有这些 VM 将会竞争，以从工作项队列中获取工作。

### <a name="control-queues"></a>控制队列

Durable Functions 中的每个任务中心有多个控制队列。 与较为简单的工作项队列相比，控制队列更加复杂。 控制队列用于触发有状态 orchestrator 和实体函数。 由于业务流程协调程序和实体函数实例都是有状态的单一实例，因此不能使用竞争性使用者模型来跨 Vm 分布负载。 相反，orchestrator 和 entity 消息在控制队列中进行负载均衡。 后续部分将会更详细地介绍此行为。

控制队列包含各种业务流程生命周期消息类型。 示例包括[业务流程协调程序控制消息](durable-functions-instance-management.md)、活动函数响应消息和计时器消息。 在单次轮询中，最多会从一个控制队列中取消 32 条消息的排队。 这些消息包含有效负载数据以及元数据，包括适用的业务流程实例。 如果将多个取消排队的消息用于同一业务流程实例，将会批处理这些消息。

### <a name="queue-polling"></a>队列轮询

持久任务扩展实现了随机指数回退算法，以降低空闲队列轮询对存储事务成本造成的影响。 当发现消息时，运行时将立即检查另一条消息;如果未找到任何消息，它将等待一段时间，然后重试。 在后续失败尝试获取队列消息之后，等待时间将继续增加，直到达到最长等待时间（默认为30秒）。

最大轮询延迟可通过[主机 json 文件](../functions-host-json.md#durabletask)中的 `maxQueuePollingInterval` 属性进行配置。 将此属性设置为较高的值可能会导致更高的消息处理延迟。 只有在处于不活动状态一段时间后，才会出现更高的延迟。 如果将此属性设置为较低的值，则可能会由于增加了存储事务而导致更高的存储成本。

> [!NOTE]
> 在 Azure Functions 消耗量和高级计划中运行时， [Azure Functions 规模控制器](../functions-scale.md#how-the-consumption-and-premium-plans-work)将每10秒轮询一次每个控件和一次工作项队列。 若要确定何时激活函数应用实例和进行规模决定，需要执行此额外的轮询。 在撰写本文时，这10秒的时间间隔是常量，无法进行配置。

## <a name="storage-account-selection"></a>存储帐户的选择

Durable Functions 使用的队列、表和 blob 在已配置的 Azure 存储帐户中创建。 可以使用**host. json**文件中的 `durableTask/storageProvider/connectionStringName` 设置（或 Durable Functions 1.x 中 `durableTask/azureStorageConnectionStringName` 设置）指定要使用的帐户。

### <a name="durable-functions-2x"></a>Durable Functions 2。x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1。x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

如果未指定，则使用默认的 `AzureWebJobsStorage` 存储帐户。 但是，对于性能敏感型工作负荷，我们建议配置非默认的存储帐户。 Durable Functions 重度使用 Azure 存储，它通过专用的存储帐户将 Durable Functions 存储的使用与 Azure Functions 主机的内部使用隔离开来。

## <a name="orchestrator-scale-out"></a>业务流程协调程序横向扩展

活动函数是无状态的，可通过添加 VM 自动进行横向扩展。 另一方面，业务流程协调程序函数和实体在一个或多个控制队列中进行*分区*。 控制队列的数目在 **host.json** 文件中定义。 下面的示例 host 代码段将 `durableTask/storageProvider/partitionCount` 属性（或 `durableTask/partitionCount` Durable Functions 1.x）设置为 `3`。

### <a name="durable-functions-2x"></a>Durable Functions 2。x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1。x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

可将任务中心配置为包含 1 到 16 个分区。 如果未指定分区数，则会使用默认分区数 **4**。

横向扩展到多个函数主机实例（通常在不同的 VM 上）时，每个实例会获取某个控制队列上的锁。 这些锁在内部实现为 blob 存储租约，并确保一个业务流程实例或实体仅在单个主机实例上运行一次。 如果任务中心配置了三个控制队列，则可以在最多三个 Vm 之间对业务流程实例和实体进行负载均衡。 可以添加更多的 VM，以提高活动函数执行容量。

下图演示了 Azure Functions 主机如何与横向扩展环境中的存储实体交互。

![缩放示意图](./media/durable-functions-perf-and-scale/scale-diagram.png)

如上图所示，所有 VM 都会争用工作项队列中的消息。 但是，只有三个 VM 可从控制队列获取消息，每个 VM 锁定了单个控制队列。

业务流程实例和实体分布在所有控制队列实例上。 通过对业务流程的实例 ID 或实体名称和密钥对进行哈希运算来完成分配。 默认情况下，业务流程实例 Id 为随机 Guid，确保实例在所有控制队列中均匀分布。

一般而言，业务流程协调程序函数是轻量型的，应该不需要大量的计算能力。 因此，无需创建大量控制队列分区即可获得更好的业务流程吞吐量。 大部分繁重工作应在可无限横向扩展的无状态活动函数中完成。

## <a name="auto-scale"></a>自动缩放

与在使用和弹性高级计划中运行的所有 Azure Functions 一样，Durable Functions 通过[Azure Functions 缩放控制器](../functions-scale.md#runtime-scaling)支持自动缩放。 缩放控制器通过定期发出 _peek_ 命令来监视所有队列的延迟。 根据扫视消息的延迟，缩放控制器将决定是要添加还是删除 VM。

如果缩放控制器确定控制队列消息延迟过高，则会添加 VM 实例，直到消息延迟下降到可接受的级别，或者达到控制队列分区计数。 同样，如果工作项队列延迟偏高，缩放控制器会不断地添加 VM 实例，而不管分区计数如何。

> [!NOTE]
> 从 Durable Functions 2.0 开始，可以将函数应用配置为在弹性高级计划中的 VNET 保护的服务终结点中运行。 在此配置中，Durable Functions 触发器启动缩放请求，而不是缩放控制器。

## <a name="thread-usage"></a>线程用量

在单个线程上执行业务流程协调程序函数，确保多次重播后执行可以是确定性的。 由于采用这种单线程执行，业务流程协调程序函数线程不得执行 CPU 密集型任务、执行 I/O 或出于任何原因而阻塞。 应将需要 I/O、阻塞或多个线程的任何工作移到活动函数中。

活动函数的行为与队列触发的正则函数完全相同。 它们可以安全地执行 I/O、执行 CPU 密集型操作和使用多个线程。 由于活动触发器是无状态的，因此可以任意横向扩展到不限数量的 VM。

实体函数也在单个线程上执行，并且操作是一次性处理的。 但是，实体函数对可执行的代码类型没有任何限制。

## <a name="concurrency-throttles"></a>并发限制

Azure Functions 支持在单个应用实例中并发执行多个函数。 这种并发执行有助于提高并行度，将典型的应用在一段时间内遇到的“冷启动”次数减到最少。 但高并发性可能会耗尽每个 VM 系统资源，如网络连接或可用内存。 根据函数应用的需求，可能有必要限制每个实例的并发性，以免在负载较高的情况下出现内存不足的问题。

可以在**host**文件中配置活动、orchestrator 和实体函数并发限制。 相关设置 `durableTask/maxConcurrentActivityFunctions` 用于活动函数，以及 orchestrator 和 entity 函数 `durableTask/maxConcurrentOrchestratorFunctions`。

### <a name="functions-20"></a>函数2。0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

在上面的示例中，最多可以在单个 VM 上并发运行最多10个 orchestrator 或 entity 函数和10个活动函数。 如果未指定，则并发活动数和 orchestrator 或实体函数执行数的上限为 VM 上的内核数的10倍。

> [!NOTE]
> 这些设置有助于管理单个 VM 上的内存和 CPU 用量。 但是，当跨多个 Vm 扩展时，每个 VM 都有自己的限制集。 这些设置不能用于在全局级别控制并发。

## <a name="extended-sessions"></a>扩展会话

扩展会话是一种设置，它在处理完消息后，将业务流程和实体保存在内存中。 启用扩展会话的典型效果是减少针对 Azure 存储帐户的 I/O，并总体提高吞吐量。

可以通过将 `durableTask/extendedSessionsEnabled` 设置为 `true` 在**host json**文件中来启用扩展会话。 `durableTask/extendedSessionIdleTimeoutInSeconds` 设置可用于控制空闲会话在内存中保留的时间：

**函数2。0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**函数1。0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

此设置需要注意两个可能的缺点：

1. 函数应用的内存使用量总体增加了。
2. 如果有很多并发、生存期较短的业务流程协调程序或实体函数执行，则吞吐量总体会降低。

例如，如果 `durableTask/extendedSessionIdleTimeoutInSeconds` 设置为30秒，则在不到1秒内执行的生存期较短的业务流程协调器或实体函数剧集仍占用内存30秒。 它还会根据前面提到的 `durableTask/maxConcurrentOrchestratorFunctions` 配额计数，这可能会阻止其他 orchestrator 或 entity 函数运行。

下一节将介绍扩展会话对 orchestrator 和实体函数的特定影响。

### <a name="orchestrator-function-replay"></a>业务流程协调程序函数重播

如前所述，业务流程协调程序函数是使用“历史记录”表的内容重播的。 默认情况下，每当从控制队列中取消一批消息的排队时，都会重播业务流程协调程序函数代码。 即使使用扇出，扇入模式并等待完成所有任务（例如，使用 .NET 中的 `Task.WhenAll` 或 JavaScript 中的 `context.df.Task.all`），在一段时间内处理任务响应的批处理时，会发生重播。 启用扩展会话后，orchestrator 函数实例将保留在内存中，并且无需重播全部历史记录即可处理新消息。

在以下情况下，通常会出现扩展会话的性能改进：

* 当并发运行的业务流程实例数量有限时。
* 当业务流程具有多个快速完成的连续操作（如数百个活动函数调用）时。
* 当业务流程进行扇出和扇入大量同时完成的操作时。
* 当业务流程协调程序函数需要处理大消息或执行任何占用大量 CPU 的数据处理时。

在所有其他情况下，业务流程协调程序函数通常不会有明显的性能改进。

> [!NOTE]
> 只能在全面开发并测试业务流程协调程序函数之后才使用这些设置。 默认的积极重播行为可用于在开发时检测[orchestrator 函数代码约束](durable-functions-code-constraints.md)冲突，因此在默认情况下是禁用的。

### <a name="entity-function-unloading"></a>实体函数卸载

实体函数在单个批处理中处理最多20个操作。 一旦实体处理完一批操作，它就会保持其状态并从内存中卸载。 您可以使用 "扩展会话" 设置延迟从内存中卸载实体。 实体继续像以前一样保持其状态更改，但会在配置的时间段内保留在内存中，从而减少 Azure 存储中的负载。 从 Azure 存储空间中减少负载可以提高经常访问的实体的整体吞吐量。

## <a name="performance-targets"></a>性能目标

规划对生产应用程序使用 Durable Functions 时，必须在规划过程中提前考虑性能要求。 本部分介绍一些基本的使用方案和预期的最大吞吐量数字。

* **连续活动执行**：此方案描述的业务流程协调程序函数逐个运行一系列活动函数。 它与[函数链接](durable-functions-sequence.md)示例非常类似。
* **并行活动执行**：此方案描述的业务流程协调程序函数使用[扇出扇入](durable-functions-cloud-backup.md)模式并行执行多个活动函数。
* **并行响应处理**：此方案是[扇出扇入](durable-functions-cloud-backup.md)模式的后半部分。 它侧重于扇入性能。 必须注意，与扇出不同，扇入是由单个业务流程协调程序函数实例执行的，因此只能在单个 VM 上运行。
* **外部事件处理**：此方案陈述每个等待一个[外部事件](durable-functions-external-events.md)的单个业务流程协调程序函数实例。
* **实体操作处理**：此方案测试_单个_[计数器实体](durable-functions-entities.md)处理恒定操作流的速度。

> [!TIP]
> 与扇出不同，扇入操作限制为单个 VM。 如果应用程序使用扇出扇入模式，并且你关注扇入性能，请考虑在多个[子业务流程](durable-functions-sub-orchestrations.md)之间分割活动函数扇出。

下表显示了前面所述方案的预期最大吞吐量数字。 “实例”是指在 Azure 应用服务中单个小型 ([A1](../../virtual-machines/sizes-previous-gen.md)) VM 上运行的业务流程协调程序函数的单个实例。 在各种情况下，都假设已启用[扩展会话](#orchestrator-function-replay)。 实际结果可能根据函数代码执行的 CPU 或 I/O 工作而异。

| 场景 | 最大吞吐量 |
|-|-|
| 顺序活动执行 | 每个实例每秒 5 个活动 |
| 并行活动执行（扇出） | 每个实例每秒 100 个活动 |
| 并行响应处理（扇入） | 每个实例每秒 150 个响应 |
| 外部事件处理 | 每个实例每秒 50 个事件 |
| 实体操作处理 | 每秒64操作数 |

> [!NOTE]
> 上述数字是截止 Durable Functions 扩展 v1.4.0（正式版）发布后的最新数字。 随着功能的不断成熟和优化，这些数字今后可能会有变化。

如果发现吞吐量数字不符合预期，但 CPU 和内存用量似乎正常，请检查原因是否与[存储帐户的运行状况](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)相关。 Durable Functions 扩展可能会在 Azure 存储帐户中施加大量的负载，负载高到一定程度会导致存储帐户受限制。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解灾难恢复和地理分布](durable-functions-disaster-recovery-geo-distribution.md)
