---
title: "使用图形 API 生成 Azure Cosmos DB .NET 应用程序 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure Cosmos DB 并进行查询的 .NET 代码示例"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 07a13c3e9e2baefe0be7ed417ba105dd23a3708d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB：使用图形 API 生成 .NET 应用程序

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户、数据库和图形（容器）。 然后将生成并运行基于[图形 API](graph-sdk-dotnet.md)（预览版）构建的控制台应用。  

## <a name="prerequisites"></a>先决条件

如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>添加图形

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="add-sample-data"></a>添加示例数据

现在可以使用数据资源管理器将数据添加到图形。

1. 在数据资源管理器中，依次展开 **sample-database**、**sample-graph**，单击“图形”，然后单击“新建顶点”和“新建边缘”向图形中添加项目。 使用数据资源管理器还可以缩放吞吐量，并将存储过程、用户定义的函数和触发器添加到容器中。

    ![将顶点和边缘添加到数据资源管理器的图形中](./media/create-graph-dotnet/azure-cosmos-db-graph-sample-data.png)

2. 添加一些项目后，单击“应用筛选器”按钮，或者右键单击“图形”，然后单击“新建图形查询”，查看数据的可视化图。 可以通过单击“样式”按钮并更改设置来更改数据的标签和样式。 下面是数据资源管理器中的示例图，显示的标签、颜色和数据全都可以修改。

    ![Azure 门户的数据资源管理器中的可视化图形资源管理器](./media/create-graph-dotnet/azure-cosmos-db-graph-explorer.png)

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆图形 API 应用、设置连接字符串，并运行。 你将看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 

## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 Program.cs 文件，会发现以下代码行创建 Azure Cosmos DB 资源。 

* 将对 DocumentClient 进行初始化。 在预览版中，我们已将图形扩展 API 添加到 DocumentDB 客户端。 我们正在努力开发从 DocumentDB 客户端和资源中解耦的独立图形客户端。

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* 将创建一个新数据库。

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* 将创建一个新图形。

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* 将使用 `CreateGremlinQuery` 方法执行一系列 Gremlin 步骤。

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，然后单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 `App.config` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-graph-dotnet/keys.png)

2. 在 Visual Studio 2017 中，打开 `App.config` 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 `App.config` 中将其设为终结点密钥的值。 

    `<add key="Endpoint" value="FILLME.documents.azure.com:443" />`

4. 然后从门户复制“主密钥”的值，并在 `App.config` 中将其设为 authKey 的值。 

    `<add key="AuthKey" value="FILLME" />`

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

## <a name="run-the-console-app"></a>运行控制台应用

1. 在 Visual Studio 中，右键单击**解决方案资源管理器**中的 **GraphGetStarted** 项目，然后单击“管理 NuGet 包”。 

2. 在 NuGet“浏览”框中，键入 *Microsoft.Azure.Graphs*，并选中“包括预发行版”框。 

3. 从结果中安装“Microsoft.Azure.Graphs”库。 这将安装 Azure Cosmos DB 图形扩展库包和所有依赖项。

4. 单击 Ctrl+F5 运行应用程序。

   控制台窗口将显示所添加到图形的顶点及边缘。 完成脚本后，按两次 ENTER 以关闭控制台窗口。 

## <a name="browse-using-the-data-explorer"></a>使用数据资源管理器浏览

现在可以返回到 Azure 门户中的数据资源管理器，浏览和查询新的图形数据。

* 在数据资源管理器中，新数据库将显示在“集合”窗格中。 依次展开 **graphdb**、**graphcoll**，然后单击“图形”。

    示例应用生成的数据将显示在“图形”窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下： 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建图形和运行应用。 现可使用 Gremlin 构建更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)


