---
title: 管理 Azure Functions 中的连接
description: 了解如何通过使用静态连接客户端来避免 Azure Functions 中的性能问题。
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370456"
---
# <a name="manage-connections-in-azure-functions"></a>管理 Azure Functions 中的连接

函数应用中的函数共享资源。 这些共享资源包括连接：HTTP 连接、数据库连接和 Azure 存储等服务的连接。 当许多函数同时运行时，可能会耗尽可用的连接。 本文介绍如何编写函数代码，避免使用超过所需的连接数。

## <a name="connection-limit"></a>连接限制

可用连接的数量在一定程度上受到限制，因为函数应用在[沙盒环境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中运行。 沙盒对你的代码施加的限制之一是对出站连接数的限制，即每个实例当前600活动（1200 total）的连接。 达到此限制时，函数运行时会将以下消息写入日志： `Host thresholds exceeded: Connections`。 有关详细信息，请参阅[函数服务限制](functions-scale.md#service-limits)。

此限制是每个实例的。 当[缩放控制器添加函数应用实例](functions-scale.md#how-the-consumption-and-premium-plans-work)来处理更多请求时，每个实例都有一个独立的连接限制。 这意味着没有全局连接限制，在所有活动实例中，你可以拥有超过600的活动连接。

进行故障排除时，请确保已启用函数应用 Application Insights。 Application Insights 使你可以查看函数应用的指标，如执行。 有关详细信息，请参阅[在 Application Insights 中查看遥测](functions-monitoring.md#view-telemetry-in-application-insights)。  

## <a name="static-clients"></a>静态客户端

为了避免占用的连接数超过实际所需，可重复使用客户端实例，而不是在每次调用函数时创建新的实例。 建议为你可以在其中编写函数的任何语言重复使用客户端连接。 例如，如果使用单个静态客户端，.NET 客户端（如[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)、[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)和 Azure 存储客户端）可以管理连接。

下面是在 Azure Functions 应用程序中使用特定于服务的客户端时要遵循的一些准则：

- *不要*使用每个函数调用创建新的客户端。
- *创建每*个函数调用都可以使用的单个静态客户端。
- 如果不同的函数使用同一个服务，*请考虑*在共享的帮助程序类中创建单个静态客户端。

## <a name="client-code-examples"></a>客户端代码示例

本部分演示了用于通过函数代码创建和使用客户端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 示例 (C#)

下面是创建静态C# [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)实例的函数代码示例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

有关 .NET 中的[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)的一个常见问题是 "应该处理我的客户端吗？" 通常，在使用完实现 `IDisposable` 的对象。 但不会释放静态客户端，因为在函数结束时未使用该客户端。 你需要将静态客户端一直保留到应用程序生存期结束。

### <a name="http-agent-examples-javascript"></a>HTTP 代理示例（JavaScript）

因为它提供了更好的连接管理选项，所以你应当使用本机 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 类而不应当使用非本机方法，例如 `node-fetch` 模块。 通过 `http.agent` 类上的选项配置连接参数。 有关 HTTP 代理提供的详细选项，请参阅[新代理（\[选项\]）](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

`http.request()` 使用的全局 `http.globalAgent` 类将所有这些值设置为各自的默认值。 在 Functions 中配置连接限制的建议方法是全局设置一个最大数量。 以下示例为函数应用设置最大 socket 数量：

```js
http.globalAgent.maxSockets = 200;
```

 下面的示例将创建一个新的 HTTP 请求，其中仅对该请求使用自定义 HTTP 代理：

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

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient 代码示例（JavaScript）
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient)连接到 Azure Cosmos DB 实例。 Azure Cosmos DB 文档建议[在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端](../cosmos-db/performance-tips.md#sdk-usage)。 以下示例展示了在函数中执行该操作的一种模式：

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

函数代码可以使用 SQL Server 的 .NET Framework 数据提供程序（[SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)）来连接到 SQL 关系数据库。 这也是依赖于 ADO.NET 的数据框架的基础提供程序，如[实体框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 与 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 连接不同，ADO.NET 默认实现连接池。 但由于仍可以用完了连接，因此应该优化与数据库的连接。 有关详细信息，请参阅 [SQL Server 连接池 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。

> [!TIP]
> 某些数据框架（如实体框架）通常从配置文件的**ConnectionStrings**节获取连接字符串。 在这种情况下，必须将 SQL 数据库连接字符串显式添加到函数应用设置的连接字符串集合以及本地项目中的 [local.settings.json 文件](functions-run-local.md#local-settings-file)中。 如果要在函数代码中创建[SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)的实例，则应将 "**应用程序设置**" 中的连接字符串值存储在其他连接中。

## <a name="next-steps"></a>后续步骤

有关为何建议静态客户端的详细信息，请参阅不[正确的实例化对立模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

有关更多 Azure Functions 性能提示，请参阅[优化 Azure Functions 的性能和可靠性](functions-best-practices.md)。
