---
title: 使用 Java 创建 Azure Cosmos DB 文档数据库
description: 演示一个可以用来连接到 Azure Cosmos DB SQL API 并进行查询的 Java 代码示例
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: d277ef51fd7f39ffab7ed4ace8848c682b28c383
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001226"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>快速入门：使用 Azure Cosmos DB SQL API 帐户生成 Java 应用程序


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

本快速入门介绍如何使用 Java 应用程序创建和管理 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帐户的资源。 首先，请使用 Azure 门户创建 Azure Cosmos DB SQL API 帐户，使用 [SQL Java SDK](sql-api-sdk-async-java.md) 创建 Java 应用，使用 Java 应用程序将资源添加到 Cosmos DB 帐户。 本快速入门中的说明适用于任何能够运行 Java 的操作系统。 完成本快速入门以后，你就会熟悉如何通过 UI 或编程方式（以首选方式为准）创建和修改 Cosmos DB 数据库和容器。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

此外： 

* [Java 开发工具包 (JDK) 版本 8](https://aka.ms/azure-jdks)
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](https://maven.apache.org/download.cgi)和[安装](https://maven.apache.org/install.html) [Maven](https://maven.apache.org/) 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

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

1. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以跳到[运行应用](#run-the-app)。 

* `AsyncDocumentClient` 初始化。 [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) 为 Azure Cosmos 数据库服务提供客户端逻辑表示。 此客户端用于对服务配置和执行请求。

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* [Database](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database) 创建。

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection) 创建。

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* 使用 [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document) 方法创建文档。

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* 使用 [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable) 方法，对 JSON 执行 SQL 查询。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>运行应用

现在返回到 Azure 门户，获取连接字符串信息，并使用终结点信息启动应用。 这样，应用程序就可以与托管的数据库进行通信。


1. 在 git 终端窗口中，通过 `cd` 转至示例代码文件夹。

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. 在 git 终端窗口中，使用以下命令安装所需的 Java 包。

    ```bash
    mvn package
    ```

3. 在 git 终端窗口中，使用以下命令启动 Java 应用程序（将 YOUR_COSMOS_DB_HOSTNAME 替换为门户中带引号的 URI 值，将 YOUR_COSMOS_DB_MASTER_KEY 替换为门户中带引号的主键）

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    此时，终端窗口显示通知，提示 FamilyDB 数据库已创建。 
    
4. 按下某个键创建数据库，然后按下另一个键创建集合。 

    在浏览器中切换回到数据资源管理器，可以看到，帐户中现在包含 FamilyDB 数据库和 FamilyCollection 集合。

5. 切换到控制台窗口，按下某个键创建第一个文档，再按下另一个键创建第二个文档。 然后切换回到数据资源管理器以查看这些资源。 

6. 按下某个键运行查询，并在控制台窗口中查看输出。 

7. 应用不删除创建的资源。 切换回门户，以便在帐户中[清理资源](#clean-up-resources)，  以免产生费用。

    ![控制台输出](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用数据资源管理器创建 Azure Cosmos 帐户、文档数据库和容器，以及如何通过运行应用以编程方式执行同一操作。 现在可以将其他数据导入 Azure Cosmos 容器。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
