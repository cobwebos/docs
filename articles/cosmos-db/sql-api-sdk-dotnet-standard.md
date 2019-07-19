---
title: Azure Cosmos DB：SQL .NET Standard API, SDK & 资源
description: 了解有关 SQL API 和 .NET SDK 的所有信息, 包括发布日期、停用日期和每个版本的 Azure Cosmos DB .NET SDK 之间所做的更改。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229019"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>适用于 SQL API 的 .NET Standard SDK Azure Cosmos DB:下载和发行说明
> [!div class="op_single_selector"]
> * [.NET 标准](sql-api-sdk-dotnet-standard.md)
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API 文档**|[ 参考文档](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**示例**|[.NET代码示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**入门**|[Azure Cosmos DB .NET SDK 入门](sql-api-get-started.md)|
|**Web 应用教程**|[使用 Azure Cosmos DB 进行 Web 应用程序开发](sql-api-dotnet-application.md)|
|**当前受支持的框架**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>发行说明

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* .NET SDK[版本 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)的公开上市
* 目标 .NET Standard 2.0, 支持 .NET framework 4.6.1 + 和 .NET Core 2.0 +
* 新的对象模型, 包含顶级 CosmosClient 和方法跨相关数据库和容器类拆分
* 新的高性能流 Api
* 对更改源处理器 Api 的内置支持
* 适用于 CosmosClient、容器和更改源处理器的流畅生成器 Api
* 惯用吞吐量管理 Api
* 针对数据库、容器、项、查询和吞吐量请求的粒度 RequestOptions 和 ResponseTypes
* 扩展未分区容器的能力 
* 可扩展和可自定义的序列化程序
* 支持自定义处理程序的可扩展请求管道


## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月  发出通知，以便顺利转换为更高版本/受支持版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Azure Cosmos DB 发出的任何请求都会遭服务拒绝。

<br/>

| Version | 发布日期 | 停用日期 |
| --- | --- | --- |
| [3.0.0](#3.0.0) |2019年7月15日 |--- |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 

