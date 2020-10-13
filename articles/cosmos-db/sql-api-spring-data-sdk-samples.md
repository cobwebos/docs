---
title: Azure Cosmos DB SQL API：Spring Data v3 示例
description: 在 GitHub 上查找使用 Azure Cosmos DB SQL API 的常见任务的 Spring Data v3 示例，包括 CRUD 操作。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: adbec15e252a7b5cbd55f1b571d55455fb313a01
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91337670"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API：Spring Data Azure Cosmos DB v3 示例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 示例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 示例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 示例](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK 示例](sql-api-spring-data-sdk-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> 这些发行说明适用于 Spring Data Azure Cosmos DB 的版本 3。 可以[在此处找到版本 2 的发行说明](sql-api-sdk-java-spring-v2.md)。 
>
> Spring Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB API 上的 Spring Data：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [将 Spring Data Gremlin 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- 可以[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

[azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) GitHub 存储库中包含可对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的最新示例应用程序。 本文将提供：

* 每个示例 Spring Data Azure Cosmos DB 项目文件中各项任务的链接。 
* 指向相关的 API 参考内容的链接。

**先决条件**

需要以下条件才能运行此示例应用程序：

* Java 开发工具包 8
* Spring Data Azure Cosmos DB v3

可以选择使用 Maven 获取最新的 Spring Data Azure Cosmos DB v3 二进制文件，以便在项目中使用。 Maven 会自动添加任何必需的依赖项。 否则，可以直接下载 pom.xml 文件中列出的依赖项并将它们添加到生成路径。

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**运行示例应用程序**

克隆示例存储库：
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

可以使用 IDE（Eclipse、IntelliJ 或 VSCODE）或使用 Maven 从命令行运行示例。

必须在 application.properties 中设置这些环境变量，

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

才能授予示例对你的帐户、数据库和容器进行读/写访问的权限。

你的 IDE 可能会提供执行 Spring Data 示例代码的功能。 否则，可以使用以下终端命令来执行该示例：

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>文档 CRUD 示例
[samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅概念性文章：[使用数据库、容器和项](databases-containers-items.md)。

| 任务 | API 参考 |
| --- | --- |
| [创建文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [按 ID 读取文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [删除所有文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>派生查询方法示例
[samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 查询，可参阅 [Baeldung 在 Spring 中的派生查询方法](https://www.baeldung.com/spring-data-derived-queries)一文。

| [查询文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository.derivedQueryMethod |

## <a name="custom-query-examples"></a>自定义查询示例
[samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) 文件演示如何使用 SQL 查询语法执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅 [Azure Cosmos DB 的 SQL 查询示例](how-to-sql-query.md)。 


| 任务 | API 参考 |
| --- | --- |
| [查询所有文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query 注释 |
| [使用 == 查询等式](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query 注释 |
| [使用 != 和 NOT 查询不等式](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query 注释 |
| [使用 >、<、>=、<= 等范围运算符进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query 注释 |
| [使用范围运算符对字符串进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query 注释 |
| [使用 ORDER BY 进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query 注释 |
| [使用 DISTINCT 查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query 注释 |
| [使用聚合函数进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query 注释 |
| [使用子文档](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query 注释 |
| [使用文档内联接进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query 注释 |
| [使用字符串、数学和数组运算符进行查询](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query 注释 |