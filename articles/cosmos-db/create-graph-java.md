---
title: "使用图形 API 生成 Azure Cosmos DB Java 应用程序 | Microsoft Docs"
description: "演示一个可以用来连接到 Azure Cosmos DB 并使用 Gremlin 查询其中图形数据的 Java 代码示例。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: e02dfacd7f67c6c935243a916140c8b29fb14f9d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB：使用图形 API 生成 Java 应用程序

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建用于图形 API（预览版）的 Azure Cosmos DB 帐户、数据库和图形。 然后使用 OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 驱动程序生成并运行控制台应用。  

## <a name="prerequisites"></a>先决条件

* 在运行此示例之前，必须具备以下先决条件：
   * JDK 1.7+（如果没有 JDK，请运行 `apt-get install default-jdk`），并设置环境变量（例如 `JAVA_HOME`）
   * Maven（如果没有 Maven，请运行 `apt-get install maven`）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>添加图形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆图形 API（预览版）应用、设置连接字符串，并运行。 你将看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 `Program.java` 文件，会发现以下几行代码。 

* 将通过先前设置的 `src/remote-secure.yaml` 中的配置初始化 Gremlin `Client`。

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* 将使用 `client.submit` 方法执行一系列 Gremlin 步骤。

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，然后单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 `Program.java` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-graph-java/keys.png)

2. 打开 `src/remote-secure.yaml` 文件。 

3. 填写 `src/remote-secure.yaml` 文件中的 *host*、*port*、*username*、*password*、*connectionPool* 和 *serializer* 配置：

    设置|建议的值|说明
    ---|---|---
    主机|***.graphs.azure.com|可从 Azure 门户中检索的图形服务 URI
    端口|443|设置为 443
    用户名|*你的用户名*|格式为 `/dbs/<db>/colls/<coll>` 的资源。
    密码|*主密钥*|Azure Cosmos DB 的主密钥
    ConnectionPool|{enableSsl: true}|SSL 的连接池设置
    序列化程序|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|设置为此值

## <a name="run-the-console-app"></a>运行控制台应用

1. 在终端中运行 `mvn package`，安装所需的 npm 模块

2. 在终端中运行 `mvn exec:java -D exec.mainClass=GetStarted.Program`，启动 Java 应用程序。

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="browse-using-the-data-explorer"></a>使用数据资源管理器浏览

现在可以返回到 Azure 门户中的数据资源管理器，浏览和查询新的图形数据。

* 在数据资源管理器中，新数据库将显示在“集合”窗格中。 依次展开 **graphdb**、**graphcoll**，然后单击“图形”。

    示例应用生成的数据将显示在“图形”窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下： 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建图形和运行应用。 现可使用 Gremlin 构建更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)


