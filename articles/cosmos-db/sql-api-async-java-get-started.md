---
title: 使用异步 Java SDK 生成 Java 应用以管理 Azure Cosmos DB SQL API 数据
description: 本教程展示了如何通过异步 Java 应用程序使用 Azure Cosmos DB SQL API 帐户来存储和访问数据。
keywords: nosql 教程, 联机数据库, java 控制台应用程序
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: d9adaaaaf64204e27c07b0728ae4bbc473a5c70a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875546"
---
# <a name="tutorial-build-a-java-app-with-async-java-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教程：使用异步 Java SDK 生成 Java 应用以管理 Azure Cosmos DB SQL API 数据

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

本教程介绍如何使用异步 Java SDK 生成 Java 应用程序，以便存储和访问 Azure Cosmos DB SQL API 数据。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建并连接到 Azure Cosmos DB 帐户
> * 配置解决方案
> * 创建集合
> * 创建 JSON 文档
> * 查询集合

## <a name="prerequisites"></a>先决条件

请确保具有以下资源：

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)。
* [Java 开发工具包 (JDK) 8+](https://aka.ms/azure-jdks)。
* [Maven](http://maven.apache.org/download.cgi)。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户
让我们创建一个 Azure Cosmos DB 帐户。 如果已有一个可用的帐户，可以直接跳到[克隆 GitHub 项目](#GitClone)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[克隆 GitHub 项目](#GitClone)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>步骤 2：克隆 GitHub 存储库

首先，可以根据 [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)（Azure Cosmos DB 和 Java 入门）中所述克隆 GitHub 存储库。 例如，在本地目录中运行以下命令，在本地检索示例项目。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

目录包含 `pom.xml` 和 `src/main/java/com/microsoft/azure/cosmosdb/sample` 文件夹，其中包含 Java 源代码（包括 `Main.java`）。 项目包含的代码是使用 Azure Cosmos DB 执行操作（例如创建文档以及查询集合中的数据）所需的。 `pom.xml` 包括 [Maven 上的 Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) 的依赖项。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>步骤 3：连接到 Azure Cosmos DB 帐户

接下来，返回到 [Azure 门户](https://portal.azure.com)检索终结点和主密钥。 Azure Cosmos DB 终结点和主密钥是必需的，可让应用程序知道要连接的对象，使 Azure Cosmos DB 信任应用程序的连接。 `AccountSettings.java` 文件包含主密钥和 URI 值。 

在 Azure 门户中，导航到 Azure Cosmos DB 帐户，然后单击“密钥”。 从门户中复制 URI 和主密钥并将其粘贴到 `AccountSettings.java` 文件中。 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![从门户获取密钥的屏幕截图][keys]

## <a name="step-4-initialize-the-client-object"></a>步骤 4：初始化客户端对象

通过使用“AccountSettings.java”文件中定义的主机 URI 和主密钥值初始化客户端对象

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>步骤 5：创建数据库

可以通过使用 DocumentClient 类的 createDatabaseIfNotExists() 方法来创建 Azure Cosmos DB [数据库](databases-containers-items.md#azure-cosmos-databases)。 数据库是跨集合分区的 JSON 文档存储的逻辑容器。

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>步骤 6：创建集合

> [!WARNING]
> **createCollection** 创建一个具有保留吞吐量的新集合，这会牵涉到价格问题。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。
> 
> 
可以通过使用 DocumentClient 类的 createDocumentCollectionIfNotExists() 方法来创建集合。 集合是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>步骤 7：创建 JSON 文档

可以使用 DocumentClient 类的 createDocument 方法创建文档。 文档是用户定义的（任意）JSON 内容。 现在，我们可以插入一个或多个文档。 “src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java”文件定义系列 JSON 文档 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>步骤 8：查询 Azure Cosmos DB 资源

Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行[各种查询](how-to-sql-query.md)。 以下示例代码展示了如何将 SQL 语法与 [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments) 方法一起使用来查询 Azure Cosmos DB 中的文档。

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>步骤 9：运行 Java 控制台应用程序

若要从控制台运行应用程序，请导航到项目文件夹，然后使用 Maven 进行编译：

```bash
mvn package
```

运行 `mvn package` 将从 Maven 下载最新的 Azure Cosmos DB 库，并生成 `GetStarted-0.0.1-SNAPSHOT.jar`。 然后通过运行以下命令运行该应用：

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

祝贺你！ 现已完成本 NoSQL 教程，并构建了一个正常运行的 Java 控制台应用程序！

## <a name="next-steps"></a>后续步骤

本教程介绍了如何通过异步 Java SDK 生成 Java 应用以管理 Azure Cosmos DB SQL API 数据。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [使用 JavaScript SDK 和 Azure Cosmos DB 生成 Node.js 控制台应用](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
