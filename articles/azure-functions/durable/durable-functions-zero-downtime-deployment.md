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
ms.openlocfilehash: b47604f2c8703ba587e98d68dc30552e5944f562
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614505"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions 的零停机时间部署

Durable Functions 的[可靠执行模型](durable-functions-checkpointing-and-replay.md)要求业务流程是确定性的，这会在部署更新时产生额外的挑战。 当部署包含对活动函数签名或 orchestrator 逻辑的更改时，正在进行的业务流程实例将失败。 对于长时间运行的业务流程的实例，这种情况尤其重要，它可能代表几个小时或几天的工作。

若要防止这些故障发生，你必须将部署延迟到所有正在运行的业务流程实例都已完成，或者确保任何正在运行的业务流程实例都使用现有版本的函数。 有关版本控制的详细信息，请参阅[Durable Functions 中的版本控制](durable-functions-versioning.md)。

> [!NOTE]
> 本文提供针对 Durable Functions 1.x 的函数应用的指南。 尚未对其进行更新，以考虑 Durable Functions 1.x 中引入的更改。 有关扩展版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

下图比较了这三个主要策略来实现 Durable Functions 的零停机部署： 

| 策略 |  使用时机 | 优点 | 缺点 |
| -------- | ------------ | ---- | ---- |
| **[控制](#versioning)** |  不经常出现[重大更改](durable-functions-versioning.md)的应用程序。 | 简单实现。 |  增加了内存中函数应用大小和函数的数目。<br/>代码重复。 |
| **[带槽的状态检查](#status-check-with-slot)** | 不具有长时间运行的业务流程的系统持续时间超过24小时或更频繁重叠的业务流程。 | 简单基本代码。<br/>不需要额外的函数应用管理。 | 需要额外的存储帐户或任务中心管理。<br/>如果没有运行任何业务流程，则需要一段时间。 |
| **[应用程序路由](#application-routing)** | 在业务流程不运行的情况下没有时间段的系统，例如，业务流程持续时间超过24小时的系统或具有经常重叠的业务流程的系统。 | 处理持续运行包含重大更改的业务流程的新版本系统。 | 需要智能应用程序路由器。<br/>可能会超出订阅所允许的函数应用数（默认值为100）。 |

## <a name="versioning"></a>版本控制

定义新版本的函数，并在 function app 中保留旧版本。 如图中所示，函数的版本将成为其名称的一部分。 由于保留了以前版本的函数，因此正在进行的业务流程实例可以继续引用它们。 同时，对新的业务流程实例的请求将调用最新版本，你的业务流程客户端函数可以从应用设置引用该版本。

![版本控制策略](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

在此策略中，必须复制每个函数，并将其引用更新到其他函数。 您可以通过编写脚本来更轻松地执行此过程。 下面是一个包含迁移脚本的[示例项目](https://github.com/TsuyoshiUshio/DurableVersioning)。

>[!NOTE]
>此策略使用部署槽位来避免部署期间的停机时间。 有关如何创建和使用新部署槽的详细信息，请参阅[Azure Functions 部署槽](../functions-deployment-slots.md)。

## <a name="status-check-with-slot"></a>带槽的状态检查

尽管 function app 的当前版本正在生产槽中运行，但将新版本的 function app 部署到过渡槽。 在交换生产槽和过渡槽之前，请检查是否有正在运行的业务流程实例。 所有业务流程实例完成后，可以执行交换。 如果有可预测的时间段，并且没有正在进行的业务流程实例，则此策略适用。 当您的业务流程不会长时间运行并且您的业务流程执行不经常重叠时，这是最佳方法。

### <a name="function-app-configuration"></a>Function app 配置

你可以使用以下过程来设置此方案：

1. [将部署槽位添加](../functions-deployment-slots.md#add-a-slot)到用于过渡和生产的函数应用。

1. 对于每个槽，将[AzureWebJobsStorage 应用程序设置](../functions-app-settings.md#azurewebjobsstorage)设置为共享存储帐户的连接字符串。 此 Azure Functions 运行时将使用此方法。 此帐户将由 Azure Functions 运行时使用并管理函数的密钥。

1. 对于每个槽，请创建新的应用设置（例如 DurableManagementStorage）并将其值设置为不同存储帐户的连接字符串。 Durable Functions 扩展将使用这些存储帐户来实现[可靠执行](durable-functions-checkpointing-and-replay.md)。 为每个槽使用单独的存储帐户。 请勿将此设置标记为部署槽设置。

1. 在函数应用的 " [durableTask" 部分](durable-functions-bindings.md#hostjson-settings)中，将 "azureStorageConnectionStringName" 指定为在步骤3中创建的应用设置的名称。

下图显示了部署槽位和存储帐户的配置。 在这种可能的预部署方案中，函数应用的版本2在生产槽中运行，而版本1仍保留在过渡槽中。

![部署槽位](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host json 示例

下面的 JSON 片段是 host json 文件中的连接字符串设置的示例。

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

将 CI/CD 管道配置为仅在函数应用没有挂起或正在运行的业务流程实例时部署。 使用 Azure Pipelines 时，可以创建一个用于检查这些条件的函数，如以下示例中所示：

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

接下来，将暂存入口配置为等待，直到没有运行任何业务流程。 有关详细信息，请参阅[使用入口发布部署控制](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![部署入口](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines 在部署开始之前检查函数应用以运行业务流程实例。

![部署入口（正在运行）](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

现在，函数应用的新版本应部署到过渡槽。

![部署槽位](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最后，交换槽。 

还会交换未标记为部署槽设置的应用程序设置，因此版本2应用程序将保留对存储帐户 A 的引用。由于业务流程状态是在存储帐户中跟踪的，因此在版本2应用上运行的任何业务流程都将继续在新的槽中运行，而不会中断。

![部署槽位](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

若要为两个槽使用相同的存储帐户，可以更改任务中心的名称。 在这种情况下，你需要管理你的槽和应用 HubName 设置的状态。 若要了解详细信息，请参阅[Durable Functions 中的任务中心](durable-functions-task-hubs.md)。

## <a name="application-routing"></a>应用程序路由

此策略最复杂。 但是，它可用于在运行业务流程之间没有时间间隔的函数应用。

对于此策略，你必须在 Durable Functions 前面创建一个*应用程序路由器*。 此路由器可通过 Durable Functions 实现，并且具有以下职责：

* 部署 function app。
* 管理 Durable Functions 的版本。 
* 将业务流程请求路由到 function app。

第一次接收业务流程请求时，路由器将执行以下任务：

1. 在 Azure 中创建新的 function app。
2. 将函数应用的代码部署到 Azure 中的新函数应用。
3. 将业务流程请求转发到新的应用。

路由器管理将应用程序代码的哪个版本部署到 Azure 中的函数应用的状态。

![应用程序路由（首次）](media/durable-functions-zero-downtime-deployment/application-routing.png)

路由器会根据随请求发送的 `version`，将部署和业务流程请求定向到相应的函数应用，忽略修补程序版本。

部署新版本的应用时，如果*不*进行重大更改，则可以递增修补程序版本。 路由器部署到现有 function app，并将新版本的代码的请求发送到相同的函数应用。

![应用程序路由（无重大更改）](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

当你使用重大更改部署应用的新版本时，你可以递增主版本或次要版本。 然后，应用程序路由器在 Azure 中创建新的 function app 并将其部署到该应用，并将新版本的应用的请求路由到该应用。 在下图中，在1.0.1 版应用程序上运行的业务流程保持运行，但1.1.0 版本的请求会路由到新的 function app。

![应用程序路由（重大更改）](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

路由器在1.0.1 版上监视业务流程的状态，并在所有业务流程完成后删除应用。  

### <a name="tracking-store-settings"></a>跟踪存储设置

每个函数应用应使用单独的计划队列，这些队列可能位于不同的存储帐户中。 但是，如果你想要跨应用程序的所有版本查询所有业务流程实例，则可以在 function app 中共享实例和历史记录表。 您可以通过在[host. json 设置](durable-functions-bindings.md#host-json)文件中配置 `trackingStoreConnectionStringName` 和 `trackingStoreNamePrefix` 来共享表，以便它们都使用相同的值。

有关更多详细信息，请[在 Azure 中的 Durable Functions 中管理实例](durable-functions-instance-management.md)。

![跟踪存储设置](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [版本控制 Durable Functions](durable-functions-versioning.md)

