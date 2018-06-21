---
title: 如何管理 Azure Functions 中的连接
description: 了解如何通过使用静态连接客户端来避免 Azure Functions 中的性能问题。
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658920"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>如何管理 Azure Functions 中的连接

函数应用中的函数共享资源，这些共享资源包含各种连接 &mdash; HTTP 连接、数据库连接以及到 Azure 服务（如存储）的连接。 当许多函数同时运行时，可能会耗尽可用的连接。 本文介绍如何编写函数，以避免其使用的连接数超过实际所需。

## <a name="connections-limit"></a>连接限制

可用连接数量受到限制，部分原因是函数应用在 [Azure 应用服务沙盒](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中运行。 沙盒对代码施加的限制之一便是[连接数量上限，目前为 300 个](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits)。 达到此限制时，函数运行时会创建一个包含以下消息的日志：`Host thresholds exceeded: Connections`。

当[缩放控制器添加函数应用实例](functions-scale.md#how-the-consumption-plan-works)时，超出该限制的可能性会增加。 每个函数应用实例可以同时调用函数多次，并且所有这些函数使用的连接都会计入 300 个的限制中。

## <a name="use-static-clients"></a>使用静态客户端

为了避免占用的连接数超过实际所需，可重复使用客户端实例，而不是在每次调用函数时创建新的实例。 如果使用单个静态客户端，`HttpClient`、`DocumentClient` 和 Azure 存储客户端等 .NET 客户端可以管理连接。

在 Azure Functions 应用程序中使用特定于服务的客户端时，请遵循以下准则：

- **不要**在每次调用函数时创建新的客户端。
- **应**创建一个可在每次调用函数时使用的静态客户端。
- 如果不同的函数使用相同的服务，请**考虑**在共享帮助程序类中创建单个静态客户端。

## <a name="httpclient-code-example"></a>HttpClient 代码示例

以下是创建静态 `HttpClient` 的函数代码示例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

关于 .NET `HttpClient` 的一个常见问题是“我应该释放我的客户端吗？” 一般情况下，在使用对象实现了 `IDisposable` 之后，你会释放这些对象。 但是，你不会释放静态客户端，因为在函数结束后还需要使用它。 你需要将静态客户端一直保留到应用程序生存期结束。

## <a name="documentclient-code-example"></a>DocumentClient 代码示例

`DocumentClient` 连接到 Cosmos DB 实例。 Cosmos DB 文档建议[在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage)。 以下示例展示了在函数中执行该操作的一种模式。

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

## <a name="next-steps"></a>后续步骤

有关为何建议使用静态客户端的详细信息，请参阅[不当实例化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

有关更多 Azure Functions 性能提示，请参阅[优化 Azure Functions 的性能和可靠性](functions-best-practices.md)。