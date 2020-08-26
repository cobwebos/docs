---
title: 通过使用关系来管理孪生图
titleSuffix: Azure Digital Twins
description: 请参阅如何通过将数字孪生与关系连接起来来管理该图形。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f7239e0c13478af712d8e8d9dad8fda23fe42c7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125526"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用关系管理数字孪生图

Azure 数字孪生的核心是代表您的整个环境的克隆[图](concepts-twins-graph.md)。 克隆图形由通过**关系**连接的单个数字孪生组成。

一旦拥有工作[Azure 数字孪生实例](how-to-set-up-instance-scripted.md)，并在客户端应用程序中设置了[身份验证](how-to-authenticate-client.md)代码，就可以使用[**DigitalTwins api**](how-to-use-apis-sdks.md)来创建、修改和删除 Azure 数字孪生实例中的数字孪生和它们之间的关系。 还可以使用[.net （c #） SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[AZURE 数字孪生 CLI](how-to-use-cli.md)。

本文重点介绍如何作为一个整体来管理关系和图形;若要使用单独的数字孪生，请参阅[*操作方法：管理数字孪生*](how-to-manage-twin.md)。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>创建关系

关系描述不同数字孪生彼此连接的方式，它们构成了双子图形的基础。

关系是使用调用创建的 `CreateRelationship` 。 

若要创建关系，需要指定：
* 源源源 ID （关系源自的位置）
* 目标克隆 ID （关系到达的位置的位置）
* 关系名称
* 关系 ID

关系 ID 在给定的源克隆中必须是唯一的。 它不需要全局唯一。
例如，对于双子*foo*，每个特定的关系 ID 都必须是唯一的。 但是，另一个克隆*栏*可以具有与*foo*关系的相同 ID 匹配的传出关系。 

下面的代码示例演示如何将关系添加到 Azure 数字孪生实例。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

有关 helper 类的详细信息 `BasicRelationship` ，请参阅 how [*To： Use The Azure 数字孪生 Api and sdk*](how-to-use-apis-sdks.md)。

## <a name="list-relationships"></a>列出关系

若要访问图形中给定的克隆的关系列表，可以使用：

```csharp
await client.GetRelationshipsAsync(id);
```

这会返回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，具体取决于你使用的是调用的同步还是异步版本。

下面是检索关系列表的完整示例：

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

您可以使用检索到的关系导航到图形中的其他孪生。 为此，请 `target` 从返回的关系中读取字段，并将其用作对的下一次调用的 ID `GetDigitalTwin` 。 

### <a name="find-relationships-to-a-digital-twin"></a>查找到数字输出的关系

Azure 数字孪生还提供了一个 API，用于查找到给定克隆的所有传入关系。 这通常适用于反向导航或删除克隆。

上面的代码示例重点介绍如何查找传出关系。 下面的示例类似，但却查找传入关系。 它还会在找到它们后将其删除。

请注意， `IncomingRelationship` 调用不返回关系的完整正文。

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>删除关系

您可以使用删除关系 `DeleteRelationship(source, relId);` 。

第一个参数指定源克隆（关系源自的位置）。 另一个参数是关系 ID。 由于关系 Id 只在克隆的作用域内是唯一的，因此您需要同时使用的 ID 和关系 ID。

## <a name="create-a-twin-graph"></a>创建克隆图形 

以下代码片段使用本文中的关系操作来创建数字孪生和关系的克隆图形。

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>从电子表格创建克隆图形

在实际使用案例中，会经常从存储在不同数据库中或电子表格中的数据创建克隆层次结构。 本部分说明如何分析电子表格。

请考虑以下数据表，描述要创建的一组数字孪生和关系。

| 型号    | ID | Parent | 关系名 | 其他数据 |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| 房间    | Room10 | Floor01 | contains | … |
| 房间    | Room11 | Floor01 | contains | … |
| 房间    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| 房间    | Room21 | Floor02 | contains | … |
| 房间    | Room22 | Floor02 | contains | … |

下面的代码使用[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/overview)来读取电子表格，并从结果中构造 Azure 数字孪生双子图。

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>用 CLI 管理关系

孪生及其关系也可以使用 Azure 数字孪生 CLI 进行管理。 有关命令，请参阅[*操作方法：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)。

## <a name="next-steps"></a>后续步骤

了解如何查询 Azure 数字孪生克隆图形：
* [*概念：查询语言*](concepts-query-language.md)
* [*操作方法：查询双子图形*](how-to-query-graph.md)