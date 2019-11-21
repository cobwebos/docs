---
title: Manage connections in Azure Functions
description: 了解如何通过使用静态连接客户端来避免 Azure Functions 中的性能问题。
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226494"
---
# <a name="manage-connections-in-azure-functions"></a>Manage connections in Azure Functions

Functions in a function app share resources. Among those shared resources are connections: HTTP connections, database connections, and connections to services such as Azure Storage. 当许多函数同时运行时，可能会耗尽可用的连接。 This article explains how to code your functions to avoid using more connections than they need.

## <a name="connection-limit"></a>Connection limit

The number of available connections is limited partly because a function app runs in a [sandbox environment](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). One of the restrictions that the sandbox imposes on your code is a limit on the number of outbound connections, which is currently 600 active (1,200 total) connections per instance. When you reach this limit, the functions runtime writes the following message to the logs: `Host thresholds exceeded: Connections`. For more information, see the [Functions service limits](functions-scale.md#service-limits).

This limit is per instance. When the [scale controller adds function app instances](functions-scale.md#how-the-consumption-and-premium-plans-work) to handle more requests, each instance has an independent connection limit. That means there's no global connection limit, and you can have much more than 600 active connections across all active instances.

When troubleshooting, make sure that you have enabled Application Insights for your function app. Application Insights lets you view metrics for your function apps like executions. For more information, see [View telemetry in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Static clients

为了避免占用的连接数超过实际所需，可重复使用客户端实例，而不是在每次调用函数时创建新的实例。 We recommend reusing client connections for any language that you might write your function in. For example, .NET clients like the [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), and Azure Storage clients can manage connections if you use a single, static client.

Here are some guidelines to follow when you're using a service-specific client in an Azure Functions application:

- *Do not* create a new client with every function invocation.
- *Do* create a single, static client that every function invocation can use.
- *Consider* creating a single, static client in a shared helper class if different functions use the same service.

## <a name="client-code-examples"></a>客户端代码示例

本部分演示了用于通过函数代码创建和使用客户端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 示例 (C#)

Here's an example of C# function code that creates a static [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A common question about [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET is "Should I dispose of my client?" In general, you dispose of objects that implement `IDisposable` when you're done using them. But you don't dispose of a static client because you aren't done using it when the function ends. 你需要将静态客户端一直保留到应用程序生存期结束。

### <a name="http-agent-examples-javascript"></a>HTTP agent examples (JavaScript)

因为它提供了更好的连接管理选项，所以你应当使用本机 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 类而不应当使用非本机方法，例如 `node-fetch` 模块。 Connection parameters are configured through options on the `http.agent` class. For detailed options available with the HTTP agent, see [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

The global `http.globalAgent` class used by `http.request()` has all of these values set to their respective defaults. 在 Functions 中配置连接限制的建议方法是全局设置一个最大数量。 以下示例为函数应用设置最大 socket 数量：

```js
http.globalAgent.maxSockets = 200;
```

 The following example creates a new HTTP request with a custom HTTP agent only for that request:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 代码示例 (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 连接到 Azure Cosmos DB 实例。 Azure Cosmos DB 文档建议[在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)。 以下示例展示了在函数中执行该操作的一种模式：

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient code example (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) connects to an Azure Cosmos DB instance. Azure Cosmos DB 文档建议[在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端](../cosmos-db/performance-tips.md#sdk-usage)。 以下示例展示了在函数中执行该操作的一种模式：

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 连接

Your function code can use the .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) to make connections to a SQL relational database. This is also the underlying provider for data frameworks that rely on ADO.NET, such as [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). 与 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 连接不同，ADO.NET 默认实现连接池。 But because you can still run out of connections, you should optimize connections to the database. 有关详细信息，请参阅 [SQL Server 连接池 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。

> [!TIP]
> Some data frameworks, such as Entity Framework, typically get connection strings from the **ConnectionStrings** section of a configuration file. 在这种情况下，必须将 SQL 数据库连接字符串显式添加到函数应用设置的连接字符串集合以及本地项目中的 [local.settings.json 文件](functions-run-local.md#local-settings-file)中。 If you're creating an instance of [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in your function code, you should store the connection string value in **Application settings** with your other connections.

## <a name="next-steps"></a>后续步骤

For more information about why we recommend static clients, see [Improper instantiation antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

有关更多 Azure Functions 性能提示，请参阅[优化 Azure Functions 的性能和可靠性](functions-best-practices.md)。
