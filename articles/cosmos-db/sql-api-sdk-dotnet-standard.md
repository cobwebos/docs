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
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663813"
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
> * [大容量执行程序-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API 文档**|[ 参考文档](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**示例**|[.NET代码示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**入门**|[Azure Cosmos DB .NET SDK 入门](sql-api-get-started.md)|
|**Web 应用教程**|[使用 Azure Cosmos DB 进行 Web 应用程序开发](sql-api-dotnet-application.md)|
|**当前受支持的框架**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>发行说明
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>已添加
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541)向客户端和查询选项添加了一致性级别
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544)为 LINQ 添加了继续标记支持
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557)向 item 请求选项添加了触发器选项
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571)使用可选设置添加了默认 JSON.net 序列化程序
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572)已在 CreateContainerIfNotExistsAsync 上添加分区键验证
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581)添加 LINQ to QueryDefinition API
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592)已将 CreateIfNotExistsAsync 添加到容器生成器
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597)向 ResponseMessage 添加了继续标记属性
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604)添加了 LINQ ToStreamIterator 扩展方法

#### <a name="fixed"></a>已修复
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548)修复了 CosmosException () 中的错误类型消息;
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558)LocationCache ConcurrentDict 锁争用修补程序
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561)GetItemLinqQueryable 现在使用 null 查询
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567)查询正确处理不同的语言文化
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574)修复了查询分析因意外异常而失败时的空错误消息
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576)查询将输入正确序列化到流中

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
Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Azure Cosmos DB 发出的任何请求都会遭服务拒绝。

<br/>

| Version | 发布日期 | 停用日期 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019年7月29日 |--- |
| [3.0.0](#3.0.0) |2019年7月15日 |--- |


## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 

