---
title: Azure Cosmos DB：批量执行程序 .NET API、SDK 和资源
description: 了解有关批量执行程序 .NET API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 .NET SDK 各版本之间所做的更改。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 4f2d8b3246901f139695998224dfe036cccb9833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855778"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 批量执行程序库：下载信息 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [异步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行程序 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **说明**| 批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供了 BulkImport、BulkUpdate 和 BulkDelete 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。 BulkDelete 模块可以批量以优化方式删除文档，以便最大程度地使用为集合配置的吞吐量。|
|**SDK 下载**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的 BulkExecutor 库**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文档**|[ 参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**入门**|[批量执行程序库 .NET SDK 入门](bulk-executor-dot-net.md)|
| **当前受支持的框架**| Microsoft .NET Framework 4.5.2、4.6.1 和 .NET Standard 2.0 |

## <a name="release-notes"></a>发行说明

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括了 MongoBulkExecutor 来支持 .NET Standard 2.0。 此功能使其功能等效于 1.3.0 版本，增加了支持使用 .NET Standard 2.0 作为目标框架。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 添加了 .NET Standard 2.0 作为受支持的目标框架之一，以使 BulkExecutor 库与 .NET Core 应用程序一起工作。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* 增加了对 SQL API 帐户的 BulkDelete 操作的重载，以接受要删除的分区键、文档 ID 元组。
* 增加了对 SQL API 帐户的 BulkDelete 操作的重载以接受包含分区键值的 RequestOptions，并且还在输入查询中使用它作为筛选器来指定要删除的文档。
* 修复了一个问题，该问题导致 BulkExecutor 使用的用户代理出现格式设置问题。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 改进了 BulkExecutor 导入和更新 API，使其在存储超出了当前容量且未引发异常时以透明方式适应 Cosmos DB 容器的弹性缩放。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 增加了对版本 2.1.3 的 DocumentDB .NET SDK 依赖项。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 修复了一个问题，该问题导致 BulkExecutor 在导入到固定集合时引发 JSRT 错误。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkDelete 操作的支持。
* 使用 Azure Cosmos DB 的 API for MongoDB 添加了对帐户进行 BulkImport 操作的支持。
* 增加了对版本 2.0.0 的 DocumentDB .NET SDK 依赖项。 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* 添加了对 Azure Cosmos DB Gremlin API 帐户的 BulkImport 操作的支持。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* 对 Azure Cosmos DB SQL API 帐户的 BulkImport 操作的小 bug 修复。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkImport 和 BulkUpdate 操作的支持。

## <a name="next-steps"></a>后续步骤

若要了解批量执行程序 Java 库，请参阅以下文章：

[Java 批量执行程序库 SDK 和发行信息](sql-api-sdk-bulk-executor-java.md)
