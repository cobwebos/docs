---
title: 从 Durable Functions 发布到 Azure 事件网格（预览）
description: 了解如何配置 Durable Functions 的自动 Azure 事件网格发布。
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535479"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>从 Durable Functions 发布到 Azure 事件网格（预览）

本文介绍了如何设置 Durable Functions，以便将业务流程生命周期事件（例如“已创建”、“已完成”和“失败”）发布到自定义的 [Azure 事件网格主题](https://docs.microsoft.com/azure/event-grid/overview)。

此功能在以下场景中非常有用：

* **DevOps 方案（如蓝色/绿色部署**）：在实施[并行部署策略](durable-functions-versioning.md#side-by-side-deployments)之前，您可能需要了解是否有任何任务正在运行。

* **高级监视和诊断支持**：您可以跟踪针对查询（如 Azure SQL 数据库或 Azure Cosmos DB）优化的外部存储中的业务流程状态信息。

* **长时间运行的后台活动**：如果对长时间运行的后台活动使用 Durable Functions，此功能有助于了解当前状态。

## <a name="prerequisites"></a>先决条件

* 在 Durable Functions 项目中安装 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)。
* 安装[Azure 存储仿真程序](../../storage/common/storage-use-emulator.md)（仅限 Windows）或使用现有的 Azure 存储帐户。
* 安装 [Azure CLI ](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 或使用 [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>创建自定义事件网格主题

创建事件网格主题，以便从 Durable Functions 发送事件。 以下说明介绍如何使用 Azure CLI 创建主题。 也可以[通过使用 PowerShell](../../event-grid/custom-event-quickstart-powershell.md)或使用[Azure 门户](../../event-grid/custom-event-quickstart-portal.md)来执行此操作。

### <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令创建资源组。 目前，Azure 事件网格不支持所有区域。 有关支持哪些区域的信息，请参阅 [Azure 事件网格概述](../../event-grid/overview.md)。

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向该终结点发布事件。 用主题的唯一名称替换 `<topic_name>`。 主题名称必须唯一，因为它将用作 DNS 条目。

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>获取终结点和密钥

获取主题的终结点。 将 `<topic_name>` 替换为所选的名称。

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

获取主题密钥。 将 `<topic_name>` 替换为所选的名称。

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

现在，可将事件发送到主题。

## <a name="configure-event-grid-publishing"></a>配置事件网格发布

在 Durable Functions 项目中，找到 `host.json` 文件。

### <a name="durable-functions-1x"></a>Durable Functions 1.x

在 `durableTask` 属性中添加 `eventGridTopicEndpoint` 和 `eventGridKeySettingName`。

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

向文件`notifications``durableTask`的属性添加节，`<topic_name>`替换为您选择的名称。 如果`durableTask`不存在`extensions`或 属性，请像以下示例一样创建它们：

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

可能的 Azure 事件网格配置属性可以在 [host.json 文档](../functions-host-json.md#durabletask)中找到。 配置`host.json`文件后，函数应用会向事件网格主题发送生命周期事件。 当您在本地和 Azure 中运行函数应用时，这一点有效。

在函数应用和 `local.settings.json` 中设置主题密钥的应用设置。 以下 JSON 是用于本地调试的 `local.settings.json` 示例。 将 `<topic_key>` 替换为主题密钥。  

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

如果使用[存储仿真器](../../storage/common/storage-use-emulator.md)（仅限 Windows），请确保它正常工作。 最好是在执行之前运行 `AzureStorageEmulator.exe clear all` 命令。

如果使用现有的 Azure 存储帐户，请将其连接`UseDevelopmentStorage=true`字符串`local.settings.json`替换。

## <a name="create-functions-that-listen-for-events"></a>创建用于侦听事件的函数

使用 Azure 门户，创建另一个函数应用来侦听持久功能应用发布的事件。 最好将其定位在事件网格主题的同一区域中。

### <a name="create-an-event-grid-trigger-function"></a>创建事件网格触发器函数

创建用于接收生命周期事件的函数。 选择“自定义函数”。****

![选择“创建自定义函数”。](./media/durable-functions-event-publishing/functions-portal.png)

选择事件网格触发器，然后选择一种语言。

![选择“事件网格触发器”。](./media/durable-functions-event-publishing/eventgrid-trigger.png)

输入函数的名称，然后选择 `Create`。

![创建事件网格触发器。](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

创建包含以下代码的函数：

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

选择 `Add Event Grid Subscription`。 此操作为添加的事件网格主题创建事件网格订阅。 有关详细信息，请参阅 [Azure 事件网格中的概念](https://docs.microsoft.com/azure/event-grid/concepts)。

![选择“事件网格触发器”链接。](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

为“主题类型”选择 `Event Grid Topics`。**** 选择为事件网格主题创建的资源组。 然后选择事件网格主题的实例。 按 `Create`。

![创建事件网格订阅。](./media/durable-functions-event-publishing/eventsubscription.png)

现已准备好接收生命周期事件。

## <a name="run-durable-functions-app-to-send-the-events"></a>运行持久功能应用以发送事件

在前面配置的"持久功能"项目中，在本地计算机上开始调试并开始业务流程。 该应用程序将持久函数生命周期事件发布到事件网格。 通过在 Azure 门户中检查事件网格的日志，验证事件网格是否触发您创建的侦听器函数。

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

* **`id`**：事件网格事件的唯一标识符。
* **`subject`**： 事件主题的路径。 `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` 为 `Running`、`Completed`、`Failed` 和 `Terminated`。  
* **`data`**：持久函数特定参数。
  * **`hubName`**：[任务中心](durable-functions-task-hubs.md)名称。
  * **`functionName`**：协调器函数名称。
  * **`instanceId`**：持久函数实例Id。
  * **`reason`**：与跟踪事件关联的其他数据。 有关详细信息，请参阅 [Durable Functions 中的诊断 (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**：业务流程运行时状态。 值为 Running、Completed、Failed 和 Canceled。
* **`eventType`**："协调器事件"
* **`eventTime`**：事件时间 （UTC）。
* **`dataVersion`**：生命周期事件架构的版本。
* **`metadataVersion`**：元数据的版本。
* **`topic`**：事件网格主题资源。

## <a name="how-to-test-locally"></a>如何在本地测试

要在本地进行测试，请阅读[Azure 函数事件网格触发本地调试](../functions-debug-event-grid-trigger-local.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的实例管理](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的版本控制](durable-functions-versioning.md)
