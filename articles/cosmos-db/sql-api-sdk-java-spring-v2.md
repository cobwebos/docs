---
title: 用于 SQL API 发行说明和资源的弹簧数据 Azure Cosmos DB v2
description: 了解 SQL API Azure Cosmos DB v2 的弹簧数据，其中包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 8d795624097877d20f98a6fd205fb7136cf38007
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069118"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>用于核心 (SQL) API 的弹簧数据 Azure Cosmos DB v2：发行说明和资源
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

 用于核心 (SQL) 的春季 Data Azure Cosmos DB 版本2允许开发人员在春季应用程序中使用 Azure Cosmos DB。 Spring Data Azure Cosmos DB 公开 Spring Data 接口，以便操作数据库和集合、使用文档和发出查询。 同一 Maven 项目中同时支持 Sync 和 Async (Reactive) API。 

[弹簧框架](https://spring.io/projects/spring-framework)是一种用于简化 Java 应用程序开发的编程和配置模型。 弹簧通过使用依赖关系注入简化应用程序的 "检测"。 许多开发人员（如春季），因为它使生成和测试应用程序变得更加简单。 [春季 Boot](https://spring.io/projects/spring-boot) 扩展了此管道的处理能力，并密切关注 web 应用程序和微服务的开发。 [弹簧数据](https://spring.io/projects/spring-data) 是一种用于访问数据存储的编程模型，如从弹簧或弹簧引导应用程序的上下文中 Azure Cosmos DB。 

可在 [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用 Spring Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于春季 Data Azure Cosmos DB 版本2。 可在 [此处找到版本3的发行说明](sql-api-sdk-java-spring-v3.md)。 
>
> 春季 Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB Api 上的弹簧数据：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [将 Spring Data Gremlin 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 希望快速开始？
> 1. 安装 [受支持的最低 Java 运行时（JDK 8](/java/azure/jdk/?view=azure-java-stable)），以便你可以使用 SDK。
> 2. 使用 [starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)创建弹簧数据 Azure Cosmos DB 应用。 操作起来非常简单！
> 3. 查看 [春季 Data Azure Cosmos DB 开发人员指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)，逐步完成基本 Azure Cosmos DB 请求。
>
> 可以使用 [春季 Initializr](https://start.spring.io/)快速启动春季 Boot 入门应用！
>

## <a name="resources"></a>资源

| 资源 | 链接 |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文档** | [Spring Data Azure Cosmos DB 参考文档]() |
|**参与 SDK** | [GitHub 上的弹簧数据 Azure Cosmos DB 存储库](https://github.com/microsoft/spring-data-cosmosdb) | 
|**春季 Boot 入门**| [适用于 Java 的 Azure Cosmos DB Spring Boot Starter 客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**用于 Azure Cosmos DB 的 Spring TODO 应用示例**| [应用服务 Linux 中的端到端 Java 体验（第 2 部分）](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**开发人员指南** | [春季 Data Azure Cosmos DB 开发人员指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**使用 Starter** | [如何将弹簧 Boot Starter 与 Azure Cosmos DB SQL API 一起使用](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [用于 Azure Cosmos DB 春季 Boot 入门的 GitHub 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Azure App Service 示例** | [如何将 Spring 和 Cosmos DB 与 Linux 上的应用服务配合使用](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 应用示例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本历史记录

### <a name="230-may-21-2020"></a>2.3.0 (5 月21日 2020) 
#### <a name="new-features"></a>新增功能
* 将弹簧 Boot 版本更新为2.3.0。


### <a name="225-may-19-2020"></a>2.2.5 (5 月19日 2020) 
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB 版本更新为3.7.3。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 包含 Azure Cosmos DB SDK 3.7.3 中的内存泄漏修复和 Netty 版本升级。

### <a name="224-april-6-2020"></a>2.2.4 (2020 年4月6日) 
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复 `allowTelemetry` 要从中考虑的标志 `CosmosDbConfig` 。
* 修复 `TTL` 容器上的属性。

### <a name="223-february-25-2020"></a>2.2.3 (2020 年2月25日) 
#### <a name="new-features"></a>新增功能
* `findAll`按分区键 API 添加新的。
* 将 Azure Cosmos DB 版本更新为3.7.0。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复 `collectionName`  ->  `containerName` 。
* 修复了 `entityClass` 和 `domainClass`  ->  `domainType` 。
* 修复了 "返回存储库保存的实体集合，而不是输入实体"。

### <a name="2110-february-25-2020"></a>hdp-2.1.10 (2020 年2月25日) 
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* Precise-backports 修复了 "返回按存储库保存的实体集合，而不是输入实体"。

### <a name="222-january-15-2020"></a>2.2.2 (2020 年1月15日) 
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB 版本更新为3.6.0。
#### <a name="key-bug-fixes"></a>关键 Bug 修复

### <a name="221-december-31-2019"></a>2.2.1 (2019 年12月31日) 
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB SDK 版本更新为3.5.0。
* 添加批注字段，以启用或禁用自动集合创建。
* 改进异常处理。 `CosmosClientException`通过公开 `CosmosDBAccessException` 。
* 公开 `requestCharge` 和 `activityId` `ResponseDiagnostics` 。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* SDK 3.5.0 更新修复 "Cosmos DB HTTP 响应标头大于8192字节时出现异常，" "ConsistencyPolicy. defaultConsistencyLevel ( # A1 在受限过期和一致前缀上失败。"
* 修复 `findById` 方法的行为。 以前，如果找不到实体而不是引发异常，则此方法返回空。
* 修复了一个 bug，在使用时，不会在下一页上应用排序 `CosmosPageRequest` 。

### <a name="219-december-26-2019"></a>2.1.9 (2019 年12月26日) 
#### <a name="new-features"></a>新增功能
* 添加批注字段，以启用或禁用自动集合创建。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
*  修复 `findById` 方法的行为。 以前，如果找不到实体而不是引发异常，则此方法返回空。

### <a name="220-october-21-2019"></a>2.2.0 (2019 年10月21日) 
#### <a name="new-features"></a>新增功能
* 完成反应 Cosmos 存储库支持。
* Azure Cosmos DB 请求诊断字符串和查询指标支持。
* Azure Cosmos DB SDK 版本更新为3.3.1。
* 春季 Framework 版本升级到5.2.0。
* 春季 Data Commons 版本升级到2.2.0。
* 添加 `findByIdAndPartitionKey` 和 `deleteByIdAndPartitionKey` api。
* 删除 azure-documentdb 的依赖项。
* Rebrands DocumentDB 到 Azure Cosmos DB。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复了当 pageSize 小于存储库中的项目总数时，排序引发异常。

### <a name="218-october-18-2019"></a>2.1.8 () 年10月18日，2019
#### <a name="new-features"></a>新增功能
* 弃用 DocumentDB Api。
* 添加 `findByIdAndPartitionKey` 和 `deleteByIdAndPartitionKey` api。
* 添加基于的乐观锁定 `_etag` 。
* 启用文档集合名称的 SpEL 表达式。
* 增加了 `ObjectMapper` 改进。

### <a name="217-october-18-2019"></a>2.1.7 () 年10月18日，2019
#### <a name="new-features"></a>新增功能
* 添加 Azure Cosmos DB SDK 版本3依赖项。
* 添加反应性 Cosmos 存储库。
* 的更新实现 `DocumentDbTemplate` 以使用 SDK 版本 3 Azure Cosmos DB。
* 为反应 Cosmos 存储库支持添加其他配置更改。

### <a name="212-march-19-2019"></a>2.1.2 (3 月19日 2019) 
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 删除 `applicationInsights` 以下项的依赖项：
    * 依赖项污染的潜在风险。
    * Java 11 不兼容。
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="207-march-20-2019"></a>2.0.7 (2019 年3月20日) 
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 向后移植删除 `applicationInsights` 以下项的依赖项：
    * 依赖项污染的潜在风险。
    * Java 11 不兼容。
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="211-march-7-2019"></a>2.1.1 (2019 年3月7日) 
#### <a name="new-features"></a>新增功能
* 将主版本更新为2.1.1。

### <a name="206-march-7-2019"></a>2.0.6 (2019 年3月7日) 
#### <a name="new-features"></a>新增功能
* 忽略遥测中的所有异常。

### <a name="210-december-17-2018"></a>2.1.0 (2018 年12月17日) 
#### <a name="new-features"></a>新增功能
* 将版本更新为2.1.0 以解决问题。

### <a name="205-september-13-2018"></a>2.0.5 (2018 年9月13日) 
#### <a name="new-features"></a>新增功能
* 添加关键字 `exists` 和 `startsWith` 。
* 更新自述文件。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复了 "无法直接调用实体的自助 href"。
* 修复了 "如果未创建集合，findAll 将失败"。

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (预发布版本)  (2018 年8月23日) 
#### <a name="new-features"></a>新增功能
* 将包从 documentdb 重命名为 cosmosdb。
* 新增了 query method 关键字的功能。 现在支持来自 SQL API 的16个关键字。
* 通过分页和排序添加查询的新功能。
* 简化了 cosmosdb 的配置。
* 添加 `deleteCollection` 和 `deleteAll` api。

#### <a name="key-bug-fixes"></a>关键 Bug 修复
* Bug 修复和缺陷缓解。

## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤
了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

了解有关 [弹簧框架](https://spring.io/projects/spring-framework)的详细信息。

详细了解 [弹簧 Boot](https://spring.io/projects/spring-boot)。

详细了解 [弹簧数据](https://spring.io/projects/spring-data)。