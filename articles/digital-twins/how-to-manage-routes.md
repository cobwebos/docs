---
title: 管理终结点和路由
titleSuffix: Azure Digital Twins
description: 请参阅如何设置和管理 Azure 数字孪生数据的终结点和事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8c9fbf7bc45ed2070570faf0d1dfdb15b5fd98ee
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373260"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>在 Azure 数字孪生中管理终结点和路由

在 Azure 数字孪生中，可以将[事件通知](how-to-interpret-event-data.md)路由到下游服务，或连接到计算资源。 这是通过首先设置可接收事件的**终结点**，然后是指定由 Azure 数字孪生生成的事件传递到哪些终结点的[**事件路由**](concepts-route-events.md)来完成的。

支持的终结点类型包括：
* [事件中心](../event-hubs/event-hubs-about.md)
* [事件网格](../event-grid/overview.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

有关不同终结点的详细信息，请参阅在[*Azure 消息服务之间选择*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)。

终结点和路由通过[**EventRoutes api**](how-to-use-apis-sdks.md)、 [.Net （c #） SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 数字孪生 CLI](how-to-use-cli.md)进行管理。 还可以通过[Azure 门户](https://portal.azure.com)进行管理。

## <a name="create-an-endpoint-for-azure-digital-twins"></a>为 Azure 数字孪生创建终结点

若要将终结点链接到 Azure 数字孪生，需要为终结点使用的事件中心、事件网格主题或服务总线。 

下面的示例演示如何使用 Azure CLI 创建事件网格主题：

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> 要输出可传递到 Azure CLI 命令中的 Azure 区域名称的列表，请运行以下命令：
> ```azurecli
> az account list-locations -o table
> ```

创建主题后，可以使用以下命令将其链接到 Azure 数字孪生：

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

这样，就可以使用参数指定的名称，在 Azure 数字孪生内部提供事件网格主题 `--endpoint-name` 。 通常会将该名称用作**事件路由**的目标，将在下一节中使用 Azure 数字孪生服务 API 创建该路由。

事件中心和服务总线终结点有等效的命令：

* 添加服务总线主题终结点（需要预先创建的服务总线资源）
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* 添加事件中心终结点（需要预先创建的事件中心资源）
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>用 Api 管理事件路由

若要将数据从 Azure 数字孪生实际发送到终结点，需要定义事件路由。 通过 Azure 数字孪生**EventRoutes api** ，开发人员可以将事件流连接到整个系统和下游服务。 有关事件路由的详细信息，请参阅[*概念：路由 Azure 数字孪生事件*](concepts-route-events.md)。

终结点完成设置后，可以继续创建事件路由。

>[!NOTE]
>如果你最近部署了终结点，请验证它们是否已完成部署，**然后再**尝试将它们用于新的事件路由。 如果由于终结点尚未准备好而导致路由部署失败，请等待几分钟，然后重试。
>
> 如果要编写此流脚本，可能需要在2-3 分钟的等待时间内生成终结点服务，然后再继续进行路由设置。

本文中的示例使用 c # SDK。

使用数据平面 Api 定义事件路由。 路由定义可包含以下元素：
* 要使用的路由 ID
* 要使用的终结点的名称
* 定义要发送到终结点的事件的筛选器 

如果没有路由 ID，则不会在 Azure 数字孪生之外路由任何消息。 如果有路由 ID 且筛选器为，则 `true` 所有消息都将路由到该终结点。 如果已添加路由 ID 并添加了不同的筛选器，则将根据筛选器筛选消息。

一个路由应该允许选择多个通知和事件类型。 

`CreateEventRoute`用于添加事件路由的 SDK 调用。 下面是其用法的示例：

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

### <a name="event-route-sample-code"></a>事件路由示例代码

下面的代码示例演示如何创建、列出和删除事件路由：
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>筛选事件

如果没有筛选，终结点就会收到来自 Azure 数字孪生的各种事件：
* [数字孪生](concepts-twins-graph.md)使用 Azure 数字孪生服务 API 触发的遥测数据
* 克隆属性更改通知，对 Azure 数字孪生实例中任何克隆的属性更改触发
* 生命周期事件，在创建或删除孪生或关系时触发
* 添加或删除在 Azure 数字孪生实例中配置的[模型](concepts-models.md)时触发的模型更改事件

可以通过向终结点添加筛选器来限制正在发送的事件。

若要添加筛选器，可以使用具有以下正文的 PUT 请求到 https：/ */{YourHost}/EventRoutes/myNewRoute？ api 版本 = 2020-05-31-preview* ：

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

下面是受支持的路由筛选器。

| 筛选器名称 | 说明 | 筛选架构 | 支持的值 | 
| --- | --- | --- | --- |
| 类型 | 通过数字克隆实例流动的[事件类型](./concepts-route-events.md#types-of-event-messages) | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 源 | Azure 数字孪生实例的名称 | `"filter" : "source = '<hostname>'"`|  **对于通知**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **对于遥测**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 使用者 | 上述事件源上下文中事件的说明 | `"filter": " subject = '<subject>'"` | **对于通知**：使用者为`<twinid>` <br> 或主题的 URI 格式，由多个部件或 Id 唯一标识：<br>`<twinid>/relationships/<relationshipid>`<br> **对于遥测**： "主题" 是组件路径（如果从 "克隆" 组件发出遥测数据），例如 `comp1.comp2` 。 如果未从组件发出遥测数据，则其 "使用者" 字段为空。 |
| 数据架构 | DTDL 模型 ID | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **对于遥测**：数据架构是克隆或发出遥测数据的组件的模型 ID <br>**对于通知**：不支持数据架构|
| 内容类型 | 数据值的内容类型 | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| 规范版本 | 你使用的事件架构的版本 | `"filter": "specversion = '<version>'"` | 必须是 `1.0`。 这表示 CloudEvents 架构版本1。0 |
| True/False | 允许创建无筛选或禁用路由的路由 | `"filter" : "<true/false>"` | `true`= 启用无筛选的路由 <br> `false`= 已禁用路由 |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

还可以使用以下操作合并筛选器：

| 筛选器名称 | 筛选架构 | 示例 | 
| --- | --- | --- |
| AND/OR | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| AND/OR | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| 嵌套操作 | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> 预览期间，仅支持字符串相等性（=、！ =）。

实现或更新筛选器时，更改可能需要几分钟才会反映在数据管道中。

## <a name="manage-endpoints-and-routes-with-cli"></a>用 CLI 管理终结点和路由

还可以使用 Azure 数字孪生 CLI 管理终结点和路由。 有关命令，请参阅[*操作方法：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)。

## <a name="monitor-event-routes"></a>监视事件路由

可以在[Azure 门户](https://portal.azure.com/)中查看路由指标，如计数、延迟和失败率。 

在门户主页上，搜索 Azure 数字孪生实例以提取其详细信息。 从 Azure 数字孪生实例的菜单中选择 "**指标**" 选项，打开 "*指标*" 页。

:::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="显示 Azure 数字孪生的 "指标" 页的屏幕截图":::

在此处，你可以查看实例的度量值并创建自定义视图。

## <a name="next-steps"></a>后续步骤

阅读可接收的不同类型的事件消息：
* [*操作说明：解释事件数据*](how-to-interpret-event-data.md)
