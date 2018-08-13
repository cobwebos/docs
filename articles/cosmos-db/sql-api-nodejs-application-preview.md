---
title: 生成适用于 Azure Cosmos DB 的 Node.js Web 应用 | Microsoft Docs
description: 此 Node.js 教程探讨了如何使用 Microsoft Azure Cosmos DB 从 Azure 网站上托管的 Node.js Express Web 应用程序来存储和访问数据。
keywords: 应用程序开发, 数据库教程, 了解 Node.js, Node.js 教程
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608964"
---
# <a name="_Toc395783175"></a>使用 Azure Cosmos DB 和 Node.js SDK（预览版）生成 Node.js Web 应用程序
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js - v2.0 预览版](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

本 Node.js 教程介绍了如何使用 Azure Cosmos DB SQL API 帐户，通过在 Azure 网站上托管的 Node.js Express 应用程序存储和访问数据。 在本教程中，需生成一个简单的基于 Web 的应用程序（待办事项应用），用于创建、检索和完成任务。 任务存储为 Azure Cosmos DB 中的 JSON 文档。 下图显示待办事项应用程序的屏幕快照：

![在本 Node.js 教程中创建的 My Todo List 应用程序的屏幕截图](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

本教程演示如何使用 Azure 门户创建 Azure Cosmos DB SQL API 帐户， 然后生成并运行一个基于 Node.js SDK 的 Web 应用，以便创建数据库和容器并向容器添加项。 本教程使用目前为预览版的 2.0 版 Node.js SDK。

也可从 [GitHub][GitHub] 获取完成的示例。 只需读取[自述](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md)文件，了解如何运行该应用。

## <a name="_Toc395783176"></a>先决条件

在按照本文中的说明操作之前，应确保已拥有下列项：

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 6.10 或更高版本。
* [Express 生成器](http://www.expressjs.com/starter/generator.html)（可以通过 `npm install express-generator -g` 安装）
* [Git][Git]。

## <a name="_Toc395637761"></a>步骤 1：创建 Azure Cosmos DB 数据库帐户
让我们首先创建一个 Azure Cosmos DB 帐户。 如果已有一个帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[步骤 2：创建新的 Node.js 应用程序](#_Toc395783178)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>步骤 2：创建新的 Node.js 应用程序
现在让我们来了解如何使用 [Express](http://expressjs.com/) 框架创建基本的 Hello World Node.js 项目。

1. 打开最喜欢的终端，如 Node.js 命令提示符。
2. 导航到要在其中存储新应用程序的目录。
3. 使用 Express 生成器生成名叫 **todo**的新应用程序。

   ```bash
   express todo
   ```
4. 打开新的 **todo** 目录并安装依赖项。

   ```bash
   cd todo
   npm install
   ```
5. 运行新应用程序。

   ```bash
   npm start
   ```

6. 在浏览器中导航到 [http://localhost:3000](http://localhost:3000) 即可查看新应用程序。
   
    ![了解 Node.js - 浏览器窗口中 Hello World 应用程序的屏幕截图](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 停止应用程序的方法是先在终端窗口中按 CTRL+C，然后单击“y”终止批处理作业。

## <a name="_Toc395783179"></a>步骤 3：安装所需的模块

**package.json** 文件是在项目的根目录中创建的文件之一。 此文件包含一系列其他模块，它们是 Node.js 应用程序必需的。 稍后，在将此应用程序部署到 Azure 网站时，使用此文件以确定需要在 Azure 上安装哪些模块来支持应用程序。 需要为本教程再安装两个包。

1. 打开终端，通过 npm 安装 **async** 模块。

   ```bash
   npm install async --save
   ```

2. 通过 npm 安装 **@azure/cosmos** 模块。 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>步骤 4：在 Node 应用程序中使用 Azure Cosmos DB 服务
完成初始安装和配置以后，下一步是编写待办事项应用程序与 Azure Cosmos DB 通信所需的代码。

### <a name="create-the-model"></a>创建模型
1. 在项目的根目录中，创建名为 **models**的新目录  

2. 在 **models** 目录中，创建一个名为 **taskDao.js** 的新文件。 此文件包含创建数据库和容器所需的代码，并定义在 Azure Cosmos DB 中读取、更新、创建和查找任务所需的方法。 

3. 将以下代码复制到 **taskDao.js** 文件中

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. 保存并关闭 **taskDao.js** 文件。  

### <a name="create-the-controller"></a>创建控制器

1. 在项目的 **routes** 目录中，创建一个名为 **tasklist.js** 的新文件。  

2. 将以下代码添加到 **tasklist.js**。 这会加载 **tasklist.js** 使用的 CosmosClient 和 async 模块， 并定义 **TaskList** 类，该类作为我们之前定义的 **TaskDao** 对象的一个实例来传递：
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. 保存并关闭 **tasklist.js** 文件。

### <a name="add-configjs"></a>添加 config.js

1. 在项目根目录中创建一个名为 **config.js** 的新文件。 

2. 将以下代码添加到 **config.js** 文件。 此代码会定义应用程序所需的配置设置和值。
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. 在 **config.js** 文件中，使用 [Microsoft Azure 门户](https://portal.azure.com)中 Azure Cosmos DB 帐户的“密钥”页中的值更新 HOST 和 AUTH_KEY 的值。 

4. 保存并关闭 **config.js** 文件。

### <a name="modify-appjs"></a>修改 app.js
1. 在项目目录中，打开 **app.js** 文件。 此文件早于 Express Web 应用程序创建。  

2. 将以下代码添加到 **app.js** 文件。 此代码定义要使用的配置文件，并继续将该文件中的值读取到我们不久要使用的某些变量中。 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. 最后，保存并关闭 **app.js** 文件，我们就快完成了。

## <a name="_Toc395783181"></a>步骤 5：生成用户界面
现在让我们把注意力转向构建用户界面，因此用户可以与我们的应用程序进行切实的交互。 我们创建的 Express 应用程序使用 **Jade** 作为视图引擎。 有关详细信息，请参阅 [http://jade-lang.com/](http://jade-lang.com/)。

1. **views** 目录中的 **layout.jade** 文件用作其他 **.jade** 文件的全局模板。 在此步骤中，将对其进行修改以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)（一个可以轻松设计美观网站的工具包）。  

2. 打开 **views** 文件夹中的 **layout.jade** 文件，将内容替换为以下代码：

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    这会有效告知 **Jade** 引擎来为应用程序呈现某些 HTML，并创建名为 **content** 的**块**，我们可在其中提供内容页面的布局。

    保存并关闭此 **layout.jade** 文件。

3. 现在打开 **index.jade** 文件（应用程序将要使用的视图），并将文件内容替换为以下代码：

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

这会扩展布局，并为我们先前在 **layout.jade** 文件中看到的 **content** 占位符提供内容。
   
在此布局中，我们创建了两个 HTML 窗体。

第一个窗体中的表包含我们的数据和按钮，该按钮允许我们通过将内容发布到控制器的 **/completeTask** 方法来更新项。
    
第二个窗体包含两个输入字段和一个按钮，该按钮允许我们通过发布控制器的 **/addtask** 方法来新建项。

这应该是应用程序工作所需的所有内容了。

## <a name="_Toc395783181"></a>步骤 6：在本地运行应用程序
1. 要在本地计算机上测试应用程序，请在终端中运行 `npm start` 以启动应用程序，并刷新 [http://localhost:3000](http://localhost:3000) 浏览页。 该页此时看起来应如下图所示：
   
    ![浏览器窗口中 MyTodo List 应用程序的屏幕截图](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > 如果收到有关 layout.jade 文件或 index.jade 文件的缩进错误，请确保这两个文件中的头两行都已经左对齐，没有空格。 如果头两行之前留有空格，请删除这些空格，将这两个文件保存，然后刷新浏览器窗口。 

2. 使用“项”、“项名”和“类别”字段输入新任务，并单击“添加项”。 这会在 Azure Cosmos DB 中创建具有这些属性的文档。 
3. 页面应更新为在 ToDo 列表中显示新建项。
   
    ![ToDo 列表中具有新的项的应用程序屏幕截图](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. 要完成任务，只需选中“完成”列中的复选框，并单击“更新任务” 。 这将更新已创建的文档并将其从视图中删除。

5. 若要停止应用程序，可在终端窗口中按 CTRL+C 并单击“Y”终止批处理作业。

## <a name="_Toc395783182"></a>步骤 7：将应用程序开发项目部署到 Azure 网站
1. 如果尚未部署，则启用 Azure 网站的 git 存储库。 可以在 [Local Git Deployment to Azure 应用服务](../app-service/app-service-deploy-local-git.md) （从本地 GIT 部署到 Azure 应用服务）主题中找到如何执行此操作的说明。
2. 将 Azure 网站添加为 git 远程。
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. 通过推送到远程进行部署。
   
        git push azure master
4. 在几秒钟内，git 将完成 Web 应用程序发布并启动浏览器，从中可查看在 Azure 中运行的简单作品！

    祝贺你！ 刚才构建了第一个使用 Azure Cosmos DB 的 Node.js Express Web 应用程序并将其发布到了 Azure 网站。

    若需下载或参考本教程的完整参考应用程序，可从 [GitHub][GitHub] 下载。

## <a name="_Toc395637775"></a>后续步骤

* 希望使用 Azure Cosmos DB 执行规模和性能测试？ 请参阅[使用 Azure Cosmos DB 执行规模和性能测试](performance-testing.md)
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中对示例数据集运行查询。
* 浏览 [Azure Cosmos DB 文档](https://docs.microsoft.com/azure/cosmos-db/)。

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

