---
title: 用于 SQL API 的 Azure Cosmos DB Java SDK v4 的发行说明和资源
description: 了解有关用于 SQL API 的 Azure Cosmos DB Java SDK v4 和 SDK 的所有信息，包括发行日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725646"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>用于 Core (SQL) API 的 Azure Cosmos DB Java SDK v4：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行工具 - Java](sql-api-sdk-bulk-executor-java.md)

用于 Core (SQL) 的 Azure Cosmos DB Java SDK v4 将异步 API 和同步 API 合并到一个 Maven 项目中。 v4 SDK 基于 Project Reactor 和 [Netty 库](https://netty.io/)提供了增强的性能、新 API 功能和 Async 支持。 通过 Azure Cosmos DB Java SDK v4 与 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 和 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)，用户可以提高性能。

> [!IMPORTANT]  
> 这些发行说明仅适用于 Azure Cosmos DB Java SDK v4。 如果你当前使用的是早于 v4 的版本，请参阅[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，获取升级到 v4 的相关帮助。
>
> 以下是快速操作的三个步骤！
> 1. 安装[最低支持的 Java 运行时版本，JDK 8](/java/azure/jdk/?view=azure-java-stable)，以便可以使用 SDK。
> 2. 通过 [Azure Cosmos DB Java SDK v4 快速入门指南](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)，可以访问 Maven 项目并完成基本的 Azure Cosmos DB 请求。
> 3. 阅读 Azure Cosmos DB Java SDK v4 [性能提示](performance-tips-java-sdk-v4-sql.md)和[疑难解答](troubleshoot-java-sdk-v4-sql.md)指南，以优化应用程序的 SDK。
>
> 此外，[Azure Cosmos DB 研讨会和实验室](https://aka.ms/cosmosworkshop)也是了解如何使用 Azure Cosmos DB Java SDK v4 的绝佳资源！
>

| |  |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 文档** | [Java API 参考文档](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**参与 SDK** | [GitHub 上用于 Java 的 Azure SDK 中央存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**入门** | [快速入门：生成 Java 应用以管理 Azure Cosmos DB SQL API 数据](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)   [具有快速入门代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本代码示例** | [Azure Cosmos DB：SQL API 的 Java 示例](sql-api-java-sdk-samples.md)   [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**包含更改源的控制台应用**| [更改源 - Java SDK v4 示例](create-sql-api-java-changefeed.md)   [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web 应用示例**| [使用 Java SDK v4 构建 Web 应用](sql-api-java-application.md)   [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **性能提示**| [适用于 Java SDK v4 的性能提示](performance-tips-java-sdk-v4-sql.md)| 
| **故障排除** | [排查 Java SDK v4 问题](troubleshoot-java-sdk-v4-sql.md) |
| **从旧 SDK 迁移到 v4** | [迁移到 Java V4 SDK](migrate-java-v4-sdk.md) |
| 受支持的最小运行时|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB 研讨会和实验室** |[Cosmos DB 研讨会主页](https://aka.ms/cosmosworkshop)

