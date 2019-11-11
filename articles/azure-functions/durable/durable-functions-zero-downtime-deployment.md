---
title: Durable Functions 的零停机时间部署
description: 了解如何在零停机部署中启用 Durable Functions 业务流程。
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: af19f8cdcc26d1459bc024f00b963f04bd8d763b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904042"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions 的零停机时间部署

Durable Functions 的[可靠执行模型](durable-functions-checkpointing-and-replay.md)要求业务流程是确定性的，这会在部署更新时产生额外的挑战。 如果部署包含对活动函数签名或业务流程协调程序逻辑的更改，运行中的业务流程实例将发生故障。 对于长时间运行的业务流程的实例，这种情况尤其有问题，这可能表示工作时间或天数。

若要防止这些故障发生，可以使用两个选项： 
- 延迟部署，直到所有正在运行的业务流程实例都已完成。
- 请确保任何正在运行的业务流程实例都使用现有版本的函数。 

> [!NOTE]
> 本文提供面向 Durable Functions 1.x 的函数应用的指南。 尚未对其进行更新，以考虑 Durable Functions 1.x 中引入的更改。 有关扩展版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)。

下图比较了这三个主要策略来实现 Durable Functions 的零停机时间部署： 

| 策略 |  何时使用 | 优点 | 缺点 |
| -------- | ------------ | ---- | ---- |
| [版本控制](#versioning) |  不经常出现[中断性变更](durable-functions-versioning.md)的应用程序。 | 易于实施。 |  函数应用的内存大小和函数数目增加。<br/>代码复制。 |
| [带槽的状态检查](#status-check-with-slot) | 没有长时间运行的业务流程持续时间超过24小时或经常重叠的业务流程的系统。 | 简单的基本代码。<br/>不需要额外的函数应用管理。 | 需要额外的存储帐户或任务中心管理。<br/>需要有几段时间没有任何业务流程运行。 |
| [应用程序路由](#application-routing) | 在业务流程不运行的情况下没有时间段的系统，例如，业务流程持续时间超过24小时或经常重叠的业务流程的时间段。 | 处理持续运行具有中断性变更的业务流程的新系统版本。 | 需要智能应用程序路由器。<br/>可能会超出订阅所允许的函数应用数。 默认值为 100。 |

## <a name="versioning"></a>版本控制

定义新版本函数，并在函数应用中保留旧版本。 如图中所示，函数的版本将成为其名称的一部分。 由于保留了以前的函数版本，运行中的业务流程实例可以继续引用它们。 同时，对新业务流程实例的请求将调用最新版本，业务流程客户端函数可以从应用设置引用该版本。

![版本控制策略](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

在此策略中，必须复制每个函数，并且必须更新对其他函数的引用。 可以通过编写脚本来简化此过程。 下面是一个包含迁移脚本的[示例项目](https://github.com/TsuyoshiUshio/DurableVersioning)。

>[!NOTE]
>此策略使用部署槽来避免部署期间发生停机。 有关如何创建和使用新部署槽的详细信息，请参阅[Azure Functions 部署槽](../functions-deployment-slots.md)。

## <a name="status-check-with-slot"></a>带槽的状态检查

当前版本的函数应用在生产槽中运行时，将新版本的函数应用部署到过渡槽。 在交换生产槽和过渡槽之前，请检查是否有任何正在运行的业务流程实例。 所有业务流程实例完成后，可以执行交换。 当没有任何业务流程实例处于可预测的时间段时，此策略适用。 如果业务流程不会长时间运行，并且业务流程执行不经常重叠，则这是最佳方法。

### <a name="function-app-configuration"></a>函数应用配置

使用以下过程来设置此方案。

1. [将部署槽添加](../functions-deployment-slots.md#add-a-slot)到函数应用用于过渡和生产。

1. 对于每个槽，请将 [AzureWebJobsStorage 应用程序设置](../functions-app-settings.md#azurewebjobsstorage)指定为共享存储帐户的连接字符串。 此存储帐户连接字符串由 Azure Functions 运行时使用。 此帐户由 Azure Functions 运行时使用，并管理函数的键。

1. 对于每个槽，创建新的应用设置，例如 `DurableManagementStorage`。 将其值设置为不同存储帐户的连接字符串。 这些存储帐户由 Durable Functions 扩展用于[可靠执行](durable-functions-checkpointing-and-replay.md)。 对每个槽使用单独的存储帐户。 不要将此设置标记为部署槽设置。

1. 在函数应用的[host json 文件的 durableTask 节](durable-functions-bindings.md#hostjson-settings)中，将 `azureStorageConnectionStringName` 指定为你在步骤3中创建的应用设置的名称。

下图显示了部署槽位和存储帐户的配置。 在这种潜在的预先部署方案中，函数应用的版本2在生产槽中运行，而版本1仍保留在过渡槽中。

![部署槽位和存储帐户](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host json 示例

下面的 JSON 片段是*host json*文件中的连接字符串设置的示例。

#### <a name="functions-20"></a>函数2。0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD 管道配置

将 CI/CD 管道配置为仅当函数应用不存在任何挂起或正在运行的业务流程实例时进行部署。 使用 Azure Pipelines 时，可以创建一个函数用于检查这些条件，如以下示例中所示：

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

接下来，将过渡门限配置为等到没有任何业务流程运行为止。 有关详细信息，请参阅[使用入口发布部署控制](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![部署门限](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines 会在部署开始之前检查函数应用是否存在正在运行的业务流程实例。

![部署入口（正在运行）](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

现在，新版本的函数应用应会部署到过渡槽。

![过渡槽](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最后交换槽。 

未标记为部署槽设置的应用程序设置也会交换，因此，版本 2 应用将保留它对存储帐户 A 的引用。由于业务流程状态是在存储帐户中跟踪的，因此，在版本 2 应用中运行的任何业务流程将继续在新槽中运行，而不会中断。

![部署槽位](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

若要对两个槽使用同一个存储帐户，可以更改任务中心的名称。 在这种情况下，你需要管理你的槽的状态和应用的 HubName 设置。 有关详细信息，请参阅 [Durable Functions中的任务中心](durable-functions-task-hubs.md)。

## <a name="application-routing"></a>应用程序路由

此策略最复杂。 但是，它可用于在运行的业务流程之间没有时间间隔的函数应用。

对于此策略，必须在 Durable Functions 的前面创建一个应用程序路由器。 此路由器可通过 Durable Functions 来实现。 路由器负责执行以下操作：

* 部署函数应用。
* 管理 Durable Functions 的版本。 
* 将业务流程请求路由到 function app。

第一次接收业务流程请求时，路由器将执行以下任务：

1. 在 Azure 中创建新的函数应用。
2. 将函数应用的代码部署到 Azure 中的新函数应用。
3. 将业务流程请求转发到新应用。

该路由器管理以下状态：将哪个版本的应用代码部署到 Azure 中的哪个函数应用。

![应用程序路由（首次）](media/durable-functions-zero-downtime-deployment/application-routing.png)

路由器根据请求发送的版本将部署和业务流程请求定向到相应的函数应用。 它忽略修补程序版本。

部署新版本的应用时，如果不进行重大更改，则可以递增修补程序版本。 该路由器将部署到现有 function app，并发送对新旧版本代码的请求，并将这些请求路由到同一个函数应用。

![应用程序路由（无重大更改）](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

部署具有中断性变更的新应用版本时，可以递增主要版本或次要版本。 然后，应用程序路由器将在 Azure 中创建新的函数应用，部署到该应用，并将对新版应用的请求路由到该应用。 在下图中，在1.0.1 版应用程序上运行的业务流程保持运行，但对1.1.0 版本的请求会路由到新的 function app。

![应用程序路由（重大更改）](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

路由器监视1.0.1 版的业务流程的状态，并在所有业务流程完成后删除应用。 

### <a name="tracking-store-settings"></a>跟踪存储设置

每个函数应用应使用单独的计划队列（这些队列可能位于不同的存储帐户中）。 如果要跨应用程序的所有版本查询所有业务流程实例，可以在 function app 中共享实例和历史记录表。 您可以通过在[host. json 设置](durable-functions-bindings.md#host-json)文件中配置 `trackingStoreConnectionStringName` 和 `trackingStoreNamePrefix` 设置来共享表，以使它们均使用相同的值。

有关详细信息，请参阅[管理 Azure 中 Durable Functions](durable-functions-instance-management.md)中的实例。

![跟踪存储设置](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Durable Functions 版本控制](durable-functions-versioning.md)

