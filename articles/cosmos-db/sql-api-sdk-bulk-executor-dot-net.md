---
title: Azure 宇宙 DB：大批量执行器 .NET API、SDK &资源
description: 了解有关批量执行程序 .NET API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 .NET SDK 各版本之间所做的更改。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169398"
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
> * [批量执行器 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行器 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **说明**| .Net 批量执行器库允许客户端应用程序对 Azure Cosmos DB 帐户执行批量操作。 此库提供了 BulkImport、BulkUpdate 和 BulkDelete 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos 容器中的现有数据。 BulkDelete 模块可以批量以优化方式删除文档，以便最大程度地使用为集合配置的吞吐量。|
|**SDK 下载**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub 中的批量执行程序库**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 文档**|[ 参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**入门**|[批量执行程序库 .NET SDK 入门](bulk-executor-dot-net.md)|
| **当前受支持的框架**| Microsoft .NET Framework 4.5.2、4.6.1 和 .NET Standard 2.0 |

> [!NOTE]
> 如果使用批量执行程序，请参阅 [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md) 的最新版本 3.x，其中内置了批量执行程序。 

## <a name="release-notes"></a>发行说明

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1 预览

* 在 BulkDelete 的响应中固定总时间，以正确测量总时间，包括任何重试。

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0 预览版

* 将 SDK 依赖项更改为 >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-预览2

* 添加了对图形批量执行器的支持，以在顶点和边上接受 ttl

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-预览2

* 修复了在网关模式下运行时 Azure Cosmos DB 的弹性缩放期间导致异常的问题。 此修复程序使其在功能上等效于 1.4.1 版本。

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-预览2

* 添加了对 SQL API 帐户接受分区密钥、文档 ID 副本以删除的支持。 此更改使其在功能上等效于 1.4.0 版本。

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括了 MongoBulkExecutor 来支持 .NET Standard 2.0。 此功能使其功能等效于 1.3.0 版本，增加了支持使用 .NET Standard 2.0 作为目标框架。

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 添加了 .NET 标准 2.0 作为支持的目标框架之一，以使批量执行器库与 .NET Core 应用程序一起工作。

### <a name="188"></a><a name="1.8.8"/>1.8.8

* 修复了 MongoBulk Executor 上通过添加填充和在某些情况下超出最大文档大小限制意外增加文档大小的问题。

### <a name="187"></a><a name="1.8.7"/>1.8.7

* 修复了 BulkDeleteAsync 在集合嵌套分区密钥路径时的问题。

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor 现在实现了 IIIIA，并且预计在使用后释放它。

### <a name="185"></a><a name="1.8.5"/>1.8.5

* 在 SDK 版本上删除了锁。 包现在依赖于 SDK >= 2.5.1。

### <a name="184"></a><a name="1.8.4"/>1.8.4

* 使用具有数值的 POCO 对象列表调用 BulkImport 时修复标识符的处理。

### <a name="183"></a><a name="1.8.3"/>1.8.3

* 在 BulkDelete 的响应中固定总时间，以正确测量总时间，包括任何重试。

### <a name="182"></a><a name="1.8.2"/>1.8.2

* 修复了某些方案中的高 CPU 消耗。
* 现在，使用跟踪源进行跟踪。 用户可以为`BulkExecutorTrace`源定义侦听器。
* 修复了在发送大小接近 2Mb 的文档时可能导致锁定的罕见情况。

### <a name="160"></a><a name="1.6.0"/>1.6.0

* 更新了批量执行器，现在使用最新版本的 Azure Cosmos DB .NET SDK （2.4.0）

### <a name="150"></a><a name="1.5.0"/>1.5.0

* 添加了对图形批量执行器的支持，以在顶点和边上接受 ttl

### <a name="141"></a><a name="1.4.1"/>1.4.1

* 修复了在网关模式下运行时 Azure Cosmos DB 的弹性缩放期间导致异常的问题。

### <a name="140"></a><a name="1.4.0"/>1.4.0

* 添加了对 SQL API 帐户接受分区密钥、文档 ID 副本以删除的支持。

### <a name="130"></a><a name="1.3.0"/>1.3.0

* 修复了导致批量执行器使用的用户代理中的格式问题。

### <a name="120"></a><a name="1.2.0"/>1.2.0

* 改进批量执行器导入和更新 API，以透明地适应 Cosmos 容器的弹性缩放，当存储超过当前容量而不引发异常时。

### <a name="112"></a><a name="1.1.2"/>1.1.2

* 增加了对版本 2.1.3 的 DocumentDB .NET SDK 依赖项。

### <a name="111"></a><a name="1.1.1"/>1.1.1

* 修复了一个问题，导致批量执行器在导入到固定集合时引发 JSRT 错误。

### <a name="110"></a><a name="1.1.0"/>1.1.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkDelete 操作的支持。
* 使用 Azure Cosmos DB 的 API for MongoDB 添加了对帐户进行 BulkImport 操作的支持。
* 增加了对版本 2.0.0 的 DocumentDB .NET SDK 依赖项。 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* 添加了对 Azure Cosmos DB Gremlin API 帐户的 BulkImport 操作的支持。

### <a name="101"></a><a name="1.0.1"/>1.0.1

* 对 Azure Cosmos DB SQL API 帐户的 BulkImport 操作的小 bug 修复。

### <a name="100"></a><a name="1.0.0"/>1.0.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkImport 和 BulkUpdate 操作的支持。

## <a name="next-steps"></a>后续步骤

若要了解批量执行程序 Java 库，请参阅以下文章：

[Java 大容量执行器库 SDK 和发布信息](sql-api-sdk-bulk-executor-java.md)
