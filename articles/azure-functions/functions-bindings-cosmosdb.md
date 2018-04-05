---
title: 适用于函数的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: ac869cc45d352bdeed16bb3ca926ec7a921d1f75
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure Cosmos DB 绑定

本文介绍如何在 Azure Functions 中使用 [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) 绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

> [!NOTE]
> 此绑定最初名为 DocumentDB。 在 Functions 版本 1.x 中，仅触发器已重命名为 Cosmos DB；输入绑定、输出绑定和 NuGet 包保留 DocumentDB 名称。 在 [Functions 版本 2.x](functions-versions.md) 中，绑定和包也已重命名为 Cosmos DB。 本文使用 1.x 名称。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>包

[Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet 包中提供了 Functions 1.x 版的 Cosmos DB 绑定。 对于 Functions 2.x，包为 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub 存储库中提供了此绑定的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>触发器

Azure Cosmos DB 触发器使用 [Azure Cosmos DB 更改源](../cosmos-db/change-feed.md)来侦听跨分区的更改。 更改源发布插入和更新，不发布删除。 

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示一个从特定数据库和集合触发的 [C# 函数](functions-dotnet-class-library.md)。

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Cosmos DB 触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 修改 Cosmos DB 记录时，该函数会写入日志消息。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# 脚本代码如下所示：
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Cosmos DB 触发器绑定以及使用该绑定的 [JavaScript 脚本函数](functions-reference-node.md)。 修改 Cosmos DB 记录时，该函数会写入日志消息。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript 代码如下所示：

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>触发器 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[触发器 - 配置](#trigger---configuration)。 下面是某个方法签名中的 `CosmosDBTrigger` 特性示例：

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

有关完整示例，请参阅[触发器 - C# 示例](#trigger---c-example)。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `CosmosDBTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** || 必须设置为 `cosmosDBTrigger`。 |
|**direction** || 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置该参数。 |
|**name** || 函数代码中使用的变量名称，表示发生更改的文档列表。 | 
|**connectionStringSetting**|**ConnectionStringSetting** | 应用设置的名称，该应用设置包含用于连接到受监视的 Azure Cosmos DB 帐户的连接字符串。 |
|**databaseName**|**DatabaseName**  | 带有受监视的集合的 Azure Cosmos DB 数据库的名称。 |
|**collectionName** |**CollectionName** | 受监视的集合的名称。 |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | （可选）应用设置的名称，该应用设置包含指向保留租用集合的服务的连接字符串。 未设置时，使用 `connectionStringSetting` 值。 在门户中创建绑定时，将自动设置该参数。 用于租用集合的连接字符串必须具有写入权限。|
|**leaseDatabaseName** |**LeaseDatabaseName** | （可选）数据库的名称，该数据库包含用于存储租用的集合。 未设置时，使用 `databaseName` 设置的值。 在门户中创建绑定时，将自动设置该参数。 |
|**leaseCollectionName** | **LeaseCollectionName** | （可选）用于存储租用的集合的名称。 未设置时，使用值 `leases`。 |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | （可选）设置为 `true` 时，如果租用集合并不存在，将自动创建该集合。 默认值为 `false`。 |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| （可选）在创建租用集合时，定义要分配的请求单位的数量。 仅当 `createLeaseCollectionIfNotExists` 设置为 `true` 时，才会使用该设置。 使用门户创建绑定时，将自动设置该参数。
| |**LeaseOptions** | 通过在 [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions) 类的实例中设置属性来配置租用选项。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>触发器 - 用法

触发器需要第二个集合，该集合用于存储各分区的_租用_。 必须提供受监视的集合和包含租用的集合，触发器才能正常工作。

 >[!IMPORTANT]
 > 如果多个函数都配置为对同一集合使用 Cosmos DB 触发器，则每个函数都应使用专用租用集合。 否则，将只触发其中一个函数。 

该触发器并不指示是更新还是插入文档，它仅提供文档本身。 如果需要以不同方式处理更新和插入，可通过实现用于插入或更新的时间戳字段来执行该操作。

## <a name="input"></a>输入

Azure Cosmos DB 输入绑定会检索一个或多个 Azure Cosmos DB 文档，并将其传递给函数的输入参数。 可根据调用函数的触发器确定文档 ID 或查询参数。 

>[!NOTE]
> 如果要对 Cosmos DB 帐户使用 MongoDB API，请不要使用 Azure Cosmos DB 输入或输出绑定。 可能会数据损坏。

## <a name="input---example-1"></a>输入 - 示例 1

参阅读取单个文档的特定于语言的示例：

* [C#](#input---c-example)
* [C# 脚本 (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>输入 - C# 示例

以下示例演示一个从特定数据库和集合检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 

首先，将 `CarReview` 实例的 `Id` 和 `Maker` 值传递到队列。 Cosmos DB 绑定使用队列消息中的 `Id` 和 `Maker` 从数据库检索文档。

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

下面是 `CarReview` POCO：

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>输入 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>输入 - F# 示例

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[配置](#input---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

此示例要求具有指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

若要添加 `project.json` 文件，请参阅 [F# 包管理](functions-reference-fsharp.md#package)。

### <a name="input---javascript-example"></a>输入 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>输入 - 示例 2

参阅读取多个文档的特定于语言的示例：

* [C#](#input---c-example-2)
* [C# 脚本 (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>输入 - C# 示例 2

以下示例演示一个执行 SQL 查询的 [C# 函数](functions-dotnet-class-library.md)。 若要使用 `SqlQuery` 参数，需要安装 `Microsoft.Azure.WebJobs.Extensions.DocumentDB` NuGet 包的最新 beta 版本。

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>输入 - C# 脚本示例 2

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输入绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用队列触发器自定义查询参数检索 SQL 查询指定的多个文档。

队列触发器提供参数 `departmentId`。 `{ "departmentId" : "Finance" }` 的队列消息将返回财务部的所有记录。 

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

### <a name="input---javascript-example-2"></a>输入 - JavaScript 示例 2

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输入绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用队列触发器自定义查询参数检索 SQL 查询指定的多个文档。

队列触发器提供参数 `departmentId`。 `{ "departmentId" : "Finance" }` 的队列消息将返回财务部的所有记录。 

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

## <a name="input---attributes"></a>输入 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[下面的“配置”部分](#input---configuration)。 

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件和 `DocumentDB` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型**     || 必须设置为 `documentdb`。        |
|**direction**     || 必须设置为 `in`。         |
|**name**     || 表示函数中的文档的绑定参数的名称。  |
|**databaseName** |**DatabaseName** |包含文档的数据库。        |
|**collectionName** |**CollectionName** | 包含文档的集合的名称。 |
|**id**    | **Id** | 要检索的文档的 ID。 此属性支持[绑定表达式](functions-triggers-bindings.md#binding-expressions-and-patterns)。 不要同时设置 **id** 和 **sqlQuery** 属性。 如果上述两个属性都未设置，则会检索整个集合。 |
|**sqlQuery**  |**SqlQuery**  | 用于检索多个文档的 Azure Cosmos DB SQL 查询。 该属性支持运行时绑定，如以下示例中所示：`SELECT * FROM c where c.departmentId = {departmentId}`。 不要同时设置 **id** 和 **sqlQuery** 属性。 如果上述两个属性都未设置，则会检索整个集合。|
|**连接**     |**ConnectionStringSetting**|内含 Azure Cosmos DB 连接字符串的应用设置的名称。        |
|**partitionKey**|**PartitionKey**|指定用于查找分区键值。 可以包含绑定参数。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>输入 - 用法

在 C# 函数和 F# 函数中，函数成功退出后，通过命名输入参数对输入文档所做的任何更改都会自动保存。 

在 JavaScript 函数中，函数退出时不会自动进行更新。 请改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 进行更新。 请参阅 [JavaScript 示例](#input---javascript-example)。

## <a name="output"></a>输出

Azure Cosmos DB 输出绑定允许将新文档写入 Azure Cosmos DB 数据库。 

>[!NOTE]
> 如果要对 Cosmos DB 帐户使用 MongoDB API，请不要使用 Azure Cosmos DB 输入或输出绑定。 可能会数据损坏。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示一个使用队列存储消息中提供的数据，将文档添加到数据库的 [C# 函数](functions-dotnet-class-library.md)。

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

该函数按下列格式为每个记录创建 Azure Cosmos DB 文档：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[配置](#output---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

若要创建多个文档，可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输出绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数使用一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

该函数按下列格式为每个记录创建 Azure Cosmos DB 文档：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
[配置](#output---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

此示例要求具有指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

若要添加 `project.json` 文件，请参阅 [F# 包管理](functions-reference-fsharp.md#package)。

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

该函数按下列格式为每个记录创建 Azure Cosmos DB 文档：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

## <a name="output---attributes"></a>输出 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[输出 - 配置](#output---configuration)。 下面是某个方法签名中的 `DocumentDB` 特性示例：

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `DocumentDB` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型**     || 必须设置为 `documentdb`。        |
|**direction**     || 必须设置为 `out`。         |
|**name**     || 表示函数中的文档的绑定参数的名称。  |
|**databaseName** | **DatabaseName**|包含在其中创建文档的集合的数据库。     |
|**collectionName** |**CollectionName**  | 包含在其中创建文档的集合的名称。 |
|**createIfNotExists**  |**CreateIfNotExists**    | 一个用于指示是否创建集合（如果不存在）的布尔值。 默认值为 *false*，因为新集合是使用保留的吞吐量创建的，具有成本方面的隐含意义。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/documentdb/)。  |
|**partitionKey**|**PartitionKey** |当 `CreateIfNotExists` 为 true 时，将定义所创建集合的分区键路径。|
|**collectionThroughput**|**CollectionThroughput**| 当 `CreateIfNotExists` 为 true 时，将定义所创建集合的[吞吐量](../cosmos-db/set-throughput.md)。|
|**连接**    |**ConnectionStringSetting** |内含 Azure Cosmos DB 连接字符串的应用设置的名称。        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

默认情况下，当写入函数中的输出参数时，将在数据库中创建一个文档。 本文档将自动生成的 GUID 作为文档 ID。 可以通过在传递给输出参数的 JSON 对象中指定 `id` 属性来指定输出文档的文档 ID。 

> [!Note]  
> 如果指定现有文档的 ID，它会被新的输出文档覆盖。 

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 引用 |
|---|---|
| CosmosDB | [CosmosDB 错误代码](https://docs.microsoft.com/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用 Cosmos DB 触发器的快速入门](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [详细了解如何使用 Cosmos DB 执行无服务器数据库计算](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
