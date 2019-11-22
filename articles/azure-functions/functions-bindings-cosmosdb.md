---
title: 适用于 Functions 1.x 的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 18bbfd1b54947bb88ba8f06c65a17b90430b38a3
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305217"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>适用于 Azure Functions 1.x 的 Azure Cosmos DB 绑定

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [第 1 版](functions-bindings-cosmosdb.md)
> * [第 2 版](functions-bindings-cosmosdb-v2.md)

本文介绍如何在 Azure Functions 中使用 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

> [!NOTE]
> 本文适用于 Azure Functions 1.x。 若要了解如何在 Functions 2.x 中使用这些绑定，请参阅[适用于 Azure Functions 2.x 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb-v2.md)。
>
>此绑定最初名为 DocumentDB。 在 Functions 版本 1.x 中，仅触发器已重命名为 Cosmos DB；输入绑定、输出绑定和 NuGet 包保留 DocumentDB 名称。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB 绑定只能与 SQL API 配合使用。 对于所有其他的 Azure Cosmos DB API，应使用适用于 API 的静态客户端通过函数来访问数据库。API 包括 [Azure Cosmos DB 的 API for MongoDB](../cosmos-db/mongodb-introduction.md)、[Cassandra API](../cosmos-db/cassandra-introduction.md)、[Gremlin API](../cosmos-db/graph-introduction.md) 和[表 API](../cosmos-db/table-introduction.md)。

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet 包 1.x 版中提供了适用于 Functions 1.x 版的 Azure Cosmos DB 绑定。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub 存储库中提供了此绑定的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>触发器

Azure Cosmos DB 触发器使用 [Azure Cosmos DB 更改源](../cosmos-db/change-feed.md)来侦听跨分区的插入和更新。 更改源发布插入和更新，不发布删除。

## <a name="trigger---example"></a>触发器 - 示例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)。当指定数据库和集合中存在插入或更新操作时，会调用该函数。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

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
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

## <a name="trigger---attributes"></a>触发器 - 特性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

