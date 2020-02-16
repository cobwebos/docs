---
title: 快速入门：使用 Node.js 查询 Azure Cosmos DB SQL API 帐户
description: 如何使用 Node.js 创建连接 Azure Cosmos DB SQL API 帐户和查询数据的应用。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134460"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>快速入门：使用 Node.js 连接和查询 Azure Cosmos DB SQL API 帐户中的数据

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入门中，你将通过 Azure 门户并使用从 GitHub 克隆的 Node.js 应用来创建和管理 Azure Cosmos DB SQL API 帐户。 Azure Cosmos DB 是一种多模型数据库服务，它通过全局分布和水平缩放功能让你快速创建和查询文档、表、键/值和图形数据库。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。 你还可以使用 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)以及 URI `https://localhost:8081` 和密钥 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Node.js 6.0.0+](https://nodejs.org/)。
- [Git](https://www.git-scm.com/downloads)。

## <a name="create-a-database"></a>创建数据库 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>添加容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 GitHub 克隆 Node.js 应用，设置连接字符串并运行。

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

此步骤是可选的。 如果有意了解如何使用代码创建 Azure Cosmos 数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

如果你熟悉旧版 SQL JavaScript SDK，则可能习惯于看到术语“集合”和“文档”。   由于 Azure Cosmos DB 支持[多 API 模型](introduction.md)，因此 [2.0+ 版的 JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) 使用通用术语“容器”  （可能为集合、图形或表），并使用“项”  来描述容器的内容。

以下代码片段全部摘自 *app.js* 文件。

* `CosmosClient` 对象已初始化。

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* 新建 Azure Cosmos 数据库。

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* 在数据库中新建容器（集合）。

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* 将创建新项（文档）。

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* 在系列数据库中对 JSON 执行 SQL 查询。 查询返回 Anderson 系列的所有子级。 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>更新连接字符串

现在，返回 Azure 门户，获取 Azure Cosmos 帐户的连接字符串详细信息。 将连接字符串复制到应用，以便其连接数据库。

1. 在 [Azure 门户](https://portal.azure.com/)中，在你的 Azure Cosmos DB 帐户中，从左侧导航栏中选择“密钥”  ，然后选择“读写密钥”  。 在下一步骤中，你将使用屏幕右侧的复制按钮将 URI 和主密钥复制到 *config.js* 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-sql-api-dotnet/keys.png)

2. 打开 *config.js* 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 *config.js* 中将其设为终结点密钥的值。 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. 然后从门户复制“主密钥”的值，并在 *config.js* 中将其设为 `config.key` 的值。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>运行应用

1. 在终端中运行 `npm install`，安装所需的 npm 模块

2. 在终端中运行 `node app.js`，启动 node 应用程序。

现可返回数据资源管理器，然后修改和处理这些新数据。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解了如何创建 Azure Cosmos DB 帐户、如何使用数据资源管理器创建容器，以及如何运行 Node.js 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)


