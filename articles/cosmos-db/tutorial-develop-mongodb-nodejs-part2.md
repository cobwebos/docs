---
title: 适用于 Azure 的 MongoDB、Angular 和 Node 教程 - 第 2 部分 | Microsoft Docs
description: 本教程系列的第 2 部分，介绍如何通过 Angular 和 Node 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与用于 MongoDB 的 API 完全相同。
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 9d3c3209807a201f11d4d0a4ddd905f332a3951f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>通过 Angular 和 Azure Cosmos DB 创建 MongoDB 应用 - 第 2 部分：通过 Angular CLI 创建 Node.js Express 应用 

本教程包含多个部分，演示了如何通过 Express、Angular 和 Azure Cosmos DB 数据库创建以 Node.js 编写的新 [MongoDB API](mongodb-introduction.md) 应用。

本教程的第 2 部分基于[简介](tutorial-develop-mongodb-nodejs.md)，涵盖以下任务：

> [!div class="checklist"]
> * 安装 Angular CLI 和 TypeScript
> * 使用 Angular 创建新项目
> * 使用 Express 框架生成应用
> * 在 Postman 中测试应用

## <a name="video-walkthrough"></a>视频演练

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>先决条件

在开始教程的此部分之前，请确保已观看[简介视频](tutorial-develop-mongodb-nodejs.md)。

本教程还需要： 
* [Node.js](https://nodejs.org/) 8.4.0 或更高版本。
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) 或你喜欢用的代码编辑器。

> [!TIP]
> 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="install-the-angular-cli-and-typescript"></a>安装 Angular CLI 和 TypeScript

1. 打开 Windows 命令提示符或 Mac Terminal 窗口并安装 Angular CLI。

    ```bash
    npm install -g @angular/cli
    ```

2. 在提示符处输入以下命令，安装 TypeScript。 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>使用 Angular CLI 创建新项目

1. 在命令提示符处转到需在其中创建新项目的文件夹，然后运行以下命令。 此命令创建名为 angular-cosmosdb 的新文件夹和项目，并安装新应用所需的 Angular 组件。 此外，它还会在 src/client 文件夹中安装源代码 (-sd src/client)、使用最低设置 (--minimal)，以及指定该项目使用 Sass（一种带 --style scss 标记的类似 CSS 的语法）。

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. 完成该命令后，将目录转到 src/client 文件夹。

    ```bash
    cd angular-cosmosdb
    ```

3. 然后在 Visual Studio Code 中打开该文件夹。

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>使用 Express 框架生成应用

1. 在 Visual Studio Code 的“资源管理器”窗格中，右键单击 src 文件夹，单击“新建文件夹”，然后将新文件夹命名为“server”。

2. 在“资源管理器”窗格中，右键单击“server”文件夹，单击“新建文件”，然后将新文件命名为“index.js”。

3. 回到命令提示符处，使用以下命令安装正文分析器。 这有助于应用分析通过 API 传入的 JSON 数据。

    ```bash
    npm i express body-parser --save
    ```

4. 将以下代码复制到 Visual Studio Code 的 index.js 文件中。 此代码：
    * 引用 Express
    * 拉入 body-parser，用于读取请求正文中的 JSON 数据
    * 使用称为 path 的内置功能
    * 设置根变量，方便查找代码所处位置
    * 设置端口
    * 启动 Express
    * 告知应用如何使用可以为服务器提供服务的中间件
    * 提供 dist 文件夹中将会成为静态内容的所有内容
    * 为应用程序提供服务，为服务器上找不到的任何 GET 请求提供 index.html（用于深层链接）
    * 通过 app.listen 启动服务器
    * 使用 arow 函数来记录端口处于活动状态这一情况
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. 在 Visual Studio Code 的“资源管理器”窗格中，右键单击 server 文件夹，然后单击“新建文件”。 将新文件命名为 routes.js。 

6. 将以下代码复制到 routes.js 中。 此代码：
   * 引用 Express 路由器
   * 获取 hero
   * 发送回适用于已定义 hero 的 JSON

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. 保存所有修改过的文件。 

8. 在 Visual Studio Code 中单击“调试”按钮 ![Visual Studio Code 中的“调试”图标](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png)，单击“齿轮”按钮 ![Visual Studio Code 中的“齿轮”按钮](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png)，然后选择 Node.js 来创建配置。

   此时会在 Visual Studio Code 中打开新的 launch.json 文件。

8. 在 launch.json 文件的第 11 行将 `"program": "${file}"` 更改为 `"program": "${workspaceRoot}/src/server/index.js"`，然后保存文件。

9. 单击“开始调试”按钮 ![Visual Studio Code 中的“开始调试”图标](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png)，运行应用。

    应用应该正常运行。

## <a name="use-postman-to-test-the-app"></a>使用 Postman 来测试应用

1. 现在请打开 Postman，将 `http://localhost:3000/api/heroes` 置于 GET 框中。 

2. 单击“发送”按钮，从应用获取 JSON 响应。 

    该响应显示应用在本地启动并运行。 

    ![Postman，显示请求和响应](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>后续步骤

在本教程的此部分，你已完成以下操作：

> [!div class="checklist"]
> * 使用 Angular CLI 创建 Node.js 项目
> * 使用 Postman 测试应用

你可以转到本教程的下一部分来生成 UI。

> [!div class="nextstepaction"]
> [通过 Angular 生成 UI](tutorial-develop-mongodb-nodejs-part3.md)
