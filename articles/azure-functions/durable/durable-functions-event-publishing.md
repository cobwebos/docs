---
title: 从 Durable Functions 发布到 Azure 事件网格（预览）
description: 了解如何配置 Durable Functions 的自动 Azure 事件网格发布。
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: f0fbb46320b896008b6a1343357f016a9f57b0fe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231449"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>从 Durable Functions 发布到 Azure 事件网格（预览）

本文介绍了如何设置 Durable Functions，以便将业务流程生命周期事件（例如“已创建”、“已完成”和“失败”）发布到自定义的 [Azure 事件网格主题](https://docs.microsoft.com/azure/event-grid/overview)。

此功能在以下场景中非常有用：

* **DevOps scenarios like blue/green deployments**: You might want to know if any tasks are running before implementing the [side-by-side deployment strategy](durable-functions-versioning.md#side-by-side-deployments).

* **高级监视和诊断支持**：可以在针对查询优化的外部存储（例如 SQL 数据库或 CosmosDB）中跟踪业务流程状态信息。

* **长时间运行的后台活动**：如果对长时间运行的后台活动使用 Durable Functions，此功能有助于了解当前状态。

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>必备组件

* Install [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in your Durable Functions project.
* 安装 [Azure 存储模拟器](../../storage/common/storage-use-emulator.md)。
* 安装 [Azure CLI ](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 或使用 [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Create a custom event grid topic

Create an event grid topic for sending events from Durable Functions. 以下说明介绍如何使用 Azure CLI 创建主题。 有关如何使用 PowerShell 或 Azure 门户执行此操作的信息，请参阅以下文章：

* [事件网格快速入门：创建自定义事件 - PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [事件网格快速入门：创建自定义事件 - Azure 门户](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令创建资源组。 Currently, Azure Event Grid doesn't support all regions. For information about which regions are supported, see the [Azure Event Grid overview](../../event-grid/overview.md).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>创建自定义主题

An event grid topic provides a user-defined endpoint that you post your event to. 用主题的唯一名称替换 `<topic_name>`。 主题名称必须唯一，因为它将用作 DNS 条目。

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>获取终结点和密钥

获取主题的终结点。 将 `<topic_name>` 替换为所选的名称。

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

获取主题密钥。 将 `<topic_name>` 替换为所选的名称。

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

现在，可将事件发送到主题。

## <a name="configure-azure-event-grid-publishing"></a>配置 Azure 事件网格发布

在 Durable Functions 项目中，找到 `host.json` 文件。

在 `durableTask` 属性中添加 `eventGridTopicEndpoint` 和 `eventGridKeySettingName`。

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

The possible Azure Event Grid configuration properties can be found in the [host.json documentation](../functions-host-json.md#durabletask). After you configure the `host.json` file, your function app sends lifecycle events to the event grid topic. This works when you run your function app both locally and in Azure.```

在函数应用和 `local.setting.json` 中设置主题密钥的应用设置。 以下 JSON 是用于本地调试的 `local.settings.json` 示例。 将 `<topic_key>` 替换为主题密钥。  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

确保[存储模拟器](../../storage/common/storage-use-emulator.md)正在运行。 最好是在执行之前运行 `AzureStorageEmulator.exe clear all` 命令。

## <a name="create-functions-that-listen-for-events"></a>创建用于侦听事件的函数

创建函数应用。 It's best to locate it in the same region as the event grid topic.

### <a name="create-an-event-grid-trigger-function"></a>Create an event grid trigger function

创建用于接收生命周期事件的函数。 选择“自定义函数”。

![选择“创建自定义函数”。](./media/durable-functions-event-publishing/functions-portal.png)

选择“事件网格触发器”，然后选择 `C#`。

![选择“事件网格触发器”。](./media/durable-functions-event-publishing/eventgrid-trigger.png)

输入函数的名称，然后选择 `Create`。

![创建事件网格触发器。](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

创建包含以下代码的函数：

#### <a name="precompiled-c"></a>预编译 C#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C# 脚本

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

选择 `Add Event Grid Subscription`。 This operation adds an event grid subscription for the event grid topic that you created. 有关详细信息，请参阅 [Azure 事件网格中的概念](https://docs.microsoft.com/azure/event-grid/concepts)。

![选择“事件网格触发器”链接。](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

为“主题类型”选择 `Event Grid Topics`。 Select the resource group that you created for the event grid topic. Then select the instance of the event grid topic. 按 `Create`。

![创建事件网格订阅。](./media/durable-functions-event-publishing/eventsubscription.png)

现已准备好接收生命周期事件。

## <a name="create-durable-functions-to-send-the-events"></a>创建用于发送事件的 Durable Functions

在本地计算机上，在 Durable Functions 项目中开始调试。  以下代码与 Durable Functions 的模板代码相同。 已在本地计算机上配置 `host.json` 和 `local.settings.json`。

### <a name="precompiled-c"></a>预编译 C#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`, `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

如果使用 Postman 或浏览器调用 `Sample_HttpStart`，则 Durable Functions 会开始发送生命周期事件。 终结点通常是用于本地调试的 `http://localhost:7071/api/Sample_HttpStart`。

查看在 Azure 门户中创建的函数发出的日志。

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>事件架构

以下列表解释了生命周期事件架构：

* **`id`** : Unique identifier for the event grid event.
* **`subject`** : Path to the event subject. `durable/orchestrator/{orchestrationRuntimeStatus}`。 `{orchestrationRuntimeStatus}` 为 `Running`、`Completed`、`Failed` 和 `Terminated`。  
* **`data`** : Durable Functions Specific Parameters.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) name.
  * **`functionName`** : Orchestrator function name.
  * **`instanceId`** : Durable Functions instanceId.
  * **`reason`** : Additional data associated with the tracking event. 有关详细信息，请参阅 [Durable Functions 中的诊断 (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Orchestration Runtime Status. 值为 Running、Completed、Failed 和 Canceled。
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Event time (UTC).
* **`dataVersion`** : Version of the lifecycle event schema.
* **`metadataVersion`** :  Version of the metadata.
* **`topic`** : Event grid topic resource.

## <a name="how-to-test-locally"></a>如何在本地测试

若要在本地测试，请使用 [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的实例管理](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的版本控制](durable-functions-versioning.md)