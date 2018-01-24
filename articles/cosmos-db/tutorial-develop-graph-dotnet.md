---
title: "Azure Cosmos DB：在 .NET 中使用图形 API 进行开发 | Microsoft Docs"
description: "了解如何通过 .NET 使用 Azure Cosmos DB 的 SQL API 进行开发"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ddbfe11e4415e1c240914142f4daf54b3032f5d8
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB：在 .NET 中使用图形 API 进行开发
Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户，以及如何创建图形数据库和容器。 接着，应用程序将使用[图形 API](graph-sdk-dotnet.md)创建四个人的简易社交网络，然后使用 Gremlin 遍历并查询图形。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户 
> * 创建图形数据库和容器
> * 序列化 .NET 对象的顶点和边缘
> * 添加顶点和边缘
> * 使用 Gremlin 查询图形

## <a name="graphs-in-azure-cosmos-db"></a>Azure Cosmos DB 中的图形
可使用 Azure Cosmos DB，通过 [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) 库创建、更新和查询图形。 Microsoft.Azure.Graph 库提供基于 `DocumentClient` 类的单个扩展方法 `CreateGremlinQuery<T>`，用于执行 Gremlin 查询。

Gremlin 是功能性编程语言，支持写入操作 (DML) 与查询和遍历操作。 本文提供有关 Gremlin 入门的几个示例。 有关 Azure Cosmos DB 中可用的 Gremlin 功能的详细演练，请参阅 [Gremlin 查询](gremlin-support.md)。 

## <a name="prerequisites"></a>先决条件
请确保具有以下内容：

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。 
    * 另外，也可在本教程中使用[本地仿真器](local-emulator.md)。
