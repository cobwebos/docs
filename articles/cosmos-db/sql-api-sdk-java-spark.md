---
title: Azure Cosmos DB 用于 SQL API 发行说明和资源的 Apache Spark 连接器
description: 了解有关 SQL API Azure Cosmos DB Apache Spark 连接器的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 的每个版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590003"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB 核心 (SQL) API Apache Spark 连接器：发行说明和资源
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

使用适用于核心 (SQL) 的 Azure Cosmos DB Apache Spark 连接器加速大数据分析。 Spark 连接器允许你对存储在 Azure Cosmos DB 中的数据运行 [spark ](https://spark.apache.org/) 作业。 支持批处理和流处理。

你可以将连接器与 [Azure Databricks](https://azure.microsoft.com/services/databricks) 或 [azure HDInsight](https://azure.microsoft.com/services/hdinsight/)配合使用，后者提供 Azure 上的托管 Spark 群集。 下表显示了支持的 Spark 版本。

| 组件 | 版本 |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x 和 2.1.x |
| Scala | 2.11 |
| Azure Databricks 运行时版本 | > 3.4 |

> [!WARNING]
> 此连接器支持 Azure Cosmos DB 的核心 (SQL) API。
> 对于 Cosmos DB for MongoDB API，请使用 [MongoDB Spark 连接器](https://docs.mongodb.com/spark-connector/master/)。
> 对于 Cosmos DB Cassandra API，请使用 [Cassandra Spark 连接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="helpful-content"></a>帮助内容

| 内容 | 链接 |
|---|---|
| **SDK 下载** | [从 Apache Spark 下载](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API 文档** | [Spark 连接器参考]() |
|**参与 SDK** | [GitHub 上 Apache Spark 的 Azure Cosmos DB 连接器](https://github.com/Azure/azure-cosmosdb-spark) | 
|**入门** | [使用 Apache Spark 到 Azure Cosmos DB 的连接器加速大数据分析](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [将 Apache Spark 结构化流式处理与 Apache Kafka 和 Azure Cosmos DB 配合使用](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>版本历史记录

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复在 "id" 中包含已应用 "ChangeFeedMaxPagesPerBatch" 配置的 "|" 字符的流式检查点边界情况

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新增功能
* 使用嵌套分区键时增加了对批量更新的支持
* 在写入到 Cosmos DB 期间增加了对 Decimal 和 Float 数据类型的支持。
* 当使用长 (unix Epoch) 作为值时，添加对时间戳类型的支持
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="308"></a>rdbms-3.0.8
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 使用 "WriteThroughputBudget" 配置时修复了类型强制转换异常。

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>新增功能
* 将批量故障的错误消息添加到异常和日志。
#### <a name="key-bug-fixes"></a>密钥错误修复

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复流检查点问题。

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 使用级别错误修复无意中留下的消息的日志级别以减少干扰

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 在分区拆分期间修复结构化流式处理中的 bug-可能导致缺少某些更改源记录或查看检查点写入的 Null 异常

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复了为 readStream 提供的自定义架构被忽略的 bug

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复回归 (unshaded JAR 包括所有着色的依赖项) 增加生成时间的50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>新增功能
#### <a name="key-bug-fixes"></a>密钥错误修复
* 修复了导致通过 TCP 直接传输失败并出现 RequestTimeoutException 的依赖项问题

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新增功能
* 改进连接管理和连接池以减少元数据调用数
#### <a name="key-bug-fixes"></a>密钥错误修复

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

若要详细了解 Apache Spark，请参阅 [主页](https://spark.apache.org/)。