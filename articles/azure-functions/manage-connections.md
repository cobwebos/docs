---
title: Azure Functions 中管理连接
description: 了解如何通过使用静态连接客户端来避免 Azure Functions 中的性能问题。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 965fa1e82be3fb87bf58a0114f97091bad212738
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450730"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Functions 中管理连接

函数在函数应用中的共享资源。 在这些共享资源之间是连接:HTTP 连接、 数据库连接和连接到 Azure 存储等服务。 当许多函数同时运行时，可能会耗尽可用的连接。 此文章介绍了如何编写函数以避免使用连接的连接数超过所需的代码。

## <a name="connection-limit"></a>连接限制

部分原因是因为中运行函数应用的可用连接数是有限[沙盒环境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)。 沙箱对代码施加的限制之一是[（当前为 600 的活动连接和连接总数不得超过 1200） 的连接数上限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits)每个实例。 达到此限制时，函数运行时会创建一个包含以下消息的日志：`Host thresholds exceeded: Connections`。

此限制为每个实例。  当[缩放控制器添加函数应用实例](functions-scale.md#how-the-consumption-plan-works)以处理更多的请求，每个实例都有独立的连接限制。 这意味着没有全局连接限制，并且可以跨所有活动实例具有 600 更多个活动连接。

## <a name="static-clients"></a>静态客户端

为了避免占用的连接数超过实际所需，可重复使用客户端实例，而不是在每次调用函数时创建新的实例。 我们建议重复使用任何语言，你可以编写您的函数中的客户端连接。 例如，.NET 客户端，如[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)， [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)，和 Azure 存储客户端可以管理的连接，如果你使用单个的静态客户端。

以下是在 Azure Functions 应用程序中使用特定于服务的客户端时应遵循一些准则：

- *不这样做*与每个函数调用创建新的客户端。
- *执行*创建单个的静态客户端，可以使用每个函数调用。
- *请考虑*共享帮助程序类中创建单个的静态客户端，如果不同的函数使用相同的服务。

## <a name="client-code-examples"></a>客户端代码示例

本部分演示了用于通过函数代码创建和使用客户端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 示例 (C#)

下面是示例的C#函数创建一个静态的代码[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)实例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

有关常见问题[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)在.NET 中是"应该我释放我的客户端？" 一般情况下，在释放的对象的实现`IDisposable`完成后使用它们。 您不释放静态客户端因为未完成，但在函数结束时使用它。 你需要将静态客户端一直保留到应用程序生存期结束。

### <a name="http-agent-examples-nodejs"></a>HTTP 代理示例 (Node.js)

因为它提供了更好的连接管理选项，所以你应当使用本机 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 类而不应当使用非本机方法，例如 `node-fetch` 模块。 在配置连接参数通过选项`http.agent`类。 有关详细的选项可使用 HTTP 代理，请参阅[新的代理 (\[选项\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

全局`http.globalAgent`类使用的`http.request()`具有所有这些值设置为其各自的默认值。 在 Functions 中配置连接限制的建议方法是全局设置一个最大数量。 以下示例为函数应用设置最大 socket 数量：

```js
http.globalAgent.maxSockets = 200;
```

 下面的示例使用仅用于该请求的自定义 HTTP 代理创建新的 HTTP 请求：

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

## <a name="sqlclient-connections"></a>SqlClient 连接

在函数代码可用于 SQL Server 的.NET Framework 数据提供程序 ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) 连接到 SQL 关系数据库。 这也是依赖 ADO.NET 中，如数据框架的基础提供程序[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 与 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 连接不同，ADO.NET 默认实现连接池。 但是，因为你仍可运行完连接，应优化的数据库连接。 有关详细信息，请参阅 [SQL Server 连接池 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。

> [!TIP]
> 某些数据框架，如实体框架中，通常会返回从连接字符串**ConnectionStrings**配置文件的部分。 在这种情况下，必须将 SQL 数据库连接字符串显式添加到函数应用设置的连接字符串集合以及本地项目中的 [local.settings.json 文件](functions-run-local.md#local-settings-file)中。 如果要创建的实例[SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)应在函数代码中，将存储连接字符串值中的**应用程序设置**与其他连接。

## <a name="next-steps"></a>后续步骤

有关为何我们建议使用静态客户端的详细信息，请参阅[不当实例化对立模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

有关更多 Azure Functions 性能提示，请参阅[优化 Azure Functions 的性能和可靠性](functions-best-practices.md)。
