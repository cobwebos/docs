---
title: Azure Cosmos DB：批量执行程序 .NET API、SDK 和资源
description: 了解有关批量执行程序 .NET API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 .NET SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/27/2020
ms.author: anfeldma
ms.openlocfilehash: 5fcde46738417a03cac8e5e99bea840ce2fd8bc6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392091"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 批量执行程序库：下载信息 

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **说明**| .NET 批量执行程序库允许客户端应用程序对 Azure Cosmos DB 帐户执行批量操作。 此库提供了 BulkImport、BulkUpdate 和 BulkDelete 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos 容器中的现有数据。 BulkDelete 模块可以批量以优化方式删除文档，以便最大程度地使用为集合配置的吞吐量。|
|**SDK 下载**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的批量执行程序库**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文档**|[ 参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**入门**|[批量执行程序库 .NET SDK 入门](bulk-executor-dot-net.md)|
| **当前受支持的框架**| Microsoft .NET Framework 4.5.2、4.6.1 和 .NET Standard 2.0 |

> [!NOTE]
> 如果使用批量执行程序，请参阅 [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md) 的最新版本 3.x，其中内置了批量执行程序。 

## <a name="release-notes"></a>发行说明

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* 修复了 BulkDelete 的响应中的 TotalElapsedTime，以正确测量包括所有重试在内的总时间。

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* 已将 SDK 依赖项更改为 >= 2.5.1。

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* 添加了对图形批量执行程序的支持，以在顶点和边缘上接受 ttl

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* 修复了一个问题，在网关模式下运行时，该问题会在弹性缩放 Azure Cosmos DB 期间导致异常。 此修补程序使其功能等效于 1.4.1 版本。

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* 添加了对 SQL API 帐户的 BulkDelete 支持，以接受要删除的分区键、文档 ID 元组。 此更改使其功能等效于 1.4.0 版本。

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* 包括了 MongoBulkExecutor 来支持 .NET Standard 2.0。 此功能使其功能等效于 1.3.0 版本，增加了支持使用 .NET Standard 2.0 作为目标框架。

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* 添加了 .NET Standard 2.0 作为受支持的目标框架之一，以使批量执行程序库与 .NET Core 应用程序一起工作。

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* 修复了在将包含转义引号的值作为输入参数传递时出现的 BulkDeleteAsync 问题。

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* 修复了一个问题，即 MongoBulkExecutor 通过添加填充意外增大了文档大小并在某些情况下超出最大文档大小限制。

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* 修复了在集合具有嵌套分区键路径时出现的 BulkDeleteAsync 问题。

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor 现在实现了 IDisposable，且在使用后会被释放。

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* 已在 SDK 版本上删除锁定。 包现在依赖于 SDK >= 2.5.1。

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* 修复了使用具有数值的 POCO 对象列表调用 BulkImport 时标识符的处理。

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* 修复了 BulkDelete 的响应中的 TotalElapsedTime，以正确测量包括所有重试在内的总时间。

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* 修复了在某些情况下 CPU 使用率过高的情况。
* 跟踪功能现在使用了 TraceSource。 用户可以为 `BulkExecutorTrace` 源定义侦听器。
* 修复了在发送大小接近 2Mb 的文档时可能导致锁定的罕见情况。

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* 已将批量执行程序更新为现在使用最新版本的 Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* 添加了对图形批量执行程序的支持，以在顶点和边缘上接受 ttl

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* 修复了一个问题，在网关模式下运行时，该问题会在弹性缩放 Azure Cosmos DB 期间导致异常。

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* 添加了对 SQL API 帐户的 BulkDelete 支持，以接受要删除的分区键、文档 ID 元组。

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* 修复了一个问题，该问题导致批量执行程序使用的用户代理出现格式设置问题。

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* 改进了批量执行程序导入和更新 API，使其在存储超出了当前容量且未引发异常时以透明方式适应 Cosmos 容器的弹性缩放。

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* 增加了对版本 2.1.3 的 DocumentDB .NET SDK 依赖项。

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 修复了一个问题，该问题导致批量执行程序在导入到固定集合时引发 JSRT 错误。

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkDelete 操作的支持。
* 使用 Azure Cosmos DB 的 API for MongoDB 添加了对帐户进行 BulkImport 操作的支持。
* 增加了对版本 2.0.0 的 DocumentDB .NET SDK 依赖项。 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* 添加了对 Azure Cosmos DB Gremlin API 帐户的 BulkImport 操作的支持。

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* 对 Azure Cosmos DB SQL API 帐户的 BulkImport 操作的小 bug 修复。

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkImport 和 BulkUpdate 操作的支持。

## <a name="next-steps"></a>后续步骤

若要了解批量执行程序 Java 库，请参阅以下文章：

[Java 批量执行程序库 SDK 和发行信息](sql-api-sdk-bulk-executor-java.md)
