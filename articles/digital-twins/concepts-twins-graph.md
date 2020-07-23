---
title: 数字孪生和孪生图
titleSuffix: Azure Digital Twins
description: 了解数字克隆的概念，以及它们之间的关系是如何制作图形的。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 955a3b8d12eb3b93bc9d44c624953cd5c1007318
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258218"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>了解数字孪生及其克隆图形

在 Azure 数字孪生解决方案中，环境中的实体由 Azure**数字孪生**表示。 数字克隆是一个自定义[模型](concepts-models.md)的实例。 它可以通过**关系**连接到其他数字孪生以形成一个克隆**图形**：此双子图形是整个环境的表示形式。

> [!TIP]
> "Azure 数字孪生" 是指作为一个整体的 Azure 服务。 "数字双子 (s) " 或仅 " () " 的 "克隆" 指的是服务实例中的各个不同的节点。

## <a name="digital-twins"></a>数字孪生

你需要将*模型*上传到服务，然后才能在 Azure 数字孪生实例中创建数字输出。 模型描述了一组属性、遥测消息和特定的非特定克隆可以具有的关系，等等。 有关在模型中定义的信息类型，请参阅[概念：自定义模型](concepts-models.md)。

创建和上载模型后，客户端应用可以创建类型的实例;这是一种数字克隆。 例如，在创建*楼层*模型后，可以创建一个或多个使用此 (类型的数字孪生，例如名为*GroundFloor*的*楼层*类型，另一个称为*Floor2*，依此类推。 ) 。 

## <a name="relationships-a-graph-of-digital-twins"></a>关系：数字孪生图形

孪生通过其关系连接到一个克隆图形中。 克隆可以具有的关系定义为其模型的一部分。  

例如，模型*楼层*可能定义一个*contains*关系，该关系面向孪生的类型*空间*。 借助此定义，Azure 数字孪生将允许你创建*包含*从任何*楼层*到任何*空间*的源的关系， (包括) *空间*子类型的孪生。 

此过程的结果是一组节点 (数字孪生) 通过边缘 (它们在关系图中的关系) 连接的。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>通过 Api 创建

此部分显示从客户端应用程序创建数字孪生和关系的外观。 它包含使用[DigitalTwins api](how-to-use-apis-sdks.md)的 .net 代码示例，以提供有关每个概念中的内容的其他上下文。

### <a name="create-digital-twins"></a>创建数字孪生

下面是使用[DigitalTwins api](how-to-use-apis-sdks.md)来实例化类型*空间*的克隆的客户端代码的代码段。

在当前预览的 Azure 数字孪生中，必须先初始化所有克隆的属性，然后才能创建克隆。 这是通过创建一个提供必要初始化值的 JSON 文档来完成的。

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

### <a name="create-relationships"></a>创建关系

下面是一些客户端代码示例，它使用[DigitalTwins api](how-to-use-apis-sdks.md)在名为*GroundFloor*的*楼层*类型数字硬编码与称为*咖啡馆*的*房间*类型数字硬编码之间建立关系。

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>Graph 元素的 JSON 表示形式

数字克隆数据和关系数据均以 JSON 格式存储。 这意味着，当你在 Azure 数字孪生实例中[查询双子图形](how-to-query-graph.md)时，结果将是已创建的数字孪生和关系的 JSON 表示形式。

### <a name="digital-twin-json-format"></a>数字输出 JSON 格式

当表示为 JSON 对象时，数字克隆将显示以下字段：

| 字段名 | 说明 |
| --- | --- |
| `$dtId` | 一个用户提供的字符串，表示数字硬编码的 ID |
| `$etag` | Web 服务器分配的标准 HTTP 字段 |
| `$conformance` | 包含此数字克隆的一致性状态的枚举 (*相容*、*不相容*、*未知*)  |
| `{propertyName}` | JSON 中的属性的值 (`string` 、数字类型或对象)  |
| `$relationships` | 关系集合的路径的 URL。 如果数字双子没有传出关系边缘，则不存在此字段。 |
| `$metadata.$model` | 可有可无表示此数字输出的模型接口 ID |
| `$metadata.{propertyName}.desiredValue` | [仅用于可写属性]指定属性的所需值。 |
| `$metadata.{propertyName}.desiredVersion` | [仅用于可写属性]所需值的版本 |
| `$metadata.{propertyName}.ackVersion` | 实现数字克隆的设备应用确认的版本 |
| `$metadata.{propertyName}.ackCode` | [仅用于可写属性]`ack`实现数字克隆的设备应用返回的代码 |
| `$metadata.{propertyName}.ackDescription` | [仅用于可写属性]`ack`实现数字克隆的设备应用返回的说明 |
| `{componentName}` | 一个 JSON 对象，包含组件的属性值和元数据，类似于根对象的属性值和元数据。 即使组件没有属性，此对象也存在。 |
| `{componentName}.{propertyName}` | JSON 中的组件属性的值 (`string` 、数字类型或对象)  |
| `{componentName}.$metadata` | 组件的元数据信息，类似于根级别`$metadata` |

下面是一个 JSON 对象格式的数字克隆示例：

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>关系 JSON 格式

当表示为 JSON 对象时，来自数字克隆的关系将显示以下字段：

| 字段名 | 说明 |
| --- | --- |
| `$relationshipId` | 一个用户提供的表示此关系的 ID 的字符串。 此字符串在源数字克隆的上下文中是唯一的，这也意味着 `sourceId`  +  `relationshipId` 在 Azure 数字孪生实例的上下文中是唯一的。 |
| `$etag` | Web 服务器分配的标准 HTTP 字段 |
| `$sourceId` | 源数字克隆的 ID |
| `$targetId` | 目标数字克隆的 ID |
| `$relationshipName` | 关系的名称 |
| `{propertyName}` | 可有可无此关系的属性的值，以 JSON (`string` 、数字类型或对象)  |

下面是格式为 JSON 对象的关系的示例：

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>后续步骤

请参阅如何通过 Azure 数字克隆 Api 管理图形元素：
* [操作说明：管理数字孪生](how-to-manage-twin.md)
* [如何：管理包含关系的双子图形](how-to-manage-graph.md)

或者，有关查询 Azure 数字孪生克隆图形的信息，请参阅：
* [概念：查询语言](concepts-query-language.md)