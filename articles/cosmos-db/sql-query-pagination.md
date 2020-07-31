---
title: Azure Cosmos DB 中的分页
description: 了解分页概念和继续标记
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 978cc67336fe7f6f89970007215da73da0737f08
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433554"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分页

在 Azure Cosmos DB 中，查询可能有多个页面结果。 本文档介绍 Azure Cosmos DB 的查询引擎用来决定是否将查询结果拆分为多个页面的条件。 您可以选择使用继续标记来管理跨多个页的查询结果。

## <a name="understanding-query-executions"></a>了解查询执行

有时，查询结果将被拆分到多个页上。 每个页面的结果都是由单独的查询执行生成的。 当一次执行中无法返回查询结果时，Azure Cosmos DB 会自动将结果拆分为多个页面。

通过设置，可以指定查询返回的最大项数 `MaxItemCount` 。 `MaxItemCount`是为每个请求指定的，并且保证查询引擎将返回该数量或更少的项。 `MaxItemCount` `-1` 如果不想对每个查询执行的结果数施加限制，则可以将设置为。

此外，查询引擎可能需要将查询结果拆分为多个页面。 其中包括:

- 已限制容器，但没有可用的 ru 返回更多查询结果
- 查询执行的响应太大
- 查询执行时间太长
- 查询引擎返回其他执行结果的效率更高

每个查询执行返回的项数将始终小于或等于 `MaxItemCount` 。 但是，其他条件可能会限制查询可能返回的结果数。 如果多次执行相同的查询，则页数可能不是固定的。 例如，如果某个查询受到限制，则每页可能会有更少的可用结果，这意味着查询将有其他页面。 在某些情况下，您的查询也可能返回一个空的结果页。

## <a name="handling-multiple-pages-of-results"></a>处理多页结果

若要确保查询结果准确，您应该经历所有页面。 在没有其他页面之前，应继续执行查询。

下面是用于处理包含多个页面的查询结果的一些示例：

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)

[Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)

[Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>继续标记

在 .NET SDK 和 Java SDK 中，可以选择使用继续标记作为查询进度的书签。 Azure Cosmos DB 查询执行在服务器端是无状态的，并且可以使用继续标记随时恢复。 Node.js SDK 或 Python SDK 中不支持继续标记。

下面是使用继续标记的一些示例：

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

如果查询返回继续标记，则存在其他查询结果。

在 Azure Cosmos DB 的 REST API 中，可以用标头来管理继续标记 `x-ms-continuation` 。 与通过 .NET 或 Java SDK 进行查询一样，如果 `x-ms-continuation` 响应标头不为空，则表示查询具有更多结果。

只要你使用相同的 SDK 版本，继续标记就永不过期。 您可以选择[限制继续标记的大小](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)。 不管容器中的数据量或物理分区数量如何，查询都将返回单个继续标记。

不能对带[GROUP BY](sql-query-group-by.md)或[DISTINCT](sql-query-keywords.md#distinct)的查询使用继续标记，因为这些查询需要存储大量状态。 对于使用的查询 `DISTINCT` ，如果将添加到查询中，则可以使用继续标记 `ORDER BY` 。

下面是一个使用继续标记的查询示例 `DISTINCT` ：

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 子句](sql-query-order-by.md)