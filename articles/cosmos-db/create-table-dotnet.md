---
title: "使用表 API 生成 Azure Cosmos DB .NET 应用程序 | Microsoft Docs"
description: "Azure Cosmos DB 的表 API（使用 .NET）入门"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/22/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 29e7eebda5177d6e852ef04ad82d9d38a8d30ed8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---
<a id="azure-cosmos-db-build-a-net-application-using-the-table-api" class="xliff"></a>

# Azure Cosmos DB：使用表 API 生成 .NET 应用程序

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户，以及如何在该帐户内创建表。 然后将编写代码来插入、更新和删除条目，以及使用 NuGet 中的新 [Microsoft Azure 存储高级表](https://aka.ms/premiumtablenuget)（预览版）包运行某些查询。 此库与 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，但还具有使用[表 API](table-introduction.md)（预览版）连接到 Azure Cosmos DB 帐户的功能。 

<a id="prerequisites" class="xliff"></a>

## 先决条件

如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## 创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

<a id="add-a-table" class="xliff"></a>

## 添加表

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

<a id="add-sample-data" class="xliff"></a>

## 添加示例数据

现在可以使用数据资源管理器（预览版）将数据添加到新表。

1. 在数据资源管理器中，展开 **sample-table**，单击“实体”，然后单击“添加实体”。

   ![在 Azure 门户的数据资源管理器中创建新实体](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. 现在请将数据添加到 PartitionKey 值框和 RowKey 值框，然后单击“添加实体”。

   ![为新实体设置分区键和行键](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    现在可以在数据资源管理器中将更多实体添加到表、编辑实体或查询数据。 使用数据资源管理器还可以缩放吞吐量，并将存储过程、用户定义的函数和触发器添加到表中。

<a id="clone-the-sample-application" class="xliff"></a>

## 克隆示例应用程序

现在让我们从 github 克隆表应用、设置连接字符串，并运行。 你将看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 

<a id="review-the-code" class="xliff"></a>

## 查看代码

让我们快速查看一下应用中发生的情况。 打开 Program.cs 文件，会发现以下代码行创建 Azure Cosmos DB 资源。 

* CloudTableClient 已初始化。

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* 如果表不存在，则创建新表。

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* 将创建一个新的表容器。 你会注意到，该代码非常类似于常规的 Azure 表存储 SDK。 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

<a id="update-your-connection-string" class="xliff"></a>

## 更新连接字符串

现在将更新连接字符串信息，使应用能够与 Azure Cosmos DB 通信。 

1. 在 Visual Studio 中，打开 app.config 文件。 

2. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 左侧导航菜单中，单击“连接字符串”。 然后在新窗格中单击连接字符串的复制按钮。 

    ![在“连接字符串”窗格中查看和复制终结点和帐户密钥](./media/create-table-dotnet/keys.png)

3. 将值粘贴到 app.config 文件中，充当 PremiumStorageConnectionString 的值。 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    可以让 StandardStorageConnectionString 保留原样。

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

<a id="run-the-web-app" class="xliff"></a>

## 运行 Web 应用

1. 在 Visual Studio 中，右键单击**解决方案资源管理器**中的 **PremiumTableGetStarted** 项目，然后单击“管理 NuGet 包”。 

2. 在 NuGet“浏览”框中，键入 WindowsAzure.Storage-PremiumTable。

3. 选中“包括预发行版”框。 

4. 从结果中安装“WindowsAzure.Storage-PremiumTable”库。 这将安装预览版 Azure Cosmos DB 表 API 包以及所有依赖项。 请注意，这是与 Azure 表存储所使用的 Microsoft Azure 存储安装包不相同的 NuGet 安装包。 

5. 单击 Ctrl+F5 运行应用程序。

    控制台窗口会显示在表中添加、检索、查询、替换和删除的数据。 完成脚本后，按任意键关闭控制台窗口。 
    
    ![快速入门的控制台输出](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. 如需查看数据资源管理器中的新实体，请直接注释掉 program.cs 中的第 188-208 行，不需将其删除即可再次运行示例。 

    现在可以回到数据资源管理器，单击“刷新”，展开“人员”表，再单击“实体”，然后即可使用该新数据。 

    ![数据资源管理器中的新实体](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## 在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## 清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下： 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

<a id="next-steps" class="xliff"></a>

## 后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建表和运行应用。  现在可以使用表 API 进行数据查询了。  

> [!div class="nextstepaction"]
> [使用表 API 的查询](tutorial-query-table.md)


