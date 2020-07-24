---
title: Durable Functions 的零停机时间部署
description: 了解如何启用 Durable Functions 业务流程以实现零停机时间部署。
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 11bbc30179cc27f4799b1fd2869cb312dfa34473
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093062"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions 的零停机时间部署

Durable Functions 的[可靠执行模型](./durable-functions-orchestrations.md)要求业务流程是确定性的，这就需要在部署更新时考虑到更多的挑战。 如果部署包含对活动函数签名或业务流程协调程序逻辑的更改，运行中的业务流程实例将发生故障。 对于长时间运行的业务流程实例（要运行数小时甚至数天），此问题尤为严重。

为了防止发生这些故障，可以执行两个选项： 
- 延迟部署，直到所有正在运行的业务流程实例都已完成。
- 确保任何正在运行的业务流程实例使用现有的函数版本。 

下图比较了实现 Durable Functions 零停机时间部署的三个主要策略： 

| 策略 |  何时使用 | 优点 | 缺点 |
| -------- | ------------ | ---- | ---- |
| [版本控制](#versioning) |  不经常出现[中断性变更](durable-functions-versioning.md)的应用程序。 | 易于实施。 |  函数应用的内存大小和函数数目增加。<br/>代码复制。 |
| [带槽的状态检查](#status-check-with-slot) | 不存在长时间运行（超过 24 小时）的业务流程或经常重叠业务流程的系统。 | 简单的基本代码。<br/>不需要额外的函数应用管理。 | 需要额外的存储帐户或任务中心管理。<br/>需要有几段时间没有任何业务流程运行。 |
| [应用程序路由](#application-routing) | 在所有时间段都有业务流程运行的系统，例如，业务流程持续时间超过 24 小时的时段，或经常重叠业务流程的时段。 | 处理持续运行具有中断性变更的业务流程的新系统版本。 | 需要智能应用程序路由器。<br/>可能超出订阅允许的最大函数应用数目。 默认值为 100。 |

## <a name="versioning"></a>版本控制

定义新版本函数，并在函数应用中保留旧版本。 如图中所示，函数的版本将成为其名称的一部分。 由于保留了以前的函数版本，运行中的业务流程实例可以继续引用它们。 同时，对新业务流程实例的请求将调用最新版本，业务流程客户端函数可以从应用设置引用该版本。

![策略版本控制](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

在此策略中，必须复制每个函数，并且必须更新其对其他函数的引用。 可以通过编写脚本来简化此过程。 下面是使用迁移脚本的[示例项目](https://github.com/TsuyoshiUshio/DurableVersioning)。

>[!NOTE]
>此策略使用部署槽来避免部署期间发生停机。 有关如何创建和使用新部署槽的详细信息，请参阅 [Azure Functions 部署槽](../functions-deployment-slots.md)。

## <a name="status-check-with-slot"></a>带槽的状态检查

当前版本的函数应用在生产槽中运行时，将新版本的函数应用部署到过渡槽。 在交换生产槽和过渡槽之前，检查是否有任何正在运行的业务流程实例。 所有业务流程实例完成后，可以执行交换。 如果在可预测的时间段没有正在运行的业务流程实例，则此策略适用。 如果业务流程不会长时间运行，并且业务流程执行不经常重叠，则这是最佳方法。

### <a name="function-app-configuration"></a>函数应用配置

请使用以下过程来设置此方案。

1. [将部署槽添加](../functions-deployment-slots.md#add-a-slot)到函数应用用于过渡和生产。

1. 对于每个槽，请将 [AzureWebJobsStorage 应用程序设置](../functions-app-settings.md#azurewebjobsstorage)指定为共享存储帐户的连接字符串。 此存储帐户连接字符串由 Azure Functions 运行时使用。 此帐户由 Azure Functions 运行时使用，可以管理函数的密钥。

1. 对于每个槽，请创建新的应用设置，例如 `DurableManagementStorage`。 将其值设置为不同存储帐户的连接字符串。 Durable Functions 扩展使用这些存储帐户来实现[可靠执行](./durable-functions-orchestrations.md)。 对每个槽使用单独的存储帐户。 不要将此设置标记为部署槽设置。

1. 在函数应用的 [host.json 文件的 durableTask 节](durable-functions-bindings.md#hostjson-settings)中，将 `azureStorageConnectionStringName` 指定为在步骤 3 中创建的应用设置的名称。

下图显示了部署槽和存储帐户的所述配置。 在这种可能的部署前方案中，函数应用版本 2 在生产槽中运行，而版本 1 保留在过渡槽中。

![部署槽位和存储帐户](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host json 示例

以下 JSON 片段是 *host.json* 文件中的连接字符串设置示例。

#### <a name="functions-20"></a>Functions 2.0

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
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

接下来，将过渡门限配置为等到没有任何业务流程运行为止。 有关详细信息，请参阅[使用入口发布部署控制](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![部署入口](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines 会在部署开始之前检查函数应用是否存在正在运行的业务流程实例。

![部署门限（正在运行）](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

现在，新版本的函数应用应会部署到过渡槽。

![过渡槽](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最后交换槽。 

未标记为部署槽设置的应用程序设置也会交换，因此，版本 2 应用将保留它对存储帐户 A 的引用。由于业务流程状态是在存储帐户中跟踪的，因此，在版本 2 应用中运行的任何业务流程将继续在新槽中运行，而不会中断。

![部署槽位](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

若要对两个槽使用同一个存储帐户，可以更改任务中心的名称。 在这种情况下，需要管理槽的状态和应用的 HubName 设置。 有关详细信息，请参阅 [Durable Functions中的任务中心](durable-functions-task-hubs.md)。

## <a name="application-routing"></a>应用程序路由

此策略最复杂。 但是，它可用于在运行的业务流程之间没有时间间隔的函数应用。

对于此策略，必须在 Durable Functions 的前面创建一个应用程序路由器。** 此路由器可通过 Durable Functions 实现。 路由器的责任如下：

* 部署函数应用。
* 管理 Durable Functions 的版本。 
* 将业务流程请求路由到函数应用。

首次收到业务流程请求时，该路由器执行以下任务：

1. 在 Azure 中创建新的函数应用。
2. 将函数应用的代码部署到 Azure 中的新函数应用。
3. 将业务流程请求转发到新应用。

该路由器管理以下状态：将哪个版本的应用代码部署到 Azure 中的哪个函数应用。

![应用程序路由（首次）](media/durable-functions-zero-downtime-deployment/application-routing.png)

该路由器根据连同请求一起发送的版本，将部署和业务流程请求定向到相应的函数应用。 它会忽略修补程序版本。

部署没有中断性变更的新应用版本时，可以递增修补程序版本。 该路由器将部署到现有的函数应用，并将对旧版和新版代码的请求发送到同一个函数应用。

![应用程序路由（无中断性变更）](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

部署具有中断性变更的新应用版本时，可以递增主要版本或次要版本。 然后，应用程序路由器将在 Azure 中创建新的函数应用，部署到该应用，并将对新版应用的请求路由到该应用。 在下图中，在 1.0.1 版应用中运行的业务流程将保持运行，但对 1.1.0 版本的请求将路由到新的函数应用。

![应用程序路由（中断性变更）](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

该路由器将监视版本 1.0.1 中的业务流程的状态，并在完成所有业务流程后删除应用。 

### <a name="tracking-store-settings"></a>跟踪存储设置

每个函数应用应使用单独的计划队列（这些队列可能位于不同的存储帐户中）。 若要跨应用程序的所有版本查询所有业务流程实例，可以在函数应用之间共享实例和历史记录表。 可以通过在 [host.json 设置](durable-functions-bindings.md#host-json)文件中配置 `trackingStoreConnectionStringName` 和 `trackingStoreNamePrefix` 设置来共享表，使它们全部使用相同的值。

有关详细信息，请参阅[在 Azure 中管理 Durable Functions 中的实例](durable-functions-instance-management.md)。

![跟踪存储设置](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Durable Functions 版本控制](durable-functions-versioning.md)
