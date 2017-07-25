---
title: "使用图形 API 生成 Azure Cosmos DB Node.js 应用程序 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure Cosmos DB 并进行查询的 Node.js 代码示例"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 153b4cc668fdebd28cec5f3d95093a595064202a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB：使用图形 API 生成 Node.js 应用程序

Azure Cosmos DB 是 Microsoft 全球分布的多模型数据库服务。 可快速创建和查询文档、键/值，及图形数据库，它们均受益于 Azure Cosmos DB 最核心的全球分布和水平缩放功能。 

本快速入门文章演示如何使用 Azure 门户创建用于图形 API（预览版）的 Azure Cosmos DB 帐户、数据库和图形。 然后使用开放源 [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) 驱动程序生成并运行控制台应用。  

> [!NOTE]
> npm 模块 `gremlin-secure` 是 `gremlin` 模块的修改版，支持使用连接 Azure Cosmos DB 时必需的 SSL 和 SASL。 [GitHub](https://github.com/CosmosDB/gremlin-javascript) 上提供了源代码。
>

## <a name="prerequisites"></a>先决条件

在运行此示例之前，必须具备以下先决条件：
* [Node.js](https://nodejs.org/en/) 版本 v0.10.29 或更高版本
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>添加图形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在从 GitHub 克隆图形 API 应用，设置连接字符串，并运行应用。 你将看到以编程方式处理数据是多么容易。 

1. 打开 Git 终端窗口（例如 Git Bash）并更改（通过 `cd` 命令）为工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 在 Visual Studio 中打开解决方案文件。 

## <a name="review-the-code"></a>查看代码

快速查看应用中发生的情况。 打开 `app.js` 文件，会发现以下代码行。 

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

1. 打开 config.js 文件。 

2. 在 config.js 中，使用 Azure 门户的“概述”页中的“Gremlin URI”值填写 config.endpoint 密钥。 

    `config.endpoint = "GRAPHENDPOINT";`

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-graph-nodejs/gremlin-uri.png)

   如果“Gremlin URI”值为空，可以通过门户中的“密钥”页生成该值：使用“URI”值，删除 https:// 并将 documents 更改为 graphs 即可。

   Gremlin 终结点只能是没有协议/端口号的主机名，例如 `mygraphdb.graphs.azure.com`（不能是 `https://mygraphdb.graphs.azure.com` 或 `mygraphdb.graphs.azure.com:433`）。

3. 在 config.js 中，使用 Azure 门户的“密钥”页中的“主密钥”值填写 config.primaryKey 值。 

    `config.primaryKey = "PRIMARYKEY";`

   ![Azure 门户“密钥”边栏选项卡](./media/create-graph-nodejs/keys.png)

4. 输入数据库名称和图形（容器）名称作为 config.database 和 config.collection 的值。 

下面举例说明了完成的 config.js 文件是什么样子：

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>运行控制台应用

1. 打开终端窗口，更改（通过 `cd` 命令）为项目中包括的 package.json 文件的安装目录。  

2. 运行 `npm install`，安装所需的 npm 模块，包括 `gremlin-secure`。

3. 在终端中运行 `node app.js`，启动 node 应用程序。

## <a name="browse-with-data-explorer"></a>使用数据资源管理器浏览

现在可以返回到 Azure 门户中的数据资源管理器，查看、查询、修改以及使用新的图形数据。

在数据资源管理器中，新数据库将显示在“集合”窗格中。 依次展开 **graphdb**、**graphcoll**，然后单击“图形”。

示例应用生成的数据将显示在“图形”窗格中。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>清理资源

如果不打算继续使用此应用，可通过执行以下操作删除本文中创建的所有资源： 

1. 在 Azure 门户的左侧导航菜单中，单击“资源组”，然后单击创建的资源的名称。 
2. 在资源组页上单击“删除”，键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

通过本文，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建图形和运行应用。 现可使用 Gremlin 生成更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)

