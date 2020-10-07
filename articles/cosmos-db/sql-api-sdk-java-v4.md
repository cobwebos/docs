---
title: 用于 SQL API 的 Azure Cosmos DB Java SDK v4 的发行说明和资源
description: 了解有关用于 SQL API 的 Azure Cosmos DB Java SDK v4 和 SDK 的所有信息，包括发行日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 686c1e37e093a358bb5122fbfeccdfd98eb301ce
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804225"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>用于 Core (SQL) API 的 Azure Cosmos DB Java SDK v4：发行说明和资源
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
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

用于 Core (SQL) 的 Azure Cosmos DB Java SDK v4 将异步 API 和同步 API 合并到一个 Maven 项目中。 v4 SDK 基于 Project Reactor 和 [Netty 库](https://netty.io/)提供了增强的性能、新 API 功能和 Async 支持。 通过 Azure Cosmos DB Java SDK v4 与 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 和 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)，用户可以提高性能。

> [!IMPORTANT]  
> 这些发行说明仅适用于 Azure Cosmos DB Java SDK v4。 如果你当前使用的是早于 v4 的版本，请参阅[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，获取升级到 v4 的相关帮助。
>
> 以下是快速操作的三个步骤！
> 1. 安装[最低支持的 Java 运行时版本，JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)，以便可以使用 SDK。
> 2. 通过 [Azure Cosmos DB Java SDK v4 快速入门指南](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)，可以访问 Maven 项目并完成基本的 Azure Cosmos DB 请求。
> 3. 阅读 Azure Cosmos DB Java SDK v4 [性能提示](performance-tips-java-sdk-v4-sql.md)和[疑难解答](troubleshoot-java-sdk-v4-sql.md)指南，以优化应用程序的 SDK。
>
> 此外，[Azure Cosmos DB 研讨会和实验室](https://aka.ms/cosmosworkshop)也是了解如何使用 Azure Cosmos DB Java SDK v4 的绝佳资源！
>

## <a name="helpful-content"></a>帮助性内容

| Content | 链接 |
|---|---|
|**SDK 下载**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 文档** | [Java API 参考文档](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable&preserve-view=true) |
|**参与 SDK** | [GitHub 上用于 Java 的 Azure SDK 中央存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**入门** | [快速入门：生成 Java 应用以管理 Azure Cosmos DB SQL API 数据](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [具有快速入门代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本代码示例** | [Azure Cosmos DB：SQL API 的 Java 示例](sql-api-java-sdk-samples.md) <br> [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**包含更改源的控制台应用**| [更改源 - Java SDK v4 示例](create-sql-api-java-changefeed.md) <br> [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web 应用示例**| [使用 Java SDK v4 构建 Web 应用](sql-api-java-application.md) <br> [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **性能提示**| [适用于 Java SDK v4 的性能提示](performance-tips-java-sdk-v4-sql.md)| 
| **故障排除** | [排查 Java SDK v4 问题](troubleshoot-java-sdk-v4-sql.md) |
| **从旧 SDK 迁移到 v4** | [迁移到 Java V4 SDK](migrate-java-v4-sdk.md) |
| 受支持的最小运行时|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Azure Cosmos DB 研讨会和实验室** |[Cosmos DB 研讨会主页](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>后续步骤
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。