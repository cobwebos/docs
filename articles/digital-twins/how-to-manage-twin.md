---
title: 管理数字孪生
titleSuffix: Azure Digital Twins
description: 请参阅如何检索、更新和删除单个孪生和关系。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9f140594ef18df7f9a6a3b919998962c966cde76
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587593"
---
# <a name="manage-digital-twins"></a>管理数字孪生

环境中的实体由 [数字孪生](concepts-twins-graph.md)表示。 管理数字孪生可能包括创建、修改和删除。 若要执行这些操作，可以使用 [**DigitalTwins api**](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或 [Azure 数字孪生 CLI](how-to-use-cli.md)。

本文重点介绍如何管理数字孪生;若要整体处理关系和整数 [关系图](concepts-twins-graph.md) ，请参阅 [*操作方法：管理包含关系的双子关系图*](how-to-manage-graph.md)。

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

## <a name="create-a-digital-twin"></a>创建数字输出

若要创建克隆，请在 `CreateDigitalTwin` 服务客户端上使用方法，如下所示：

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

若要创建数字克隆，需要提供：
* 数字克隆所需的 ID
* 要使用的[模型](concepts-models.md) 

（可选）可以为数字输出的所有属性提供初始值。 

模型和初始属性值通过 `initData` 参数提供，它是一个包含相关数据的 JSON 字符串。 有关构造此对象的详细信息，请转到下一节。

> [!TIP]
> 创建或更新克隆后，可能会有长达10秒的延迟，更改将反映在 [查询](how-to-query-graph.md)中。 `GetDigitalTwin`[本文后面](#get-data-for-a-digital-twin)所述的 API () 不会遇到此延迟，因此，如果需要即时响应，请使用 api 调用而不是查询来查看新创建的孪生。 

### <a name="initialize-model-and-properties"></a>初始化模型和属性

克隆创建 API 接受序列化为克隆属性的有效 JSON 说明的对象。 请参阅 " [*概念：数字孪生" 和 "克隆图形"*](concepts-twins-graph.md) ，以获取对一种对的 JSON 格式的说明。 

首先，您将创建一个数据对象来表示克隆及其属性数据。 然后，可以使用 `JsonSerializer` 将的序列化版本传递到用于参数的 API 调用 `initdata` 。

可以手动创建参数对象，也可以使用提供的帮助器类创建参数对象。 下面是每个的示例。

#### <a name="create-twins-using-manually-created-data"></a>使用手动创建的数据创建孪生

如果不使用任何自定义帮助器类，则可以在中表示克隆的属性 `Dictionary<string, object>` ，其中 `string` 是属性的名称， `object` 是表示属性及其值的对象。

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>用 helper 类创建孪生

使用的帮助程序类，可以 `BasicDigitalTwin` 更直接地将属性字段存储在 "双子" 对象中。 你仍可以使用生成属性列表 `Dictionary<string, object>` ，然后可以将其直接添加到克隆的对象 `CustomProperties` 。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

>[!NOTE]
> `BasicDigitalTwin` 对象附带了一个 `Id` 字段。 可以将此字段保留为空，但如果添加了一个 ID 值，则它需要与传递给调用的 ID 参数匹配 `CreateDigitalTwin` 。 对于上面的示例，此示例如下所示：
>
>```csharp
>twin.Id = "myNewRoomID";
>```

## <a name="get-data-for-a-digital-twin"></a>获取数字克隆的数据

可以通过调用以下方法访问任何数字克隆的完整数据：

```csharp
object result = await client.GetDigitalTwin(id);
```

此调用返回作为 JSON 字符串的未克隆数据。 

> [!TIP]
> 当你使用检索到次时，仅返回至少已设置一次的属性 `GetDigitalTwin` 。

若要使用单个 API 调用检索多个孪生，请参阅 [*如何：查询双子图*](how-to-query-graph.md)中的查询 API 示例。

请考虑以下模型 (以 [数字孪生定义语言编写， (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) 定义 *月球*：

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

`object result = await client.DigitalTwins.GetByIdAsync("my-moon");`对*月亮*类型的硬类型调用的结果如下所示：

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

数字克隆的已定义属性在数字克隆上作为顶级属性返回。 不属于 DTDL 定义的元数据或系统信息将以 `$` 前缀返回。 元数据属性包括：
* 此 Azure 数字孪生实例中数字输出的 ID，如 `$dtId` 。
* `$etag`，由 web 服务器分配的标准 HTTP 字段
* 节中的其他属性 `$metadata` 。 其中包括：
    - 数字克隆的模型的 DTMI。
    - 每个可写属性的同步状态。 这对于设备最为有用，在这种情况下，在设备处于) 脱机状态时，服务和设备可能会 (分叉状态。 目前，此属性仅适用于连接到 IoT 中心的物理设备。 使用元数据部分中的数据，可以了解属性的完整状态以及上次修改的时间戳。 有关同步状态的详细信息，请参阅有关同步设备状态的 [此 IoT 中心教程](../iot-hub/tutorial-device-twins.md) 。
    - 服务特定的元数据，如 IoT 中心或 Azure 数字孪生。 

您可以使用所选的 JSON 分析库（如）分析返回的 JSON `System.Text.Json` 。

你还可以使用 SDK 随附的序列化帮助器类 `BasicDigitalTwin` ，它将返回以预分析形式返回的核心数据和属性。 下面是一个示例：

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

有关序列化帮助器类的详细信息， [*请参阅如何：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)。

## <a name="update-a-digital-twin"></a>更新数字孪生

若要更新数字克隆的属性，请编写要替换为 [JSON 修补](http://jsonpatch.com/) 格式的信息。 通过这种方式，可以一次替换多个属性。 然后将 JSON 修补文档传递到方法中 `Update` ：

```csharp
await client.UpdateDigitalTwin(id, patch);
```

修补调用可以更新单个克隆上的多个属性， (甚至所有这些属性都) 。 如果需要跨多个孪生更新属性，则每个克隆都需要单独的更新调用。

> [!TIP]
> 创建或更新克隆后，可能会有长达10秒的延迟，更改将反映在 [查询](how-to-query-graph.md)中。 `GetDigitalTwin`[本文前面](#get-data-for-a-digital-twin)所述的 API () 不会遇到此延迟，因此，如果需要即时响应，请使用 api 调用而不是查询来查看新更新的孪生。 

下面是 JSON 修补程序代码的示例。 此文档替换其所应用到的数字输出的 *质量* 和 *半径* 属性值。

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

可以手动创建修补程序，也可以使用 [SDK](how-to-use-apis-sdks.md)中的序列化帮助器类创建修补程序。 下面是每个的示例。

#### <a name="create-patches-manually"></a>手动创建修补程序

```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>使用 helper 类创建修补程序

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>更新数字克隆组件中的属性

请记住，模型可以包含组件，从而使其可由其他模型组成。 

若要在数字克隆的组件中修补属性，你将使用 JSON 修补程序中的路径语法：

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>更新数字输出的模型

`Update`函数还可用于将数字克隆迁移到其他模型。 

例如，请考虑以下 JSON 修补程序文档，该文档将替换数字克隆的元数据 `$model` 字段：

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

仅当修补程序修改的数字克隆符合新的模型时，此操作才会成功。 

请考虑以下示例：
1. 假设有一个数字克隆，其型号为 *foo_old*。 *foo_old* 定义所需的属性 *质量*。
2. 新模型 *foo_new* 定义属性质量并添加新的必需属性 *温度*。
3. 修补后，数字克隆必须同时具有质量和温度属性。 

此情况的修补程序需要更新模型和克隆的温度属性，如下所示：

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>处理冲突的更新调用

Azure 数字孪生确保所有传入的请求经过一次处理。 这意味着即使多个函数尝试同时更新静态上的相同属性，也 **不需要** 编写显式锁定代码来处理冲突。

此行为以每个随机的方式进行。 

例如，假设这三个调用同时到达的情况如下： 
*   在*Twin1*上写入属性 A
*   在*Twin1*上写入属性 B
*   在*Twin2*上写入属性 A

修改 *Twin1* 的两个调用会再执行一次，并为每个更改生成更改消息。 修改 *Twin2* 的调用可能会在不冲突的情况下同时执行，而不会发生冲突。

## <a name="delete-a-digital-twin"></a>删除数字克隆

您可以使用删除孪生 `DeleteDigitalTwin(ID)` 。 但是，如果没有更多的关系，则只能删除一个克隆。 必须首先删除所有关系。 

下面是此代码的一个示例：

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>删除所有数字孪生

有关如何一次删除所有孪生的示例，请下载教程中使用的示例应用 [*：使用示例客户端应用了解基础知识*](tutorial-command-line-app.md)。 *CommandLoop.cs*文件在函数中执行此 `CommandDeleteAllTwins` 功能。

## <a name="manage-twins-with-cli"></a>用 CLI 管理孪生

还可以使用 Azure 数字孪生 CLI 管理孪生。 有关命令，请参阅 [*操作方法：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)。

## <a name="next-steps"></a>后续步骤

请参阅如何创建和管理数字孪生之间的关系：
* [*如何：管理包含关系的双子图形*](how-to-manage-graph.md)