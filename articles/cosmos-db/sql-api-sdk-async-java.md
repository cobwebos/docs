---
title: Azure Cosmos DB：SQL Async Java API、SDK 和资源
description: 了解有关 SQL Async Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 509c6e7a7940cb740a3dcbf81deed9f0d26c2bc8
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802848"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>适用于 SQL API 的 Azure Cosmos DB Async Java SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST] (/rest/api
> * [REST 资源提供程序](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK 与 SQL API Java SDK 的区别在于，前者通过支持 [Netty 库](https://netty.io/)提供异步操作。 先存在的 [SQL API Java SDK](sql-api-sdk-java.md) 不支持异步操作。 

> [!IMPORTANT]  
> 这不是最新的 Azure Cosmos DB Java SDK！ 请考虑将 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) 用于项目。 若要升级，请按照[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南和 [Reactor 与 RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 指南中的说明进行操作。 
>

| |  |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 文档** |[Java API 参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable&preserve-view=true) | 
|**参与 SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**入门** | [Async Java SDK 入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**代码示例** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **性能提示**| [GitHub 自述文件](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| 受支持的最小运行时|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

