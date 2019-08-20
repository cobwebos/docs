---
title: Azure Cosmos DB：批量执行器 .NET API, SDK & 资源
description: 了解有关批量执行程序 .NET API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 .NET SDK 各版本之间所做的更改。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 03e620f97ef094994f95b5dc39b95376fa7e09dd
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614472"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 批量执行器库:下载信息 

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
> * [大容量执行程序-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **说明**| .Net 批量执行器库允许客户端应用程序对 Azure Cosmos DB 帐户执行批量操作。 此库提供了 BulkImport、BulkUpdate 和 BulkDelete 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以将 Azure Cosmos 容器中的现有数据大容量更新为修补程序。 BulkDelete 模块可以批量以优化方式删除文档，以便最大程度地使用为集合配置的吞吐量。|
|**SDK 下载**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的 BulkExecutor 库**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文档**|[ 参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**入门**|[批量执行程序库 .NET SDK 入门](bulk-executor-dot-net.md)|
| **当前受支持的框架**| Microsoft .NET Framework 4.5.2、4.6.1 和 .NET Standard 2.0 |

## <a name="release-notes"></a>发行说明

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* 添加了对 graph 大容量执行程序的支持, 以接受顶点和边缘上的 ttl

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* 修复了一个问题: 在网关模式下运行时, 在弹性缩放 Azure Cosmos DB 期间导致异常。 此修补程序使其在功能上等效于1.4.1 版本。

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* 添加了对 SQL API 帐户的 BulkDelete 支持, 以接受分区键、要删除的文档 id 元组。 此更改使其在功能上等效于1.4.0 版本。

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括了 MongoBulkExecutor 来支持 .NET Standard 2.0。 此功能使其功能等效于 1.3.0 版本，增加了支持使用 .NET Standard 2.0 作为目标框架。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 添加了 .NET Standard 2.0 作为受支持的目标框架之一，以使 BulkExecutor 库与 .NET Core 应用程序一起工作。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* 已将批量执行程序更新为现在使用最新版本的 Azure Cosmos DB .NET SDK (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* 添加了对 graph 大容量执行程序的支持, 以接受顶点和边缘上的 ttl

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* 修复了一个问题: 在网关模式下运行时, 在弹性缩放 Azure Cosmos DB 期间导致异常。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* 添加了对 SQL API 帐户的 BulkDelete 支持, 以接受分区键、要删除的文档 id 元组。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* 修复了一个问题，该问题导致 BulkExecutor 使用的用户代理出现格式设置问题。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 对 BulkExecutor 导入和更新 Api 做出了改进, 以便在存储超过当前容量而不引发异常时, 透明地适应 Cosmos 容器的弹性缩放。

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

若要了解批量执行器 Java 库, 请参阅以下文章:

[Java 大容量执行器库 SDK 和发布信息](sql-api-sdk-bulk-executor-java.md)
