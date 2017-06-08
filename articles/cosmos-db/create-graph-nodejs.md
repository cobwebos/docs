---
title: "使用图形 API 生成 Azure Cosmos DB Node.js 应用程序 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure Cosmos DB 并进行查询的 Node.js 代码示例"
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
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5c996068ff5fbadda6730244c34c0d0d1f8fb447
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB：使用图形 API 生成 Node.js 应用程序

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建用于图形 API（预览版）的 Azure Cosmos DB 帐户、数据库和图形。 然后使用 OSS [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) 驱动程序生成并运行控制台应用。  

> [!NOTE]
> NPM 模块 `gremlin-secure` 是 `gremlin` 模块的修改版，支持使用 Azure Cosmos DB 进行连接时必需的 SSL 和 SASL。 [GitHub](https://github.com/CosmosDB/gremlin-javascript) 上提供了源代码。
>

## <a name="prerequisites"></a>先决条件

* 在运行此示例之前，必须具备以下先决条件：
    * [Node.js](https://nodejs.org/en/) 版本 v0.10.29 或更高版本
    * [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>添加图形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆图形 API 应用、设置连接字符串，并运行。 你将看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 

## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 `app.js` 文件，会发现以下几行代码。 

* 将创建 Gremlin 客户端。

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

配置均位于 `config.js` 中，后者可以在以下部分编辑。

* 将使用 `client.execute` 方法执行一系列 Gremlin 步骤。

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，然后单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 `app.js` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-dotnet/keys.png)

2. 从门户中复制 Gremlin URI 值（使用复制按钮），并在 config.js 中将其设为 `config.endpoint` 密钥的值。 Gremlin 终结点只能是没有协议/端口号的主机名，例如 `mygraphdb.graphs.azure.com`（不能是 `https://mygraphdb.graphs.azure.com` 或 `mygraphdb.graphs.azure.com:433`）。

    `config.endpoint = "GRAPHENDPOINT";`

3. 然后从门户复制“主密钥”的值，并在 config.js 中将其设为 config.primaryKey 的值。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

    `config.primaryKey = "PRIMARYKEY";`

4. 输入数据库名称和图形（容器）名称作为 config.database 和 config.collection 的值。 

下面举例说明了完成的 config.js 文件是什么样子：

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>运行控制台应用

1. 打开终端窗口，然后通过 `cd` 转到项目中包括的 package.json 文件的安装目录。  

2. 运行 `npm install`，安装所需的 npm 模块。 其中包括 `gremlin-secure`。

3. 在终端中运行 `node app.js`，启动 node 应用程序。

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="browse-using-the-data-explorer"></a>使用数据资源管理器浏览

现在可以返回到 Azure 门户中的数据资源管理器，浏览和查询新的图形数据。

* 在数据资源管理器中，新数据库将显示在“集合”窗格中。 依次展开 **graphdb**、**graphcoll**，然后单击“图形”。

    示例应用生成的数据将显示在“图形”窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下： 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。**** 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建图形和运行应用。 现可使用 Gremlin 构建更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)
