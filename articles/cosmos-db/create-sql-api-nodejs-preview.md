---
title: Azure Cosmos DB：使用 Node.js 和 SQL API 生成应用 | Microsoft Docs
description: 演示一个可以用来连接到 Azure Cosmos DB SQL API 并进行查询的 Node.js 代码示例
services: cosmos-db
author: deborahc
manager: andrl
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: 23b7c5d332bd5fb75936c95ab9e38d17e1573999
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39602354"
---
# <a name="azure-cosmos-db-build-a-nodejs-app-using-sql-api-javascript-sdk-20-preview-and-the-azure-portal"></a>Azure Cosmos DB：使用 SQL API、JavaScript SDK 2.0（预览版）和 Azure 门户生成 Node.js 应用 

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门演示如何使用 Azure 门户创建 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帐户、文档数据库和容器， 然后生成并运行基于 [SQL JavaScript SDK](sql-api-sdk-node.md) 的控制台应用。 本快速入门使用目前为预览版的 2.0 版 [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos)。 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 此外：
    * [Node.js](https://nodejs.org/en/) v6.0.0 或更高版本
    * [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，请克隆 GitHub 中的 SQL API 应用，设置连接字符串，然后运行该应用。 你会看到以编程方式处理数据是多么容易。 

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

注意，如果你熟悉旧版 JavaScript SDK，则可能习惯于看到术语“集合”和“文档”。 由于 Azure Cosmos DB 支持[多 API 模型](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities)，因此 2.0+ 版的 JavaScript SDK 使用通用术语“容器”（可能为集合、图形或表），并使用“项”来描述容器的内容。

以下代码片段全部摘自 **app.js** 文件。

* 将对 `CosmosClient` 进行初始化。

    ```nodejs
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* 将创建一个新数据库。

    ```nodejs
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* 将创建新容器（集合）。

    ```nodejs
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* 将创建新项（文档）。

    ```nodejs
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* 将对 JSON 执行 SQL 查询。

    ```nodejs
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，并单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 `config.js` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-sql-api-dotnet/keys.png)

2. 打开 `config.js` 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 `config.js` 中将其设为终结点密钥的值。 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. 然后从门户复制“主密钥”的值，并在 `config.js` 中将其设为 `config.primaryKey` 的值。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>运行应用
1. 在终端中运行 `npm install`，安装所需的 npm 模块

2. 在终端中运行 `node app.js`，启动 node 应用程序。

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建集合和运行应用。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)