有关完整示例，请参阅[触发器 - C# 示例](#trigger)。

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

---

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
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| （可选）设置后，此项向在此 Function 的“租用”集合中创建的租用添加一个前缀，实际上就是允许两个不同的 Azure Functions（使用不同的前缀）共享同一“租用”集合。
|**feedPollDelay**| **FeedPollDelay**| （可选）设置后，此项以毫秒为单位定义在所有当前更改均耗尽后，源上新更改的分区轮询间的延迟。 默认为 5000（5 秒）。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| （可选）设置后，此项以毫秒为单位定义启动一个计算任务的时间间隔（前提是分区在已知的主机实例中均匀分布）。 默认为 13000（13 秒）。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| （可选）设置后，此项以毫秒为单位定义在表示分区的租用上进行租用的时间间隔。 如果在此时间间隔内不续订租用，则该租用会过期，分区的所有权会转移到另一个实例。 默认为 60000（60 秒）。
|**leaseRenewInterval**| **LeaseRenewInterval**| （可选）设置后，此项以毫秒为单位定义当前由实例拥有的分区的所有租用的续订时间间隔。 默认为 17000（17 秒）。
|**checkpointFrequency**| **CheckpointFrequency**| （可选）设置后，此项以毫秒为单位定义租用检查点的时间间隔。 默认为始终在进行每个 Function 调用之后进行检查。
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| （可选）设置后，此项对每次 Function 调用收到的项目的最大数目进行自定义。
|**startFromBeginning**| **StartFromBeginning**| （可选）设置时，它会告诉触发器从集合历史记录的开头而不是当前时间开始读取更改。 这仅在触发器第一次启动时起作用，因为在后续运行中，已存储检查点。 如果已经创建租约，则将此值设置为 `true` 无效。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>触发器 - 用法

触发器需要第二个集合，该集合用于存储各分区的_租用_。 必须提供受监视的集合和包含租用的集合，触发器才能正常工作。

>[!IMPORTANT]
> 如果多个函数都配置为对同一集合使用 Cosmos DB 触发器，则每个函数都应使用专用租用集合，否则应该为每个函数指定不同的 `LeaseCollectionPrefix`。 否则，将只触发其中一个函数。 有关前缀的信息，请参阅[“配置”部分](#trigger---configuration)。

该触发器并不指示是更新还是插入文档，它仅提供文档本身。 如果需要以不同方式处理更新和插入，可通过实现用于插入或更新的时间戳字段来执行该操作。

## <a name="input"></a>输入

Azure Cosmos DB 输入绑定会使用 SQL API 检索一个或多个 Azure Cosmos DB 文档，并将其传递给函数的输入参数。 可根据调用函数的触发器确定文档 ID 或查询参数。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

本部分包含以下示例：

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-c)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-c)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-c)
* [HTTP 触发器，使用 SqlQuery 从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP 触发器，使用 SqlQuery 获取多个文档](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP 触发器，使用 DocumentClient 获取多个文档](#http-trigger-get-multiple-docs-using-documentclient-c)

这些示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 该函数由包含 JSON 对象的队列消息触发。 队列触发器将 JSON 解析成名为 `ToDoItemLookup` 的对象，其中包含要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，查询字符串中的查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，查找路由数据中的 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[跳过输入示例](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 查找路由数据中的 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[跳过输入示例](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 触发器，获取多个文档，使用 SqlQuery

以下示例演示检索文档列表的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发。 此查询在 `SqlQuery` 特性属性中指定。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[跳过输入示例](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP 触发器，使用 DocumentClient 获取多个文档 (C#)

以下示例演示检索文档列表的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发。 此代码使用 Azure Cosmos DB 绑定提供的 `DocumentClient` 实例来读取文档列表。 `DocumentClient` 实例也可用于写入操作。

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

本部分包含以下示例：

* [队列触发器，从字符串查找 ID](#queue-trigger-look-up-id-from-string-c-script)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP 触发器，使用 SqlQuery 获取多个文档](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 触发器，使用 DocumentClient 获取多个文档](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP 触发器示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>队列触发器，从字符串查找 ID

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

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，获取多个文档，使用 SqlQuery

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

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，查询字符串中的查找 ID

以下示例演示检索单个文档的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

C# 脚本代码如下所示：

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，查找路由数据中的 ID

以下示例演示检索单个文档的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 触发器，获取多个文档，使用 SqlQuery

以下示例演示检索文档列表的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发。 此查询在 `SqlQuery` 特性属性中指定。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP 触发器，获取多个文档，使用 DocumentClient

以下示例演示检索文档列表的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发。 此代码使用 Azure Cosmos DB 绑定提供的 `DocumentClient` 实例来读取文档列表。 `DocumentClient` 实例也可用于写入操作。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

本部分包含以下示例：

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-javascript)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

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

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，查询字符串中的查找 ID

以下示例演示检索单个文档的 [JavaScript 函数](functions-reference-node.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，查找路由数据中的 ID

以下示例演示检索单个文档的 [JavaScript 函数](functions-reference-node.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，获取多个文档，使用 SqlQuery

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

---

## <a name="input---attributes"></a>输入 - 特性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[下面的“配置”部分](#input---configuration)。

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

---

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件和 `DocumentDB` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型**     || 必须设置为 `documentdb`。        |
|**direction**     || 必须设置为 `in`。         |
|**name**     || 表示函数中的文档的绑定参数的名称。  |
|**databaseName** |**DatabaseName** |包含文档的数据库。        |
|**collectionName** |**CollectionName** | 包含文档的集合的名称。 |
|**id**    | **Id** | 要检索的文档的 ID。 此属性支持[绑定表达式](./functions-bindings-expressions-patterns.md)。 不要同时设置 **id** 和 **sqlQuery** 属性。 如果上述两个属性都未设置，则会检索整个集合。 |
|**sqlQuery**  |**SqlQuery**  | 用于检索多个文档的 Azure Cosmos DB SQL 查询。 该属性支持运行时绑定，如以下示例中所示：`SELECT * FROM c where c.departmentId = {departmentId}`。 不要同时设置 **id** 和 **sqlQuery** 属性。 如果上述两个属性都未设置，则会检索整个集合。|
|**连接**     |**ConnectionStringSetting**|内含 Azure Cosmos DB 连接字符串的应用设置的名称。        |
|**partitionKey**|**PartitionKey**|指定用于查找分区键值。 可以包含绑定参数。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>输入 - 用法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

函数成功退出后，通过命名输入参数对输入文档所做的任何更改都将自动保留。

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

函数成功退出后，通过命名输入参数对输入文档所做的任何更改都将自动保留。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

不会在函数退出时自动进行更新。 请改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 进行更新。 请参阅[输入示例](#input)。

---

## <a name="output"></a>输出

Azure Cosmos DB 输出绑定允许使用 SQL API 将新文档写入 Azure Cosmos DB 数据库。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

本部分包含以下示例：

* 队列触发器，写入一个文档
* 队列触发器，使用 `IAsyncCollector` 编写文档

这些示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>队列触发器，写入一个文档

以下示例演示一个使用队列存储消息中提供的数据，将文档添加到数据库的 [C# 函数](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>队列触发器，使用 IAsyncCollector 来写入文档

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数使用以队列消息 JSON 格式提供的数据将文档集添加到数据库。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

本部分包含以下示例：

* 队列触发器，写入一个文档
* 队列触发器，使用 `IAsyncCollector` 编写文档

### <a name="queue-trigger-write-one-doc"></a>队列触发器，写入一个文档

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>队列触发器，使用 IAsyncCollector 来写入文档

若要创建多个文档，可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

此示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

## <a name="output---attributes"></a>输出 - 特性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

有关完整示例，请参阅[输出](#output)。

# <a name="c-scripttabcsharp-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

---

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
| CosmosDB | [CosmosDB 错误代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>后续步骤

* [详细了解如何使用 Cosmos DB 执行无服务器数据库计算](../cosmos-db/serverless-computing-database.md)
* [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
