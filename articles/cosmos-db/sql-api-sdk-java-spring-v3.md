---
title: 春季 Data Azure Cosmos DB v3 for SQL API 发行说明和资源
description: 了解有关 SQL API Azure Cosmos DB v3 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590567"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>用于核心 (SQL) API 的弹簧数据 Azure Cosmos DB v3：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [弹簧数据 v2](sql-api-sdk-java-spring-v2.md)
> * [弹簧数据 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

用于核心 (SQL) 的弹簧数据 Azure Cosmos DB v3，使开发人员能够在春季应用程序中使用 Azure Cosmos DB。 弹簧数据 Azure Cosmos DB 公开用于处理数据库和集合的弹簧数据接口，使用文档和发出查询。 相同的 Maven 项目支持同步和异步 (被动) Api。 

弹簧数据 Azure Cosmos DB 会依赖于弹簧数据框架。 Azure Cosmos DB SDK 团队发布了用于春季数据 v2.0 和 v2.0 的 Maven 项目。

[弹簧框架](https://spring.io/projects/spring-framework)是一种编程和配置模型，可简化 Java 应用程序开发。 为了对组织的网站进行报价，春季使用依赖关系注入简化了应用程序的 "检测"。 由于生成和测试应用程序变得更加简单，因此许多开发人员喜欢春季。 [春季 Boot](https://spring.io/projects/spring-boot) 扩展了处理管道的这一理念，并密切关注 web 应用程序和微服务的开发。 [弹簧数据](https://spring.io/projects/spring-data) 是用于从弹簧或弹簧引导应用程序的上下文中访问数据存储（如 Azure Cosmos DB）的编程模型和框架。 

可以在 [Azure 春季云](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用春季 Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于春季 Data Azure Cosmos DB。 可在 [此处](sql-api-sdk-java-spring-v2.md)找到 v2 发行说明。 
>
> 弹簧数据 Azure Cosmos DB 仅支持 SQL API。
>
> 以下指南支持其他 Azure Cosmos DB Api 上的弹簧数据：
> * [Apache Cassandra 与 Azure Cosmos DB 的弹簧数据](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [弹簧数据 MongoDB 与 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [弹簧数据 Gremlin 与 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>从此处开始

# <a name="explore"></a>[浏览](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>在上面的选项卡上导航，以获取基本弹簧数据 Azure Cosmos DB 示例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>配置依赖项

提供两个弹簧数据 Azure Cosmos DB v3 Maven 项目。

依赖于春季 Data framework v2.0 的项目：
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

依赖于春季 Data framework v2.0 的项目：
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[“连接”](#tab/connect)

### <a name="connect"></a>连接

指定 Azure Cosmos DB 帐户和容器详细信息。 春季 Data Azure Cosmos DB 会自动创建客户端并连接到容器。

[application. properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties)：
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc ops](#tab/docs)

### <a name="document-operations"></a>文档操作

[创建](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[删除](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[查询](#tab/queries)

### <a name="query"></a>查询

[查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)： [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>帮助内容

| 内容 | 春季 Data framework v2。0 | 春季 Data framework v2。0 |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**参与 SDK** | [GitHub 上的弹簧数据 Azure Cosmos DB 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [GitHub 上的弹簧数据 Azure Cosmos DB 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**教程**| [GitHub 上的弹簧数据 Azure Cosmos DB 教程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [GitHub 上的弹簧数据 Azure Cosmos DB 教程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>版本历史记录

### <a name="300-beta1-2020-08-17"></a>3.0.0 (2020-08-17) 
#### <a name="new-features"></a>新增功能
* 已将组 ID 更新到 `com.azure` 。
* 已将项目 ID 更新到 `azure-spring-data-2-3-cosmos` 。
* 已更新 azure cosmos SDK 依赖项 `4.3.2-beta.2` 。
* 支持审核实体-自动管理 System.createdby、createdDate、lastModifiedBy 和 lastModifiedDate 批注字段。
* `@GeneratedValue` 批注支持自动生成类型为的 ID 字段的 id `String` 。
* 多数据库配置支持具有多个数据库的单个 cosmos 帐户和多个具有多个数据库的 cosmos 帐户。
* 支持对 `@Version` 任何字符串字段进行批注。
* 更新的同步 Api 将类型返回到 `Iterable` 类型而不是 `List` 。
* `CosmosClientBuilder`作为春季 bean 从 COSMOS SDK 公开到 `@Configuration` 类。
* 已更新 `CosmosConfig` 为包含查询指标和响应诊断处理器实现。
* 支持为 `Optional` 单个结果查询返回数据类型。
#### <a name="renames"></a>指
* `CosmosDbFactory` 到 `CosmosFactory` 。
* `CosmosDBConfig` 到 `CosmosConfig` 。
* `CosmosDBAccessException` 到 `CosmosAccessException` 。
* `Document` 批注到 `Container` 批注。
* `DocumentIndexingPolicy` 批注到 `CosmosIndexingPolicy` 批注。
* `DocumentQuery` 到 `CosmosQuery` 。
* application. properties 标记 `populateQueryMetrics` 为 `queryMetricsEnabled` 。
#### <a name="key-bug-fixes"></a>密钥错误修复
* 将诊断日志记录任务安排到 `Parallel` 线程，以避免阻塞 Netty i/o 线程。
* 修复了删除操作时的乐观锁定。
* 修复了子句的转义查询问题 `IN` 。
* 通过允许 `long` 的数据类型修复了问题 `@Id` 。
* 已修复问题：允许 `boolean` 、、 `long` `int` ， `double` 作为批注的数据类型 `@PartitionKey` 。
* 修复 `IgnoreCase`  &  `AllIgnoreCase` 了忽略大小写查询的关键字。
* 自动创建容器时，删除了默认的请求单位值4000。

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

若要了解有关弹簧框架的详细信息，请参阅 [项目主页](https://spring.io/projects/spring-framework)。

若要详细了解弹簧 Boot，请参阅 [项目主页](https://spring.io/projects/spring-boot)。

若要了解有关弹簧数据的详细信息，请参阅 [项目主页](https://spring.io/projects/spring-data)。