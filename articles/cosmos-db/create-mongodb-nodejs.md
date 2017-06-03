---
title: "使用 Node.js 将 MongoDB 应用连接到 Azure Cosmos DB | Microsoft Docs"
description: "了解如何将现有的 Node.js MongoDB 应用连接到 Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: bfdf42ef717c090bffb89e9f276a135c58b1884f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB：迁移现有的 Node.js MongoDB Web 应用 

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门演示如何使用以 Node.js 编写的现有 [MongoDB](mongodb-introduction.md) 应用，并将其连接到支持 MongoDB 客户端连接的 Azure Cosmos DB 数据库。 换而言之，Node.js 应用程序仅知道它要使用 MongoDB API 连接到某个数据库。 应用程序完全知道数据存储在 Azure Cosmos DB 中。

完成本教程后，[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 中将会运行一个 MEAN（MongoDB、Express、AngularJS 和 Node.js）应用程序。 

![在 Azure 应用服务中运行的 MEAN.js 应用](./media/create-mongodb-nodejs/meanjs-in-azure.png)

## <a name="prerequisites"></a>先决条件 

在开始学习本快速入门之前，请确保计算机上[已安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 此外，需要 [Node.js](https://nodejs.org/) 和 [Git](http://www.git-scm.com/downloads)。 将运行 `az`、`npm` 和 `git` 命令。

应具备 Node.js 的实践知识。 本快速入门并未介绍有关开发 Node.js 应用程序的一般信息。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

运行下列命令以克隆示例存储库。 此示例存储库包含默认的 [MEAN.js](http://meanjs.org/) 应用程序。 

```bash
git clone https://github.com/prashanthmadi/mean
```

## <a name="run-the-application"></a>运行应用程序

安装所需的包，并启动应用程序。

```bash
cd mean
npm install
npm start
```

## <a name="log-in-to-azure"></a>登录 Azure

现在，请在终端窗口中使用 Azure CLI 2.0 创建所需的资源，用于在 Azure 应用服务中托管 Node.js 应用程序。  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>添加 Azure Cosmos DB 模块

若要使用 Azure Cosmos DB 命令，请添加 Azure Cosmos DB 模块。 

```azurecli
az component update --add cosmosdb
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在西欧区域中创建资源组。 选择资源组的唯一名称。

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用 [az cosmosdb create](/cli/azure/cosmosdb#create) 命令创建 Azure Cosmos DB 帐户。

在以下命令中，请将 `<cosmosdb_name>` 占位符替换为你自己的唯一 Azure Cosmos DB 帐户名。 此唯一名称将用作 Azure Cosmos DB 终结点 (`https://<cosmosdb_name>.documents.azure.com/`) 的一部分，因此需要在 Azure 中的所有 Azure Cosmos DB 帐户之间保持唯一。 

```azurecli
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 参数启用 MongoDB 客户端连接。

创建 Azure Cosmos DB 帐户后，Azure CLI 将显示类似于以下示例的信息： 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb_name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>将 Node.js 应用程序连接至数据库

此步骤使用 MongoDB 连接字符串将 MEAN.js 示例应用程序连接到刚刚创建的 Azure Cosmos DB 数据库。 

## <a name="retrieve-the-key"></a>检索密钥

若要连接到 Azure Cosmos DB 数据库，需要使用数据库密钥。 使用 [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) 命令检索主键。

```azurecli
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Azure CLI 输出类似于以下示例的信息。 

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
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>在 Node.js 应用程序中配置连接字符串

在 MEAN.js 存储库中，打开 `config/env/local-development.js`。

请将此文件的内容替换为以下代码。 确保将两个 `<cosmosdb_name>` 占位符替换为 Azure Cosmos DB 帐户名，将 `<primary_master_key>` 占位符替换为在上一步骤中复制的密钥。

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

> [!NOTE] 
> 由于 [Azure Cosmos DB 需要 SSL](connect-mongodb-account.md#connection-string-requirements)，因此 `ssl=true` 选项非常重要。 
>
>

保存所做更改。

### <a name="run-the-application-again"></a>再次运行应用程序。

再次运行 `npm start`。 

```bash
npm start
```

此时应会显示一条控制台消息，告知开发环境已启动并运行。 

在浏览器中导航至 `http://localhost:3000`。 在顶部菜单中单击“注册”，然后尝试创建两个虚构的用户。 

MEAN.js 示例应用程序将用户数据存储在数据库中。 如果上述操作成功并且 MEAN.js 可自动登录到已创建的用户，则表示 Azure Cosmos DB 连接可正常工作。 

![MEAN.js 成功连接至 MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>在数据资源管理器中查看数据

可在 Azure 门户中查看、查询 Azure Cosmos DB 存储的数据并对其运行业务逻辑。

若要查看、查询和处理在上一步骤中创建的用户数据，请在 Web 浏览器中登录到 [Azure 门户](https://portal.azure.com)。

在顶部搜索框中，键入“Azure Cosmos DB”。 打开 Cosmos DB 帐户边栏选项卡后，请选择你的 Cosmos DB 帐户。 在左侧导航栏中，单击“数据资源管理器”。 在“集合”窗格中展开你的集合，然后即可查看该集合中的文档，查询数据，甚至可以创建和运行存储过程、触发器与 UDF。 

![Azure 门户中的数据资源管理器](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>将 Node.js 应用程序部署到 Azure

此步骤将已连接 MongoDB 的 Node.js 应用程序部署到 Azure Cosmos DB。

你可能已经注意到，前面更改的配置文件适用于开发环境 (`/config/env/local-development.js`)。 将应用程序部署到应用服务后，该应用程序默认在生产环境中运行。 因此，现在需要对相应的配置文件做出相同的更改。

在 MEAN.js 存储库中，打开 `config/env/production.js`。

在 `db` 对象中，替换 `uri` 的值，如下方示例所示。 请务必像前面一样替换占位符。

```javascript
'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
```

在终端中，将所有更改提交到 Git。 可以复制这两个命令，以便同时运行。

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门教程已介绍如何创建 Azure Cosmos DB 帐户以及使用数据资源管理器创建 MongoDB 集合。 现在，可将 MongoDB 数据迁移到 Azure Cosmos DB。  

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)