* [Visual Studio](http://www.visualstudio.com/)。

## <a name="create-database-account"></a>创建数据库帐户

现在首先在 Azure 门户中创建 Azure Cosmos DB 帐户。  

> [!TIP]
> * 已有一个 Azure Cosmos DB 帐户？ 如果有，请跳到[设置 Visual Studio 解决方案](#SetupVS)
> * 如果使用 Azure Cosmos DB Emulator，请遵循 [Azure Cosmos DB Emulator](local-emulator.md) 中的步骤设置该模拟器，并直接跳到[设置 Visual Studio 解决方案](#SetupVS)。 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>设置 Visual Studio 解决方案
1. 在计算机上打开 Visual Studio。
2. 在“文件”菜单中，选择“新建”，并选择“项目”。
3. 在“新建项目”对话框中，选择“模板” / “Visual C#” / “控制台应用(.NET Framework)”，为项目命名，并单击“确定”。
4. 在“解决方案资源管理器”中，右键单击 Visual Studio 解决方案下方的新控制台应用程序，并单击“管理 NuGet 包...”
5. 在“NuGet”选项卡上单击“浏览”，并在搜索框中键入 Microsoft.Azure.Graphs，并选中“包括预发布版本”。
6. 在结果中找到“Microsoft.Azure.Graphs”，并单击“安装”。
   
   如果获得有关查看解决方案更改的消息，请单击“确定”。 如果获得有关接受许可证的消息，请单击“我接受”。
   
    `Microsoft.Azure.Graphs` 库提供用于执行 Gremlin 操作的单个扩展方法 `CreateGremlinQuery<T>`。 Gremlin 是功能性编程语言，支持写入操作 (DML) 与查询和遍历操作。 本文提供有关 Gremlin 入门的几个示例。 [Gremlin 查询](gremlin-support.md)中详细介绍了有关 Azure Cosmos DB 中的 Gremlin 功能的演练。

## <a id="add-references"></a>连接应用

在应用程序中添加这两个常量和客户端变量。 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
接下来，返回到 [Azure 门户](https://portal.azure.com)检索终结点 URL 和主密钥。 终结点 URL 和主密钥是必需的，可让应用程序知道要连接的对象，使 Azure Cosmos DB 信任应用程序的连接。 

在 Azure 门户中，导航到 Azure Cosmos DB 帐户，单击“密钥”，并单击“读写密钥”。 

从门户复制 URI，并将其粘贴到上述终结点属性中的 `Endpoint` 上。 然后从门户复制“PRIMARY KEY”并将其粘贴到上述 `AuthKey` 属性。 

![教程用于创建 C# 应用程序的 Azure 门户的屏幕截图。 显示一个 Azure Cosmos DB 帐户，其中在 Azure Cosmos DB 导航中突出显示了“密钥”按钮，并在“密钥”边栏选项卡上突出显示了 URI 和 PRIMARY KEY 值](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>实例化 DocumentClient 
接下来，创建 DocumentClient 的新实例。  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>创建数据库 

现在，从 [SQL .NET SDK](sql-api-sdk-dotnet.md) 使用 DocumentClient 类的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法或 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 方法创建一个 Azure Cosmos DB [数据库](sql-api-resources.md#databases)。  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>创建图形 

接下来，使用 DocumentClient 类的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法或 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 方法创建图形容器。 集合是图形实体的容器。 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>序列化 .NET 对象的顶点和边缘
Azure Cosmos DB 使用 [GraphSON 传输格式](gremlin-support.md)，后者定义了用于顶点、边缘和属性的 JSON 架构。 Azure Cosmos DB .NET SDK 包括 JSON.NET（作为依赖项），由此可将 GraphSON 序列化/反序列化为可在代码中使用的 .NET 对象。

为举例，现来处理四个人的简易社交网络。 来看看如何创建 `Person` 顶点、在它们之间添加 `Knows` 关系，并查询并遍历该图形来查找“朋友的朋友”关系。 

`Microsoft.Azure.Graphs.Elements` 命名空间提供 `Vertex`、`Edge`、`Property` 和 `VertexProperty` 类，用于反序列化对明确定义的 .NET 对象的 GraphSON 响应。

## <a name="run-gremlin-using-creategremlinquery"></a>使用 CreateGremlinQuery 运行 Gremlin
SQL 等 Gremlin 支持读取、写入和查询操作。 作为示例，以下代码片段演示如何创建顶点、边缘，如何使用 `CreateGremlinQuery<T>` 执行一些示例查询并以异步方式使用 `ExecuteNextAsync` 和 `HasMoreResults 循环访问这些结果。

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>添加顶点和边缘

现在，更仔细地查看前面部分中显示的 Gremlin 语句。 首先，使用 Gremlin 的 `addV` 方法创建一些顶点。 例如，以下代码片段创建“Person”类型的“Thomas Andersen”顶点，具有名字、姓氏和年龄的属性。

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

然后使用 Gremlin 的 `addE` 方法在这些顶点之间创建一些边缘。 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

可以通过使用 Gremlin 中的 `properties` 步骤更新现有顶点。 对于其余示例，通过 `HasMoreResults` 和 `ExecuteNextAsync` 跳过调用来执行查询。

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

可以使用 Gremlin 的 `drop` 步骤移除边缘和顶点。 以下代码片段演示如何删除顶点和边缘。 请注意，删除顶点这一操作将执行关联边缘的级联删除。

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>查询图形

还可以使用 Gremlin 执行查询和遍历。 例如，以下代码片段演示如何对图中的顶点进行计数：

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
可通过 Gremlin 的 `has` 和 `hasLabel` 步骤执行筛选器，并使用 `and`、`or` 和 `not` 将其组合，创建更复杂的筛选器：

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

可使用 `values` 步骤对查询结果中的某些属性进行投影：

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

目前为止，我们仅介绍了适用于任何数据库的查询运算符。 如需导航到相关边缘和顶点，可以使用图快速高效地进行遍历操作。 查找 Thomas 的所有朋友。 为此，可使用 Gremlin 的 `outE` 步骤从 Thomas 中查找所有外缘，并使用 Gremlin 的 `inV` 步骤从这些边缘遍历至内顶点：

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

下一查询通过调用 `outE` 和 `inV` 两次，执行两个跃点查找 Thomas 所有的“朋友的朋友”。 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

可使用 Gremlin 构建更加复杂的查询和实现功能强大图遍历逻辑，包括混合筛选表达式、使用 `loop` 步骤执行循环以及使用 `choose` 步骤实现条件导航。 深入了解通过 [Gremlin 支持](gremlin-support.md)可实现的操作！

现在，Azure Cosmos DB 教程已完结！ 

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请使用以下步骤删除本教程在 Azure 门户中创建的所有资源。  

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户 
> * 创建图形数据库和容器
> * 序列化 .NET 对象的顶点和边缘
> * 添加顶点和边缘
> * 使用 Gremlin 查询图形

现可使用 Gremlin 构建更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)
