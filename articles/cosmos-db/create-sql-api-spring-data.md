---
title: 快速入门 - 通过 Azure Cosmos DB 使用 Spring Data Azure Cosmos DB v3 创建文档数据库
description: 本快速入门演示了一个可以用来连接并查询 Azure Cosmos DB SQL API 的 Spring Data Azure Cosmos DB v3 代码示例
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 0ccab295d39e463d4b6d1e764862678469fba751
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776402"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入门：构建一个 Spring Data Azure Cosmos DB v3 应用以管理 Azure Cosmos DB SQL API 数据


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入门中，你将通过 Azure 门户使用从 GitHub 克隆的 Spring Data Azure Cosmos DB v3 应用来创建和管理 Azure Cosmos DB SQL API 帐户。 首先，请使用 Azure 门户创建 Azure Cosmos DB SQL API 帐户，然后使用 Spring Data Azure Cosmos DB v3 连接器创建 Spring Boot 应用，再使用 Spring Boot 应用程序将资源添加到 Cosmos DB 帐户。 Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。

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

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。 你还可以使用 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)以及 URI `https://localhost:8081` 和密钥 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Java 开发工具包 (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 将 `JAVA_HOME` 环境变量指向其中安装了 JDK 的文件夹。
- [Maven 二进制存档](https://maven.apache.org/download.cgi)。 在 Ubuntu 上运行 `apt-get install maven`，以安装 Maven。
- [Git](https://www.git-scm.com/downloads)。 在 Ubuntu 上运行 `sudo apt-get install git`，以安装 Git。

## <a name="introductory-notes"></a>介绍性说明

Cosmos DB 帐户的结构。 不管使用 API 还是编程语言，都具有以下特点：一个 Cosmos DB 帐户包含零个或零个以上的数据库，一个数据库 (DB) 包含零个或零个以上的容器，一个容器包含零个或零个以上的项，如下图所示：

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 帐户实体" border="false":::

可在[此处](databases-containers-items.md)阅读有关数据库、容器和项的详细信息。 几个重要属性在容器级别定义，其中包括预配吞吐量和分区键。 

预配吞吐量以具有货币价格的请求单位 (*RU*) 度量，是帐户运营成本中重要的确定性因素。 可以按单容器粒度或单数据库粒度选择预配吞吐量，但通常首选容器级别吞吐量规范。 可在[此处](set-throughput.md)阅读有关吞吐量预配的详细信息。

将项插入 Cosmos DB 容器时，数据库会添加更多的存储和计算来处理请求，以水平方式增长。 存储和计算容量添加到称为分区的离散单元中，你必须在文档中选择一个字段作为分区键，以便将每个文档映射到分区。 分区的管理方式是从分区键值的范围内为每个分区分配一个大致相等的切片；因此，建议选择相对随机或均匀分布的分区键。 否则，某些分区看到的请求数会多得多（热分区），而其他分区看到的请求数会少得多（冷分区），这是应该避免的。 可以在[此处](partitioning-overview.md)详细了解分区。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 SQL API 帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>添加容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 接下来，克隆 GitHub 中的 SQL API 应用程序，设置连接字符串，并运行应用程序。 会看到以编程方式处理数据是多么容易。 

运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以跳到[运行应用](#run-the-app)。 

### <a name="application-configuration-file"></a>应用程序配置文件

在这里，我们将展示 Spring Boot 和 Spring Data 如何增强用户体验 - 建立 Cosmos 客户端并连接到 Cosmos 资源的过程现在是配置而不是代码。 在应用程序启动时，Spring Boot 使用 **application.properties** 中的设置来处理这整个样板：

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

创建 Azure Cosmos DB 帐户、数据库和容器后，只需填充配置文件中的空白，Spring Boot/Spring Data 就会自动执行以下操作：(1) 使用 URI 和密钥创建基础 Java SDK `CosmosClient` 实例；(2) 连接到数据库和容器。 你已准备完毕 - **没有更多的资源管理代码！**

### <a name="java-source"></a>Java 源

Spring Data value-add 也来自其简单、干净、标准化和独立于平台的接口，该接口用于在数据存储上执行操作。 基于上面链接的 Spring Data GitHub 示例，下面提供了 CRUD 和查询示例，用于使用 Spring Data Azure Cosmos DB 操作 Azure Cosmos DB 文档。

* 使用 `save` 方法创建和更新项。

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* 使用存储库中定义的派生查询方法进行点读取。 `findByIdAndLastName` 针对 `UserRepository` 执行点读取。 方法名称中提到的字段会导致 Spring Data 执行由 `id` 和 `lastName` 字段定义的点读取操作：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* 使用 `deleteAll` 删除项：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* 基于存储库方法名称的派生查询。 Spring Data 将 `UserRepository` `findByFirstName` 方法实现为对 `firstName` 字段的 Java SDK SQL 查询（此查询无法实现为点读取）：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>运行应用

现在返回到 Azure 门户，获取连接字符串信息，并使用终结点信息启动应用。 这样，应用程序就可以与托管的数据库进行通信。

1. 在 git 终端窗口中，通过 `cd` 转至示例代码文件夹。

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. 在 git 终端窗口中，使用以下命令安装所需的 Spring Data Azure Cosmos DB 包。

    ```bash
    mvn clean package
    ```

3. 在 git 终端窗口中，使用以下命令启动 Spring Data Azure Cosmos DB 应用程序：

    ```bash
    mvn spring-boot:run
    ```
    
4. 此应用加载 **application.properties** 并连接你的 Azure Cosmos DB 帐户中的资源。
5. 此应用将执行上述的点 CRUD 操作。
6. 此应用将执行派生的查询。
7. 此应用不删除你的资源。 如果要避免产生费用，请切换回门户，[清理你的帐户中的资源](#clean-up-resources)。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用数据资源管理器创建 Azure Cosmos DB SQL API 帐户、创建文档数据库和容器，以及如何通过运行 Spring Data 应用以编程方式执行同一操作。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
