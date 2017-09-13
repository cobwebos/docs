---
title: "Azure Cosmos DB：使用 .NET 和 DocumentDB API 生成 Web 应用 | Microsoft Docs"
description: "演示一个可以用来连接到 Azure Cosmos DB DocumentDB API 并进行查询的 .NET 代码示例"
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
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 9bb863261da64c97f99757d4a0cb3474a7755591
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB：使用 .NET 和 Azure 门户生成 DocumentDB API Web 应用

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户、文档数据库和集合。 然后，将生成并部署基于 [DocumentDB .NET API](documentdb-sdk-dotnet.md) 构建的“待办事项列表”Web 应用，如以下屏幕截图中所示。 

![包含示例数据的“待办事项”应用](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

## <a name="prerequisites"></a>先决条件

如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection"></a>
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

     现在可以在数据资源管理器中使用查询来检索数据。 默认情况下，数据资源管理器使用 `SELECT * FROM c` 来检索集合中的所有文档，但可以将其更改为其他 [SQL 查询](documentdb-sql-query.md)（例如 `SELECT * FROM c ORDER BY c._ts DESC`），根据时间戳按升序返回所有文档。
 
     还可以使用数据资源管理器创建存储过程、UDF 和触发器以执行服务器端业务逻辑和缩放吞吐量。 数据资源管理器公开 API 中提供的所有内置编程数据访问，但可以使用它轻松访问 Azure 门户中的数据。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 从 GitHub 克隆 DocumentDB API 应用，设置连接字符串，并运行该应用。 会看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `CD` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. 然后在 Visual Studio 中打开 todo 解决方案文件。 

## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 DocumentDBRepository.cs 文件，会发现以下代码行创建 Azure Cosmos DB 资源。 

* 将在第 73 行对 DocumentClient 进行初始化。

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* 将在第 88 行创建一个新数据库。

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 将在第 107 行创建一个新集合。

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，并单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 web.config 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-dotnet/keys.png)

2. 在 Visual Studio 2017 中，打开 web.config 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 web.config 中将其设为终结点密钥的值。 

    `<add key="endpoint" value="FILLME" />`

4. 然后从门户复制 PRIMARY KEY 值，并在 web.config 中将其设为 authKey 的值。现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

    `<add key="authKey" value="FILLME" />`
    
## <a name="run-the-web-app"></a>运行 Web 应用
1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目，并单击“管理 NuGet 包”。 

2. 在 NuGet“浏览”框中，键入 *DocumentDB*。

3. 从结果中安装“Microsoft.Azure.DocumentDB”库。 这会安装 Microsoft.Azure.DocumentDB 包以及所有依赖项。

4. 单击 Ctrl+F5 运行应用程序。 应用会显示在浏览器中。 

5. 在浏览器中单击“新建”，并在“待办事项”应用中创建一些新任务。

   ![包含示例数据的“待办事项”应用](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建集合和运行 Web 应用。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)



