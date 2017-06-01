---
title: "Azure CosmosDB：使用 .NET Core 和 DocumentDB API 生成 Web 应用 | Microsoft Docs"
description: "演示一个可以用来连接到 Azure Cosmos DB DocumentDB API 并进行查询的 .NET core 代码示例"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 9c0806a9edb52f0c05cced55edbda6b207398e3c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure Cosmos DB：使用 .NET Core 和 Azure 门户生成 DocumentDB API Web 应用

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户、文档数据库和集合。 然后，将生成并部署基于 [DocumentDB .NET Core API](documentdb-introduction.md) 构建的“待办事项列表”Web 应用，如以下屏幕截图中所示。 

![包含示例数据的“待办事项”应用](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>先决条件

如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>安装 .NET Core

按照 [.NET Core SDK](https://www.microsoft.com/net/download/core) 页上的说明安装 .NET Core。 这些 SDK 适用于 Windows、macOS 和 Linux。

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sample-data](../../includes/cosmos-db-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，我们从 github 克隆 DocumentDB API 应用、设置连接字符串，然后运行该应用。 你将看到以编程方式处理数据是多么容易。

1. 打开 git 终端窗口（例如 git bash）并使用 `CD` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 
    
## <a name="review-the-code"></a>查看代码

[!INCLUDE [cosmos-db-tutorial-review-code-dotnet](../../includes/cosmos-db-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，然后单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 web.config 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-dotnet-core/keys.png)

2. 在 Visual Studio 2017 中，打开 web.config 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 web.config 中将其设为终结点密钥的值。 

    `<add key="endpoint" value="FILLME" />`

4. 然后从门户复制“主密钥”的值，并在 web.config 中将其设为 authKey 的值。 

    `<add key="authKey" value="FILLME" />`

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

## <a name="run-the-web-app"></a>运行 Web 应用

1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目，然后单击“管理 NuGet 包”。 

2. 在 NuGet“浏览”框中，键入 *DocumentDB*。

3. 从结果中安装“Microsoft.Azure.DocumentDB”库。 这将安装 Azure Cosmos DB 包以及所有依赖项。

4. 单击 Ctrl+F5 运行应用程序。 你的应用将显示在浏览器中。 

5. 在浏览器中单击“新建”，并在“待办事项”应用中创建一些新任务。

   ![包含示例数据的“待办事项”应用](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建集合和运行 Web 应用。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)

