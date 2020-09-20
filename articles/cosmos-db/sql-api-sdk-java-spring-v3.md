---
title: 春季 Data Azure Cosmos DB v3 for SQL API 发行说明和资源
description: 了解 SQL API Azure Cosmos DB v3 的弹簧数据，其中包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817864"
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

用于核心 (SQL) 的弹簧数据 Azure Cosmos DB 版本3允许开发人员在春季应用程序中使用 Azure Cosmos DB。 Spring Data Azure Cosmos DB 公开 Spring Data 接口，以便操作数据库和集合、使用文档和发出查询。 同一 Maven 项目中同时支持 Sync 和 Async (Reactive) API。 

弹簧数据 Azure Cosmos DB 与弹簧数据框架有依赖关系。 Azure Cosmos DB SDK 团队发布了用于春季数据版本2.2 和2.3 的 Maven 项目。

[弹簧框架](https://spring.io/projects/spring-framework)是一种用于简化 Java 应用程序开发的编程和配置模型。 弹簧通过使用依赖关系注入简化应用程序的 "检测"。 许多开发人员（如春季），因为它使生成和测试应用程序变得更加简单。 [春季 Boot](https://spring.io/projects/spring-boot) 扩展了此管道的处理能力，并密切关注 web 应用程序和微服务的开发。 [弹簧数据](https://spring.io/projects/spring-data) 是用于访问数据存储的编程模型和框架，如从弹簧或弹簧引导应用程序的上下文中 Azure Cosmos DB。 

可在 [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用 Spring Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于春季 Data Azure Cosmos DB 的版本3。 可在 [此处找到版本2的发行说明](sql-api-sdk-java-spring-v2.md)。 
>
> 春季 Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB Api 上的弹簧数据：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [将 Spring Data Gremlin 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>从此处开始

# <a name="explore"></a>[浏览](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>这些选项卡包含基本的弹簧数据 Azure Cosmos DB 示例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>配置依赖项

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[“连接”](#tab/connect)

### <a name="connect"></a>连接

指定 Azure Cosmos DB 帐户和容器详细信息。 春季 Data Azure Cosmos DB 会自动创建客户端并连接到容器。

[application. properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties)：
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

创建：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

删除：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[查询](#tab/queries)

查询：
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>资源

* **参与 SDK**： [GitHub 上的弹簧数据 Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)存储库

* **教程**：有关 [GitHub 上的春季 Data Azure Cosmos DB 教程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>版本历史记录

### <a name="300-beta2-september-17-2020"></a>3.0.0 (2020 年9月17日) 

#### <a name="new-features"></a>新增功能

* 已将项目 id 更新到 `azure-spring-data-cosmos` 。
* 已更新与的 cosmos 依赖项 `4.5.0` 。
* `Query Annotation` 支持本机查询。
* 支持 Java 11。
* 通过在批注中公开字段，增加了对嵌套分区键的支持 `partitionKeyPath` `@Container` 。
* 添加了对 `limit` 查询类型的支持 `top` ，以便 `first` 在定义存储库 api 时使用和。

#### <a name="key-bug-fixes"></a>关键 Bug 修复

* 修复了用于批注的嵌套分区键错误 `@GeneratedValue` 。

### <a name="300-beta1-august-17-2020"></a>3.0.0 (8 月17日 2020) 

#### <a name="new-features"></a>新增功能

* 将组 ID 更新到 `com.azure` 。
* 将项目 ID 更新到 `azure-spring-data-2-3-cosmos` 。
* 将 azure cosmos SDK 依赖项更新到 `4.3.2-beta.2` 。
* 添加对审核实体的支持：自动管理 `createdBy` 、 `createdDate` 、 `lastModifiedBy` 和 `lastModifiedDate` 批注字段。
* 为 `@GeneratedValue` 类型为的 id 字段的自动生成 id 添加批注支持 `String` 。
* 为具有多个数据库的单个 Azure Cosmos DB 帐户和多个具有多个数据库的 Azure Cosmos DB 帐户添加多数据库配置支持。
* 添加对 `@Version` 任何字符串字段的批注的支持。
* 将同步 API 返回类型更新为 `Iterable` 类型而不是 `List` 。
* `CosmosClientBuilder`作为弹簧 bean 从 AZURE COSMOS DB SDK 公开到 `@Configuration` 类。
* 更新 `CosmosConfig` 以包含查询指标和响应诊断处理器实现。
* 添加了对返回 `Optional` 单个结果查询的数据类型的支持。

#### <a name="renames"></a>指

* `CosmosDbFactory` 到 `CosmosFactory` 。
* `CosmosDBConfig` 到 `CosmosConfig` 。
* `CosmosDBAccessException` 到 `CosmosAccessException` 。
* `Document` 批注到 `Container` 批注。
* `DocumentIndexingPolicy` 批注到 `CosmosIndexingPolicy` 批注。
* `DocumentQuery` 到 `CosmosQuery` 。
* application. properties 标记 `populateQueryMetrics` 为 `queryMetricsEnabled` 。

#### <a name="key-bug-fixes"></a>关键 Bug 修复

* 将诊断日志记录任务计划给 `Parallel` 线程，以避免阻塞 Netty i/o 线程。
* 修复删除操作时的乐观锁定。
* 修复了子句的转义查询问题 `IN` 。
* 通过允许 `long` 的数据类型解决了问题 `@Id` 。
* 通过允许 `boolean` 、 `long` 、 `int` 和 `double` 作为批注的数据类型，修复了问题 `@PartitionKey` 。
* `IgnoreCase` `AllIgnoreCase` 忽略 case 查询的修复和关键字。
* 自动创建容器时，删除默认请求单位值4000。

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

了解有关 [弹簧框架](https://spring.io/projects/spring-framework)的详细信息。

详细了解 [弹簧 Boot](https://spring.io/projects/spring-boot)。

详细了解 [弹簧数据](https://spring.io/projects/spring-data)。