---
title: Azure Cosmos DB 中的分页
description: 了解分页概念和继续标记
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 1cd0c3f48d4dc79294b3ebf9907ac18d23794830
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804191"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分页

在 Azure Cosmos DB 中，查询可能有多页结果。 本文档介绍 Azure Cosmos DB 查询引擎用于决定是否将查询结果拆分为多个页面的条件。 可以选择使用继续标记来管理跨越多个页面的查询结果。

## <a name="understanding-query-executions"></a>了解查询执行

有时，查询结果会拆分为多个页面。 每个页面的结果由单独的查询执行生成。 当一次执行无法返回查询结果时，Azure Cosmos DB 会自动将结果拆分为多个页面。

可以通过设置 `MaxItemCount` 来指定查询返回的最大项数。 每个请求都指定了 `MaxItemCount`，并保证查询引擎返回的项数不会多于该数字。 如果不想限制每次查询执行的结果数，可以将 `MaxItemCount` 设置为 `-1`。

此外，还有查询引擎可能需要将查询结果拆分为多个页面的其他原因。 其中包括：

- 容器受到限制并且没有可用的 RU 来返回更多查询结果
- 查询执行的响应太大
- 查询执行时间过长
- 查询引擎在其他执行中返回结果的效率更高

每次查询执行返回的项数将始终小于或等于 `MaxItemCount`。 但是，其他条件可能会限制查询可以返回的结果数。 如果多次执行同一查询，则页数可能不是固定的。 例如，如果查询受到限制，则每页的可用结果可能会较少，这意味着查询将具有额外的页面。 在某些情况下，查询也有可能返回空白结果页。

## <a name="handling-multiple-pages-of-results"></a>处理多页结果

若要确保查询结果准确，应经历所有页面。 应继续执行查询，直至没有其他页面为止。

以下是一些多页查询处理结果的示例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>继续标记

在 .NET SDK 和 Java SDK 中，可以选择将继续标记用作查询的进度书签。 Azure Cosmos DB 查询执行在服务器端无状态，并可以使用继续标记随时恢复。 Node.js SDK 或 Python SDK 中不支持继续标记。

以下是一些使用继续标记的示例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

如果查询返回继续标记，则会有其他查询结果。

在 Azure Cosmos DB 的 REST API 中，可以使用 `x-ms-continuation` 标头管理继续标记。 与使用 .NET 或 Java SDK 进行查询一样，如果 `x-ms-continuation` 响应标头不为空，则表示查询还有其他结果。

只要使用相同的 SDK 版本，继续标记就永远不会过期。 可以选择[限制继续标记的大小](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)。 不管容器中的数据量或物理分区数量如何，查询都会返回一个继续标记。

不能对具有 [GROUP BY](sql-query-group-by.md) 或 [DISTINCT](sql-query-keywords.md#distinct) 的查询使用继续标记，因为这些查询需要存储大量状态。 对于具有 `DISTINCT` 的查询，如果将 `ORDER BY` 添加到查询中，则可以使用继续标记。

以下是具有 `DISTINCT` 的查询（可以使用继续标记）的示例：

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 子句](sql-query-order-by.md)