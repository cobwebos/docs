---
title: 快速入门：将表 API 与 Node.js 配合使用 - Azure Cosmos DB | Microsoft Docs
description: 本快速入门介绍如何在 Azure 门户和 Node.js 中使用 Azure Cosmos DB 表 API 创建应用程序
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: arramac
ms.openlocfilehash: 0aa8f31f0c5607c82a2da36db791c29cf8769d9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>快速入门：使用 Node.js 和 Azure Cosmos DB 生成表 API 应用

本快速入门介绍如何使用 Node.js 和 Azure Cosmos DB [表 API](table-introduction.md)，通过克隆 GitHub 中的示例来生成应用。 此外，本快速入门还介绍了如何创建 Azure Cosmos DB 帐户，以及如何在基于 Web 的 Azure 门户中使用数据资源管理器创建表和实体。

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值、宽列和图形数据库，它们都受益于以 Azure Cosmos DB 为核心的全球分布和水平缩放功能。 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

此外：

* [Node.js](https://nodejs.org/en/) 版本 v0.10.29 或更高版本
* [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>创建数据库帐户

> [!IMPORTANT] 
> 必须新建表 API 帐户，才能使用正式发布的表 API SDK。 正式发布的 SDK 不支持在预览期间创建的表 API 帐户。
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>添加表

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>添加示例数据

现在可以使用数据资源管理器将数据添加到新表。

1. 在数据资源管理器中，展开 **sample-table**，单击“实体”，然后单击“添加实体”。

   ![在 Azure 门户的数据资源管理器中创建新实体](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. 现在请将数据添加到 PartitionKey 值框和 RowKey 值框，然后单击“添加实体”。

   ![为新实体设置分区键和行键](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    现在可以在数据资源管理器中将更多实体添加到表、编辑实体或查询数据。 使用数据资源管理器还可以缩放吞吐量，并将存储过程、用户定义的函数和触发器添加到表中。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆表应用、设置连接字符串，并运行。 会看到以编程方式处理数据是多么容易。 

1. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为相应的示例应用程序安装文件夹。 

    ```bash
    cd "C:\git-samples"
    ```

2. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 这样，应用程序就可以与托管的数据库进行通信。 

1. 在 [Azure 门户](http://portal.azure.com/)中，单击“连接字符串”。 

    ![在“连接字符串”窗格中查看并复制所需的连接字符串信息](./media/create-table-nodejs/connection-string.png)

2. 使用右侧的复制按钮复制主连接字符串。

3. 打开 app.config 文件，然后将值粘贴到第三行上的 connectionString 中。 

    > [!IMPORTANT]
    > 如果终结点使用 documents.azure.com，表示已有预览帐户。必须[新建表 API 帐户](#create-a-database-account)，才能使用正式版表 API SDK。
    >

3. 保存 app.config 文件。

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

## <a name="run-the-app"></a>运行应用程序

1. 在 git 终端窗口中，运行 `cd` 切换到 storage-table-java-getting-started 文件夹。

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. 运行以下命令在本地安装 azure、[node-uuid]、[nconf] 和 [async] 模块，并为它们在 package.json文件中保存一个条目

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. 在 git 终端窗口中，运行以下命令来启动节点应用程序。

    ```
    node ./tableSample.js 
    ```

    控制台窗口显示，正在将表数据添加到 Azure Cosmos DB 中的新表数据库。

    现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建表和运行应用。  现在可以使用表 API 进行数据查询了。  

> [!div class="nextstepaction"]
> [将表数据导入表 API](table-import.md)
