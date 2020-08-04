---
title: 使用 Mongoose 将 Angular 应用连接到 Azure Cosmos DB 的 API for MongoDB
description: 本教程介绍如何使用 Angular 和 Express 管理 Cosmos DB 中存储的数据，以生成 Node.js 应用程序。 在本部分，你将使用 Mongoose 连接到 Azure Cosmos DB。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: f77c20faa87adf28a2dda43412a59867309c7cf8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87066921"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>使用 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Angular 应用 - 使用 Mongoose 连接到 Cosmos DB

本教程包含多个部分，演示了如何通过 Express 和 Angular 创建 Node.js 应用，然后将其连接到[使用 Cosmos DB 的用于 MongoDB 的 API 配置的 Cosmos 帐户](mongodb-introduction.md)。 本文是教程的第 5 部分，内容基于[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)。

本教程部分介绍以下操作：

> [!div class="checklist"]
> * 使用 Mongoose 连接到 Cosmos DB。
> * 获取 Cosmos DB 连接字符串。
> * 创建 Hero 模型。
> * 创建 Hero 服务用于获取 Hero 数据。
> * 在本地运行应用。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* 开始本教程之前，请先完成[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中的步骤。

* 本教程要求在本地运行 Azure CLI。 必须安装 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="use-mongoose-to-connect"></a>使用 Mongoose 进行连接

Mongoose 是适用于 MongoDB 和 Node.js 的对象数据建模 (ODM) 库。 可以使用 Mongoose 连接到 Azure Cosmos DB 帐户。 使用以下步骤安装 Mongoose 并连接到 Azure Cosmos DB：

1. 安装 Mongoose NPM 模块 - 用来与 MongoDB 通信的 API。

    ```bash
    npm i mongoose --save
    ```

1. 在 **server** 文件夹中，创建名为 **mongo.js** 的文件。 将 Azure Cosmos DB 帐户的连接详细信息添加到此文件。

1. 将以下代码复制到 **mongo.js** 文件中。 该代码提供以下功能：

   * 需要 Mongoose。
   * 替代 Mongo 约定，以使用 ES6/ES2015 和更高版本中内置的基本约定。
   * 根据是在过渡、生产还是开发环境中操作，针对 env 文件发出调用以设置某些项目。 将在下一部分创建该文件。
   * 请包含 env 文件中设置的 MongoDB 连接字符串。
   * 创建调用 Mongoose 的 connect 函数。

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. 在“资源管理器”窗格中的“server”下，创建名为 **environment** 的文件夹。 在 **environment** 文件夹中创建名为 **environment.js** 的文件。

1. 在 mongo.js 文件中，需要包含 `dbName`、`key` 和 `cosmosPort` 参数的值。 将以下代码复制到 **environment.js** 文件中：

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>获取连接字符串

若要将应用程序连接到 Azure Cosmos DB，需要更新应用程序的配置设置。 使用以下步骤更新设置： 

1. 在 Azure 门户中，获取端口号、Azure Cosmos DB 帐户名，以及 Azure Cosmos DB 帐户的主密钥值。

1. 在 **environment.js** 文件中，将 `port` 的值更改为 10255。 

    ```javascript
    const port = 10255;
    ```

1. 在 **environment.js** 文件中，将 `accountName` 的值更改为在本教程[步骤 4](tutorial-develop-mongodb-nodejs-part4.md) 中创建的 Azure Cosmos DB 帐户的名称。 

1. 在 Terminal 窗口中使用以下 CLI 命令，检索 Azure Cosmos DB 帐户的主密钥： 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    你在本教程[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中创建的 Azure Cosmos DB 帐户名为 \<cosmosdb-name>。

1. 将主密钥作为 `key` 值复制到“environment.js”文件中。

现在，应用程序拥有了连接到 Azure Cosmos DB 所需的所有必要信息。 

## <a name="create-a-hero-model"></a>创建 Hero 模型

接下来，你需要通过定义模型文件来定义要存储在 Azure Cosmos DB 中的数据的架构。 使用以下步骤创建一个定义数据架构的 Hero 模型：

1. 在“资源管理器”窗格的“服务器”文件夹下，创建一个名为“hero.model.js”的文件 。

1. 将以下代码复制到“hero.model.js”文件中。 该代码提供以下功能：

   * 需要 Mongoose。
   * 使用 ID、name 和 saying 创建新架构。
   * 通过使用该架构创建模型。
   * 导出模型。 
   * 将集合命名为“Heroes”而不是“Heros”（根据 Mongoose 复数形式命名规则，后者为集合的默认名称） 。

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>创建 Hero 服务

创建 Hero 模型后，需要定义一个服务用于读取数据，以及执行 list、create、delete 和 update 操作。 使用以下步骤创建一个 Hero 服务用于查询 Azure Cosmos DB 中的数据：

1. 在“资源管理器”窗格中的“server”文件夹下，创建名为 **hero.service.js** 的文件。

1. 将以下代码复制到 **hero.service.js** 文件中。 该代码提供以下功能：

   * 获取创建的模型。
   * 连接到数据库。
   * 创建 `docquery` 变量，以便使用 `hero.find` 方法定义一个返回所有 Hero 的查询。
   * 结合 `docquery.exec` 函数运行查询。该函数中的约定可以获取响应状态为 200 的所有 Hero 的列表。 
   * 如果状态为 500，则发回错误消息。
   * 由于使用的是模块，因此可以获取 Hero。 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>配置路由

接下来，需要设置路由用于处理 get、create、read 和 delete 请求的 URL。 路由方法指定回调函数（也称为“处理程序函数”）。 当应用程序接收到发往指定终结点和 HTTP 方法的请求时，会调用这些函数。 使用以下步骤添加 Hero 服务并定义路由：

1. 在 Visual Studio Code 中的 **routes.js** 文件内，注释掉发送示例 Hero 数据的 `res.send` 函数。 添加一行代码，以改为调用 `heroService.getHeroes` 函数。

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. 在 **routes.js** 文件中，为 Hero 服务添加 `require` 语句：

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. 在 **hero.service.js** 文件中更新 `getHeroes` 函数，以采用 `req` 和 `res` 参数，如下所示：

    ```javascript
    function getHeroes(req, res) {
    ```

请花点时间查看并演练上面的代码。 首先查看 index.js 文件，此文件用于设置节点服务器。 可以看到，其中设置并定义了路由。 接下来，routes.js 文件与 Hero 服务通信，要求该服务获取 **getHeroes** 之类的函数，并传递请求和响应。 hero.service.js 文件获取模型并连接到 Mongo。 然后，此文件执行调用的 **getHeroes**，并返回响应 200。 

## <a name="run-the-app"></a>运行应用程序

接下来，请使用以下步骤运行应用：

1. 在 Visual Studio Code 中保存所有更改。 在左侧，选择“调试”按钮 :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png":::，然后选择“启动调试”按钮 :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: 。

1. 现在切换到浏览器。 打开“开发人员工具”和“网络”选项卡。 转到 `http://localhost:3000`，其中显示了我们的应用程序。

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Azure 门户中的新 Azure Cosmos DB 帐户":::

尚无 Hero 存储在该应用中。 在本教程的下一部分，我们将添加放置、推送和删除功能。 然后，可以使用与 Azure Cosmos 数据库的 Mongoose 连接，通过 UI 添加、更新和删除 hero。 

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的资源时，可以删除相应的资源组、Azure Cosmos DB 帐户和所有相关资源。 使用以下步骤删除资源组：

 1. 转到在其中创建了 Azure Cosmos DB 帐户的资源组。
 1. 选择“删除资源组”。
 1. 确认要删除的资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

请转到本教程的第 6 部分，了解如何向应用添加 Post、Put 和 Delete 函数：

> [!div class="nextstepaction"]
> [第 6 部分：向应用添加 Post、Put 和 Delete 函数](tutorial-develop-mongodb-nodejs-part6.md)
