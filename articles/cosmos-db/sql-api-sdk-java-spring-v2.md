---
title: 用于 SQL API 发行说明和资源的弹簧数据 Azure Cosmos DB v2
description: 了解有关 SQL API 的 "春季 Data Azure Cosmos DB v2" 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590589"
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

用于核心 (SQL) 的弹簧数据 Azure Cosmos DB v2，使开发人员能够在春季应用程序中使用 Azure Cosmos DB。 弹簧数据 Azure Cosmos DB 公开用于处理数据库和集合的弹簧数据接口，使用文档和发出查询。 相同的 Maven 项目支持同步和异步 (被动) Api。 

[弹簧框架](https://spring.io/projects/spring-framework)是一种编程和配置模型，可简化 Java 应用程序开发。 为了对组织的网站进行报价，春季使用依赖关系注入简化了应用程序的 "检测"。 由于生成和测试应用程序变得更加简单，因此许多开发人员喜欢春季。 [春季 Boot](https://spring.io/projects/spring-boot) 扩展了处理管道的这一理念，并密切关注 web 应用程序和微服务的开发。 [弹簧数据](https://spring.io/projects/spring-data) 是一种用于从弹簧或弹簧引导应用程序的上下文中访问数据存储（如 Azure Cosmos DB）的编程模型。 

可以在 [Azure 春季云](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用春季 Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于 Azure Cosmos DB 弹簧数据的 v2。 可在 [此处](sql-api-sdk-java-spring-v3.md)找到 v3 发行说明。 
>
> 弹簧数据 Azure Cosmos DB 仅支持 SQL API。
>
> 以下指南支持其他 Azure Cosmos DB Api 上的弹簧数据：
> * [Apache Cassandra 与 Azure Cosmos DB 的弹簧数据](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [弹簧数据 MongoDB 与 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [弹簧数据 Gremlin 与 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 想要快速开始？
> 1. 安装[最低支持的 Java 运行时版本，JDK 8](/java/azure/jdk/?view=azure-java-stable)，以便可以使用 SDK。
> 2. 使用初学者 Azure Cosmos DB 应用创建弹簧数据，这很 [简单](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)！
> 3. 通过 [春季 Data Azure Cosmos DB 开发人员指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) 来演练基本 Azure Cosmos DB 请求。
>
> 可以通过 [春季 Initializr](https://start.spring.io/)快速启动弹簧 Boot 入门应用！
>

## <a name="helpful-content"></a>帮助内容

| 内容 | 链接 |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文档** | [春季 Data Azure Cosmos DB 参考文档]() |
|**参与 SDK** | [GitHub 上的弹簧数据 Azure Cosmos DB 存储库](https://github.com/microsoft/spring-data-cosmosdb) | 
|**春季 Boot 入门**| [适用于 Java 的 Azure Cosmos DB 春季 Boot 入门客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**带有 Azure Cosmos DB 的弹簧 TODO 应用示例**| [应用服务 Linux 中的端到端 Java 体验 (第2部分) ](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**开发人员指南** | [春季 Data Azure Cosmos DB 开发人员指南](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**入门指南** | [如何将 Spring Boot Starter 与 Azure Cosmos DB SQL API 配合使用](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [适用于 Azure 春季 Boot 入门的 GitHub 存储库 Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**应用服务示例** | [如何将 Spring 和 Cosmos DB 与 Linux 上的应用服务配合使用](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 应用示例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本历史记录

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21) 
#### <a name="new-features"></a>新增功能
* 将弹簧 boot 版本更新为2.3。0 
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19) 
#### <a name="new-features"></a>新增功能
* Azure Cosmos DB 版本更新为 v 3.7。3
#### <a name="key-bug-fixes"></a>密钥错误修复
* 包含来自 Cosmos SDK v 3.7.3 的内存泄漏修补程序和 netty 版本升级 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06) 
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 固定 allowTelemetry 标志，将其从 CosmosDbConfig 中考虑
* 已修复容器上的 TTL 属性

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25) 
#### <a name="new-features"></a>新增功能
* 通过分区键 API 添加了新的 findAll
* 已将 cosmos 版本更新为3.7。0
#### <a name="key-bug-fixes"></a>密钥错误修复
* Fixed > 容器
* Fixed entityClass & domainClass-> domainType
* 修复了 "返回存储库而不是输入实体保存的实体集合"

### <a name="2110-2020-02-25"></a>HDP-2.1.10 (2020-02-25) 
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* "返回存储库保存的实体集合，而不是输入实体" 的向后移植修复

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15) 
#### <a name="new-features"></a>新增功能
* 将 azure cosmos 版本更新为 v 3.6。0
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31) 
#### <a name="new-features"></a>新增功能
* 将 Cosmos DB SDK 版本更新为3.5。0
* 添加了批注字段以启用/禁用自动创建集合
* 更好的异常处理，通过 CosmosDBAccessException 公开 CosmosClientException
* 通过 ResponseDiagnostics 公开的 requestCharge 和 activityId
#### <a name="key-bug-fixes"></a>密钥错误修复
* SDK 3.5.0 更新修复 "Cosmos DB HTTP 响应标头大于8192字节时出现异常"，"ConsistencyPolicy. defaultConsistencyLevel ( # A1 在受限过期和一致前缀上失败"
* 已修复 findById Api 行为，在未找到时返回 empty，而不是引发异常
* 修复了在使用 CosmosPageRequest 时，在下一页上未应用排序的 bug

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26) 
#### <a name="new-features"></a>新增功能
* 添加了批注字段以启用/禁用自动创建集合
#### <a name="key-bug-fixes"></a>密钥错误修复
* 已修复 findById Api 行为，在未找到时返回 empty，而不是引发异常

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21) 
#### <a name="new-features"></a>新增功能
* 完成反应 Cosmos 存储库支持
* Cosmos DB 请求诊断字符串和查询指标支持
* Cosmos DB SDK 版本更新到3.3。1
* 春季 Framework 版本升级到5.2。0
* 春季 Data Commons 版本升级到2.2。0
* 添加了 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api
* 从 azure 中删除了依赖项-doumentdb
* 更名 DocumentDb 到 Cosmos
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复了 "当 pageSize 小于存储库中的项时，排序引发异常"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18) 
#### <a name="new-features"></a>新增功能
* 弃用文档 DB Api
* 添加了 findByIdAndPartitionKey、deleteByIdAndPartitionKey Api。
* 添加了基于 _etag 的开放式锁定
* 已启用文档集合名称的 SPeL 表达式
* ObjectMapper 改进。
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18) 
#### <a name="new-features"></a>新增功能
* 添加了 Cosmos SDK v3 依赖项
* 添加了反应性 Cosmos 存储库
* 更新了 DocumentDbTemplate 的实现以使用 Cosmos SDK v3。
* 反应 Cosmos 存储库支持的其他配置更改。
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19) 
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 删除的 Applicationinsights.config 依赖项
    * 依赖项污染的潜在风险
    * Java 11 不兼容
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20) 
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 向后移植删除 Applicationinsights.config 依赖项
    * 依赖项污染的潜在风险
    * Java 11 不兼容
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07) 
#### <a name="new-features"></a>新增功能
* 将 master 版本更新为2.1。1
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07) 
#### <a name="new-features"></a>新增功能
* 忽略遥测中的所有异常
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17) 
#### <a name="new-features"></a>新增功能
* 将版本更新为2.1.0 以解决问题
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13) 
#### <a name="new-features"></a>新增功能
* Add 关键字 exists startsWith
* 更新自述文件
#### <a name="key-bug-fixes"></a>密钥错误修复
* 解决 "无法直接调用实体的自助 href"
* 修复 "如果未创建集合，findAll 将失败"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (预发布)  (2018-08-23) 
#### <a name="new-features"></a>新增功能
* 将包从 documentdb 重命名为 cosmosdb，
* 添加了 query method 关键字的新功能，支持来自 Sql API 的16个关键字。
* 通过分页和排序添加查询的新功能。
* 简化 cosmosdb 的配置。
* 添加 deleteCollection 和 deleteAll API。

#### <a name="key-bug-fixes"></a>密钥错误修复
* Bug 修复和缺陷增强。

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

若要了解有关弹簧框架的详细信息，请参阅 [项目主页](https://spring.io/projects/spring-framework)。

若要详细了解弹簧 Boot，请参阅 [项目主页](https://spring.io/projects/spring-boot)。

若要了解有关弹簧数据的详细信息，请参阅 [项目主页](https://spring.io/projects/spring-data)。