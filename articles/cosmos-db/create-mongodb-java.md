---
title: "Azure Cosmos DB：使用 Java 和 MongoDB API 生成控制台应用 | Microsoft Docs"
description: "演示一个可以用来连接到 Azure Cosmos DB MongoDB API 并进行查询的 Java 代码示例"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 1683afd842294b3b45ae4d0e53bbecdccadc1ed5
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB：使用 Java 和 Azure 门户生成 MongoDB API 控制台应用

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户、文档数据库和集合。 然后生成并部署基于 [MongoDB Java 驱动程序](https://docs.mongodb.com/ecosystem/drivers/java/)构建的控制台应用。 

## <a name="prerequisites"></a>先决条件

* 在运行此示例之前，必须具备以下先决条件：
   * JDK 1.7+（如果没有 JDK，请运行 `apt-get install default-jdk`）
   * Maven（如果没有 Maven，请运行 `apt-get install maven`）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>添加集合

将新数据库命名为 **db**，将新集合命名为 **coll**。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆 MongoDB API 应用、设置连接字符串，并运行。 你将看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 

## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 `Program.cs` 文件，会发现以下代码行创建 Azure Cosmos DB 资源。 

* 将对 DocumentClient 进行初始化。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 将创建新数据库和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 将使用 `MongoCollection.insertOne` 插入一些文档

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 将使用 `MongoCollection.find` 执行一些查询

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());        
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 从帐户中选择“快速启动”，选择 Java，然后将连接字符串复制到剪贴板

2. 打开 `Program.java` 文件，将 MongoClientURI 构造函数的参数替换为该连接字符串。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 
    
## <a name="run-the-console-app"></a>运行控制台应用

1. 在终端中运行 `mvn package`，安装所需的 npm 模块

2. 在终端中运行 `mvn exec:java -D exec.mainClass=GetStarted.Program`，启动 Java 应用程序。

现在可以使用 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) 来查询、修改和处理此新数据。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建集合和运行控制台应用。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)



