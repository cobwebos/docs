---
title: "在 Azure 中生成 Node.js 和 MongoDB Web 应用 | Microsoft Docs"
description: "了解如何在 Azure 中运行 Node.js 应用，并使用 MongoDB 连接字符串连接到 DocumentDB 数据库。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6db07ed122d8dbd9edaa3b4d25680863778a6adf
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>在 Azure 中生成 Node.js 和 MongoDB Web 应用
本教程演示如何在 Azure 中创建 Node.js Web 应用，并将其连接至 MongoDB 数据库。 完成操作后，将拥有一个运行在 [Azure 应用服务 Web 应用](app-service-web-overview.md)上的 MEAN 应用程序（MongoDB、Express、AngularJS 和 Node.js）。

![在 Azure 应用服务中运行的 MEAN.js 应用](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

## <a name="before-you-begin"></a>开始之前

运行本示例之前，请在本地安装以下必备组件：

1. [下载并安装 git](https://git-scm.com/)
1. [下载并安装 Node.js 和 NPM](https://nodejs.org/)
1. [安装 Gulp.js](http://gulpjs.com/)
1. [下载、安装和运行 MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)。 
1. [下载和安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb-database"></a>测试本地 MongoDB 数据库
在此步骤中，请确保本地 MongoDB 数据库正在运行。

将终端窗口和 `cd` 打开到 MongoDB 安装的 `bin` 目录。 

在终端运行 `mongo` 以连接到本地 MongoDB 服务器。

```bash
mongo
```

如果连接成功，那么 MongoDB 数据库已经开始运行。 如果连接不成功，请确保按[下载、安装和运行 MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 中的步骤来启动本地 MongoDB 数据库。

完成 MongoDB 数据库测试后，请在终端键入 `Ctrl`+`C`。 

<a name="step2"></a>

## <a name="create-local-nodejs-application"></a>创建本地 Node.js 应用程序
在此步骤中，将设置本地 Node.js 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

将终端窗口和 `cd` 打开到工作目录。  

运行下列命令以克隆示例存储库。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

此示例存储库包括 [MEAN.js](http://meanjs.org/) 应用程序。 

### <a name="run-the-application"></a>运行应用程序

安装所需的包，并启动应用程序。

```bash
cd meanjs
npm install
npm start
```

当应用完全加载后，应看见类似下方所示的消息：

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

在浏览器中导航至 `http://localhost:3000`。 单击菜单顶部的“注册”，尝试创建虚拟用户。 

MEAN.js 示例应用程序将用户数据存储在数据库中。 如果操作成功且 MEAN.js 自动登录已创建的用户，则应用将向本地 MongoDB 数据库写入数据。

![MEAN.js 成功连接至 MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

尝试单击“管理员” > “管理文章”，添加一些文章。

在终端键入 `Ctrl`+`C`，随时停止 Node.js。 

## <a name="create-a-production-mongodb-database"></a>创建生产 MongoDB 数据库

在此步骤中，将在 Azure 中创建 MongoDB 数据库。 当应用被部署到 Azure，它将此数据库用于其生产工作负荷。

对于 MongoDB，本教程使用 [Azure DocumentDB](/azure/documentdb/)。 Azure DocumentDB 支持 MongoDB 客户端连接，这意味着 Node.js 应用程序只知道它正在连接到 MongoDB 数据库。 连接由 DocumentDB 数据库提供支持这一事实对应用程序而言是显而易见的。

### <a name="log-in-to-azure"></a>登录 Azure

现在，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 应用服务中托管 Node.js 应用程序。  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在西欧地区创建资源组：

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

若要查看适用于 `--location` 的可能值，请使用 `az appservice list-locations` Azure CLI 命令。

### <a name="create-a-documentdb-account"></a>创建一个 DocumentDB 帐户

使用 [az documentdb create](/cli/azure/documentdb#create) 命令创建 DocumentDB 帐户。

在下列命令中，请在看到 `<documentdb_name>` 占位符的位置替换上自己的唯一 DocumentDB 名称。 此唯一名称将用作 DocumentDB 终结点 (`https://<documentdb_name>.documents.azure.com/`) 的一部分，因此这个名称需要在 Azure 的所有 DocumentDB 帐户中具有唯一性。 

```azurecli
az documentdb create --name <documentdb_name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 参数启用 MongoDB 客户端连接。

创建 DocumentDB 帐户后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<documentdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<documentdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<documentdb_name>",
  "readLocations": [
    ...
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    ...
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>将 Node.js 应用程序连接至数据库

在此步骤中，使用 MongoDB 连接字符串将 MEAN.js 示例应用程序连接至刚创建的 DocumentDB 数据库。 

### <a name="retrieve-the-database-key"></a>检索数据库关键字

若要连接至 DocumentDB 数据库，需要数据库关键字。 使用 [az documentdb list-keys](/cli/azure/documentdb#list-keys) 命令来检索主关键字。

```azurecli
az documentdb list-keys --name <documentdb_name> --resource-group myResourceGroup
```

Azure CLI 输出的信息类似于下列示例：

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

将 `primaryMasterKey` 的值复制到文本编辑器。 下一步需要用到此信息。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>在 Node.js 应用程序中配置连接字符串

在 MEAN.js 存储库中，打开 `config/env/production.js`。

在 `db` 对象中，替换 `uri` 的值，如下方示例所示。 请确保将两个 `<documentdb_name>` 占位符替换为你的 DocumentDB 数据库名称，将 `<primary_master_key>` 占位符替换为先前步骤中所复制的关键字。

```javascript
db: {
  uri: 'mongodb://<documentdb_name>:<primary_master_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> `ssl=true` 选项很重要，因为 [Azure DocumentDB 需要 SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements)。 
>
>

保存所做更改。

### <a name="test-the-application-in-production-mode"></a>在生产模式下测试应用程序 

和其他 Node.js Web 框架相似，MEAN.js 使用 `gulp prod` 为生产环境缩小和捆绑脚本。 这将生成生产环境所需的文件。 

现在运行 `gulp prod`。

```bash
gulp prod
```

接下来运行下列命令，以使用在 `config/env/production.js` 中配置的连接字符串。

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` 设置环境变量，它告知 Node.js 在生产环境中运行；`node server.js` 启动 Node.js 服务器，其中 `server.js` 位于存储库根路径中。 这就是 Node.js 应用程序在 Azure 中加载的方式。 

在加载应用时请进行检查，确保它在生产中运行：

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

在浏览器中导航至 `http://localhost:8443`。 单击菜单顶部的“注册”，如先前一般尝试创建虚拟用户。 如果创建成功，则应用在 Azure 中向 DocumentDB 数据库写入数据。 

在终端中，通过键入 `Ctrl`+`C` 停止 Node.js。 

## <a name="deploy-the-nodejs-application-to-azure"></a>将 Node.js 应用程序部署到 Azure
在此步骤中，你将连接了 MongoDB 的 Node.js 应用程序部署至 Azure 应用服务。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建应用服务计划。 

> [!NOTE] 
> 应用服务计划表示用于托管应用的物理资源集合。 分配到应用服务计划的所有应用程序将共享该计划定义的资源，在托管多个应用时可以节省成本。 
> 
> 应用服务计划定义： 
> 
> * 区域（北欧、美国东部、东南亚） 
> * 实例大小（小、中、大） 
> * 规模计数（一个、两个、三个实例，等等） 
> * SKU（免费、共享、基本、标准、高级） 
> 

以下示例使用**免费**定价层创建名为 `myAppServicePlan` 的应用服务计划：

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息：

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 0, 
    "family": "F", 
    "name": "F1", 
    "tier": "Free" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

### <a name="create-a-web-app"></a>创建 Web 应用

创建应用服务计划后，请在 `myAppServicePlan` 应用服务计划中创建 Web 应用。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 

在下列命令中，请用自己的唯一应用名称替换 `<app_name>` 占位符。 该唯一名称将用作 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息： 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-an-environment-variable"></a>配置环境变量

在本教程的前面部分，你已在 `config/env/production.js` 中对数据库连接字符串进行硬编码。 为遵循最佳安全实践，建议将这些敏感数据保持在 Git 存储库之外。 对于运行在 Azure 中的应用，与之相对的将使用环境变量。

在应用服务中，使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令将环境变量设置为_应用设置_。 

下面示例在 Azure Web 应用中配置 `MONGODB_URI` 应用设置。 请确保像之前那样替换占位符。

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true"
```

在 Node.js 代码中，使用 `process.env.MONGODB_URI` 访问此应用设置，如同访问任何环境变量那样。 

现在，使用以下命令撤销对 `config/env/production.js` 的更改：

```bash
git checkout -- .
```

再次打开 `config/env/production.js`。 请注意，默认 MEAN.js 应用已配置为使用刚创建的 `MONGODB_URI` 环境变量。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>配置本地 Git 部署 

可以通过不同的方法将应用程序部署到 Azure 应用服务，包括 FTP、本地 Git、 GitHub、Visual Studio Team Services 和 BitBucket。 对于 FTP 和本地 Git 部署，需在服务器上配置一个部署用户，用于对部署进行身份验证。 

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令创建帐户级别的凭据。 

> [!NOTE] 
> 在应用服务中进行 FTP 和本地 Git 部署时需要一个部署用户。 此部署用户是帐户级别的。 同样，它与 Azure 订阅帐户不相同。 你只需配置此部署用户一次。

```azurecli
az appservice web deployment user set --user-name <specify-a-username> --password <minimum-8-char-capital-lowercase-number>
```

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令配置对 Azure Web 应用的本地 Git 访问。 

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
```

配置部署用户后，Azure CLI 将显示 Azure Web 应用的部署 URL，格式如下所示：

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

复制终端的输出，因为下一步骤将要用到。 

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

将 Azure 远程功能添加到本地 Git 存储库。 

```bash
git remote add azure <paste_copied_url_here> 
```

推送到 Azure 远程以部署 Node.js 应用程序。 系统将提示你输入前面在创建部署用户期间提供的密码。 

```bash
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> 你可能会注意到部署进程在 `npm install` 之后运行 [Gulp](http://gulpjs.com/)。 应用服务在部署期间不会运行 Gulp 或 Grunt 任务，因此该示例存储库的根目录中有两个额外文件用于启用它： 
>
> - `.deployment` - 此文件告知应用服务以自定义部署脚本运行 `bash deploy.sh`。
> - `deploy.sh` - 自定义部署脚本。 如果查看该文件，则将看到它在 `npm install` 和 `bower install` 之后运行 `gulp prod`。 
>
> 可以通过此方法向基于 Git 的部署添加任意步骤。
>
> 请注意，如果重启 Azure Web 应用（无论何时），应用服务都不会重新运行这些自动化任务。
>
>

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用 
使用 Web 浏览器浏览到已部署的 Web 应用。 

```bash 
http://<app_name>.azurewebsites.net 
``` 

单击菜单顶部的“注册”，尝试创建虚拟用户。 

如果操作成功，且应用自动登录到已创建的用户，则 Azure 中的 MEAN.js 应用已连接至 MongoDB (DocumentDB) 数据库。 

![在 Azure 应用服务中运行的 MEAN.js 应用](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

尝试单击“管理员” > “管理文章”，添加一些文章。 

**祝贺你！** 你正在 Azure 应用服务中运行数据驱动的 Node.js 应用。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

在此步骤中，将对 `article` 数据模型进行一些更改，并将其发布至 Azure。

### <a name="update-the-data-model"></a>更新数据模型

打开 `modules/articles/server/models/article.server.model.js`。

在 `ArticleSchema` 中，添加名为 `comment` 的 `String` 类型。 完成后，架构代码应该如下所示：

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

你已完成模型更改。 

### <a name="update-the-articles-code"></a>更新文章代码

接下来，更新剩余 `articles` 代码以使用 `comment`。

需修改的文件共计五 (5) 个，分别是服务器控制器以及四个客户端视图。 

首先，打开 `modules/articles/server/controllers/articles.server.controller.js`。

在 `update` 函数中，添加 `article.comment` 的赋值。 完成后，`update` 函数应该如下所示：

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

接下来，打开 `modules/articles/client/views/view-article.client.view.html`。

在 `</section>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

接下来，打开 `modules/articles/client/views/list-articles.client.view.html`。

在 `</a>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

接下来，打开 `modules/articles/client/views/admin/list-articles.client.view.html`。

在 `<div class="list-group">` 标记内，以及 `</a>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

最后，打开 `modules/articles/client/views/admin/form-article.client.view.html`。

查找包含提交按钮的 `<div class="form-group">` 标记，如下所示：

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

在此标记的正上方，添加另一个 `<div class="form-group">` 标记，它允许人们编辑 `comment` 字段。 新标记应如下所示：

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>在本地测试更改

保存所有更改。

在生产模式中再次测试更改。

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> 请记住 `config/env/production.js` 已还原，且 `MONGODB_URI` 环境变量仅在 Azure Web 应用中被设置，而非在本地计算机中设置。 看一看配置文件，将发现生产配置默认使用本地 MongoDB 数据库。 这确保在本地测试代码更改时，不会接触生产数据。
>
>

在浏览器中导航至 `http://localhost:8443`，并确保你已登录。

单击“管理员” > “管理文章”，然后单击“+”按钮来添加文章。

现在应能看到新 `Comment` 文本框。

![向文章添加注释字段](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

在终端中，通过键入 `Ctrl`+`C` 停止 Node.js。 

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

提交在 git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "added article comment"
git push azure master
```

一旦 `git push` 完成，请导航至 Azure Web 应用，然后再次尝试新功能。

![发布到 Azure 的模型和数据库更改](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> 如果先前添加过任何文章，现在仍能看到它们。 DocumentDB 中的现有数据没有丢失。 同时，对数据架构的更新和现有数据都将保持不变。
>
>

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志 

当 Node.js 应用程序在 Azure 应用服务中运行时，可以将控制台日志直接传输到终端。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

若要启动日志流式处理，请使用 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 命令。

```azurecli 
az appservice web log tail --name <app_name> --resource-group myResourceGroup 
``` 

一旦启动日志流式处理，请在浏览器中刷新 Azure Web 应用，以获取一些 Web 流量。 现在应能看到控制台日志传送到终端。

通过键入 `Ctrl`+`C`，随时停止日志流式处理。 

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 Azure 门户查看已创建的 Web 应用。

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务边栏选项卡](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

<!--

## Step 4 - Download server logs
In this step, you turn on monitoring of your web app with web server logs, and then download these logs. 

### Enable logging
Enable all logging options for your web app.

```azurecli
az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
```

### Generate errors

To generate some error entries, navigate to a nonexistent page in your web app. For example: `http://<app_name>.azurewebsites.net/404`. 

### Download log files
Download the log files for review.

```azurecli
az appservice web log download --name <app_name> --resource-group myResourceGroup
```

## Step 5 - Scale to another region
In this step, you scale your Node.js app to serve your customers in a new region. That way, you can tailor your web app to customers in different regions, and also put your web app closer to them to improve performance. When you're done with this step, you will have a [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) profile with two endpoints, which route traffic to two web apps which reside in different geographical regions.

1. Create a Traffic Manager profile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

    > [!NOTE]
    > `--routing-method Performance` specifies that this profile [routes user traffic to the closest endpoint](../traffic-manager/traffic-manager-routing-methods.md).

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    ```azurecli
    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
    ```

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage. 

    Since your Traffic Manager profile only has one endpoint which points to your West Europe web app, this is the only page you'll see. Next, you create a new web app in Southeast Asia and add a new endpoint to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 2]<#step2>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```
  
Now, try to access the URL of your Traffic Manager profile. If you access the URL from the Europe region, you should see "Express Europe", but from the Asia region, you should see "Express Asia".

-->
## <a name="more-resources"></a>更多资源

