---
title: 使用 Mongoose 将 Angular 应用连接到 Azure Cosmos DB 的 API for MongoDB
description: 本教程介绍如何使用 Angular 和 Express 管理 Cosmos DB 中存储的数据，借以生成 Node.js 应用程序。 在本部分中，你使用 Mongoose 连接到 Azure Cosmos DB。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: fb0e08c9d85f4472a2fa13001e71edd12149d430
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858733"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>使用 Azure Cosmos DB 的 MongoDB API 创建 Angular 应用 - 使用 Mongoose 连接到 Cosmos DB

本教程包含多个部分，演示了如何通过 Express 和 Angular 创建 Node.js 应用，并将其连接到[使用 Cosmos DB 用于 MongoDB 的 API 配置的 Cosmos 帐户](mongodb-introduction.md)。 本文以[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)为基础，介绍该教程的第 5 部分。

本教程的此部分介绍如何：

> [!div class="checklist"]
> * 使用 Mongoose 连接到 Cosmos DB。
> * 获取 Cosmos DB 连接字符串。
> * 创建 Hero 模型。
> * 创建 Hero 服务，以获取 Hero 数据。
> * 在本地运行应用。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* 开始教程的本部分之前，请完成[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中的步骤。

* 本部分教程要求在本地运行 Azure CLI。 必须安装 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="use-mongoose-to-connect"></a>使用 Mongoose 连接

Mongoose 是 MongoDB 和 Node.js 的对象数据建模 (ODM) 库。 可以使用 Mongoose 连接到 Azure Cosmos DB 帐户。 使用以下步骤安装 Mongoose 并连接到 Azure Cosmos DB：

1. 安装 mongoose npm 模块，此模块为 API，用于与 MongoDB 通信。

    ```bash
    npm i mongoose --save
    ```

1. 在“服务器”文件夹中，创建名为“mongo.js”的文件   。 需将 Azure Cosmos DB 帐户的连接详细信息添加到此文件。

1. 将以下代码复制到“mongo.js”文件中  。 此代码提供以下功能：

   * 需要 Mongoose。
   * 替代 Mongo 约定，使用内置到 ES6/ES2015 及更高版本中的基本约定。
   * 针对 env 文件进行调用，以便根据自己是处在过渡期、生产期还是开发期来设置某些项目。 将在下一部分创建该文件。
   * 包括 MongoDB 连接字符串（在 env 文件中设置）。
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
    
1. 在资源管理器窗格的“服务器”下，创建名为“环境”的文件夹   。 在“环境”文件夹中，创建名为“environment.js”的文件   。

1. 我们需要在 mongo.js 文件中包含 `dbName`、`key` 和 `cosmosPort` 这三个参数的值。 将以下代码复制到“environment.js”文件中  ：

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

要将应用程序连接到 Azure Cosmos DB，需要更新应用程序的配置设置。 使用以下步骤更新设置： 

1. 在 Azure 门户中，获取 Azure Cosmos DB 帐户的端口号、Azure Cosmos DB 帐户名和主密钥值。

1. 在“environment.js”文件中，将 `port` 的值更改为 10255  。 

    ```javascript
    const port = 10255;
    ```

1. 在“environment.js”文件中，将 `accountName` 的值更改为在本教程[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中创建的 Azure Cosmos DB 帐户名  。 

1. 在 Terminal 窗口中使用以下 CLI 命令，检索 Azure Cosmos DB 帐户的主密钥： 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    你在本教程[第 4 部分](tutorial-develop-mongodb-nodejs-part4.md)中创建的 Azure Cosmos DB 帐户名为 \<cosmosdb-name>。

1. 将主密钥作为 `key` 值复制到“environment.js”文件中  。

现在，应用程序拥有了连接到 Azure Cosmos DB 所需的所有必要信息。 

## <a name="create-a-hero-model"></a>创建 Hero 模型

接下来，你需要通过定义模型文件来定义要存储在 Azure Cosmos DB 中的数据的架构。 使用以下步骤创建一个定义数据架构的 Hero 模型  ：

1. 在“资源管理器”窗格的“服务器”文件夹下，创建一个名为“hero.model.js”的文件   。

1. 将以下代码复制到“hero.model.js”文件中  。 此代码提供以下功能：

   * 需要 Mongoose。
   * 使用 ID、name 和 saying 创建新架构。
   * 通过使用该架构创建模型。
   * 导出模型。 
   * 将集合命名为“Heroes”而不是“Heros”（根据 Mongoose 复数形式命名规则，后者为集合的默认名称）   。

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

创建 hero 模型后，需要定义一个服务，该服务用于读取数据，执行列表以及创建、删除和更新操作。 使用以下步骤创建查询 Azure Cosmos DB 中数据的 Hero 服务  ：

1. 在“资源管理器”窗格的“服务器”文件夹下，创建名为“hero.service.js”的文件   。

1. 将以下代码复制到“hero.service.js”文件中  。 此代码提供以下功能：

   * 获取创建的模型。
   * 连接到数据库。
   * 创建 `docquery` 变量，它使用 `hero.find` 方法定义一个返回所有 hero 的查询。
   * 通过 `docquery.exec` 函数运行查询，该函数约定响应状态为 200 时获取所有 hero 的列表。 
   * 如果状态为 500，则发回错误消息。
   * 因为使用的是模块，所以可以获取 hero。 

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

接下来，你需要设置路由，用于处理获取、创建、读取和删除请求的 URL。 路由方法指定回叫函数（又称“处理程序函数”）  。 当应用程序收到对指定终结点和 HTTP 方法的请求时，将调用这些函数。 使用以下步骤添加 Hero 服务并定义路由：

1. 在 Visual Studio Code 中，注释掉“routes.js”文件中用于发送示例 hero 数据的 `res.send` 函数  。 添加一行代码，改为调用 `heroService.getHeroes` 函数。

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. 在“routes.js”文件中，`require` hero 服务  ：

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. 在“hero.service.js”文件中更新使用 `req` 和 `res` 参数的 `getHeroes` 函数，如下所示  ：

    ```javascript
    function getHeroes(req, res) {
    ```

请花点时间查看并演练前面的代码。 首先，我们进入设置节点服务器的 index.js 文件。 请注意，它会设置并定义你的路由。 然后，routes.js 文件会与 hero 服务通信，要求该服务获取“getHeroes”之类的函数，并传递请求和响应  。 hero.service.js 文件获取模型并连接到 Mongo。 然后，它在我们调用它时执行“getHeroes”函数，并返回响应 200  。 

## <a name="run-the-app"></a>运行应用

接下来，按照以下步骤运行应用：

1. 在 Visual Studio Code 中，保存所有更改。 在左侧，选择“调试”按钮 ![Visual Studio Code 中的“调试”图标](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)，然后选择“开始调试”按钮 ![Visual Studio Code 中的“开始调试”图标](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)   。

1. 现在切换到浏览器。 打开“开发人员工具”和“网络”选项卡   。转到 `http://localhost:3000`，可在其中看到我们的应用程序。

    ![Azure 门户中的新 Azure Cosmos DB 帐户](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

应用中尚未存储任何 hero。 在本教程的下一部分中，我们将添加 put、push 和 delete 函数。 然后，可以使用与 Azure Cosmos 数据库的 Mongoose 连接，通过 UI 添加、更新和删除 hero。 

## <a name="clean-up-resources"></a>清理资源

不再需要资源时，可将资源组、Azure Cosmos DB 帐户和所有相关的资源删除。 通过以下步骤删除资源组：

 1. 转到创建 Azure Cosmos DB 帐户的资源组。
 1. 选择“删除资源组”  。
 1. 确认要删除的资源组的名称，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

继续了解本教程的第 6 部分，向应用添加 Post、Put 和 Delete 函数：

> [!div class="nextstepaction"]
> [第 6 部分：向应用添加 Post、Put 和 Delete 函数](tutorial-develop-mongodb-nodejs-part6.md)
