---
title: "使用 Java 创建 Azure Cosmos DB 文档数据库 | Microsoft Docs | Microsoft Docs'"
description: "演示一个可以用来连接到 Azure Cosmos DB DocumentDB API 并进行查询的 Java 代码示例"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.openlocfilehash: f4bf1992b1f13c61f44695f641b3cb3248b5672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB：使用 Java 和 Azure 门户创建文档数据库

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门通过适用于 Azure Cosmos DB 的 Azure 门户工具创建文档数据库。 本快速入门还介绍了如何使用 [DocumentDB Java API](documentdb-sdk-java.md) 快速创建 Java 控制台应用。 本快速入门中的说明适用于任何能够运行 Java 的操作系统。 完成本快速入门以后，即可熟悉如何通过 UI 或编程方式创建和修改文档数据库资源，具体取决于你首选哪种方式。

## <a name="prerequisites"></a>先决条件

* [Java 开发工具包 (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](http://maven.apache.org/download.cgi)和[安装](http://maven.apache.org/install.html) [Maven](http://maven.apache.org/) 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 SQL (Cosmos DB) 数据库帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>添加示例数据

现在可以使用数据资源管理器将数据添加到新集合。

1. 在数据资源管理器中，新数据库会显示在“集合”窗格中。 展开 **Tasks** 数据库，展开 **Items** 集合，单击“文档”，然后单击“新建文档”。 

   ![在 Azure 门户的数据资源管理器中创建新文档](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. 现在，将文档添加到具有以下结构的集合。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. 将 json 添加到“文档”选项卡以后，即可单击“保存”。

    ![通过复制添加 json 数据，然后在 Azure 门户的数据资源管理器中单击“保存”](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  再创建并保存一个文档，在其中插入 `id` 属性的唯一值，并将其他属性更改为适当值。 新文档可以具有所需的任何结构，因为 Azure Cosmos DB 不对数据施加任何架构。

     现在可以在数据资源管理器中使用查询来检索数据，只需单击“编辑筛选器”和“应用筛选器”按钮即可。 默认情况下，数据资源管理器使用 `SELECT * FROM c` 来检索集合中的所有文档，但可以将其更改为其他 [SQL 查询](documentdb-sql-query.md)（例如 `SELECT * FROM c ORDER BY c._ts DESC`），根据时间戳按升序返回所有文档。 
 
     还可以使用数据资源管理器创建存储过程、UDF 和触发器以执行服务器端业务逻辑和缩放吞吐量。 数据资源管理器公开 API 中提供的所有内置编程数据访问，但可以使用它轻松访问 Azure 门户中的数据。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 从 GitHub 克隆 DocumentDB API 应用，设置连接字符串，并运行该应用。 会看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `CD` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

快速查看应用中发生的情况。 从 \src\GetStarted 文件夹打开 `Program.java` 文件，查找创建 Azure Cosmos DB 资源的代码行。 

* 将对 `DocumentClient` 进行初始化。

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* 将创建一个新数据库。

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* 将创建一个新集合。

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* 将创建一些文档。

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* 将对 JSON 执行 SQL 查询。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 这样即可让应用与托管的数据库通信。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，并单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和 PRIMARY KEY 复制到下一步的 `Program.java` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-dotnet/keys.png)

2. 在打开的 `Program.java` 文件中，通过门户复制 URI 值（使用复制按钮），在 `Program.java` 中将其设为 DocumentClient 构造函数的终结点的值。 

    `"https://FILLME.documents.azure.com"`

4. 然后从门户复制 PRIMARY KEY 值，以粘贴方式替换“FILLME”，使之成为 DocumentClient 构造函数中的第二个参数。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 
    
## <a name="run-the-app"></a>运行应用程序

1. 在 git 终端窗口中，通过 `cd` 命令转到 azure-cosmos-db-documentdb-java-getting-started 文件夹。

2. 在 git 终端窗口键入 `mvn package`，安装所需的 Java 包。

3. 在 git 终端窗口运行 `mvn exec:java -D exec.mainClass=GetStarted.Program`，启动 Java 应用程序。

    在终端窗口中会出现通知，指出 FamilyDB 数据库已创建，按一个键即可继续。 按一个键创建数据库，然后切换到数据资源管理器，此时会看到其中包含 FamilyDB 数据库。 继续按键创建集合和文档，然后执行查询。 项目完成后，资源会从帐户中删除。 

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用数据资源管理器创建 Azure Cosmos DB 帐户、文档数据库和集合，以及如何通过运行应用以编程方式执行同一操作。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)


