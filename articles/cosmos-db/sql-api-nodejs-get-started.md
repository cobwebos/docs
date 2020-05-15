---
title: 适用于 Azure Cosmos DB 的 SQL API 的 Node.js 教程
description: 一个 Node.js 教程，演示如何使用 SQL API 连接到 Azure Cosmos DB 并对其进行查询
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.openlocfilehash: ef493b6b21eb0ba0ad6d22a21e4e205a9fecacb6
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858064"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教程：使用 JavaScript SDK 生成 Node.js 控制台应用以管理 Azure Cosmos DB SQL API 数据

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

作为开发人员，你可能有使用 NoSQL 文件数据的应用程序。 可以使用 Azure Cosmos DB 中的 SQL API 帐户存储和访问此文档数据。 本教程介绍如何生成 Node.js 控制台应用程序，以便创建 Azure Cosmos DB 资源并对其进行查询。

在本教程中，你将：

> [!div class="checklist"]
> * 创建并连接到 Azure Cosmos DB 帐户。
> * 设置应用程序。
> * 创建数据库。
> * 创建容器。
> * 向容器添加项。
> * 对项、容器和数据库执行基本操作。

## <a name="prerequisites"></a>先决条件 

请确保具有以下资源：

* 有效的 Azure 帐户。 如果没有，可以注册 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 或更高版本。

## <a name="create-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

创建 Azure Cosmos DB 帐户。 如果已经有想要使用的帐户，可以跳到 [安装 Node.js 应用程序](#SetupNode)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[设置 Node.js 应用程序](#SetupNode)。 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>设置 Node.js 应用程序

在开始编写生成应用程序所需的代码之前，可以生成应用的框架。 运行以下步骤，设置包含框架代码的 Node.js 应用程序：

1. 打开偏好的终端。
2. 找到想要在其中保存 Node.js 应用程序的文件夹或目录。
3. 使用以下命令创建空的 JavaScript 文件：

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X：
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. 创建并初始化 `package.json` 文件。 请使用以下命令：
   * ```npm init -y```

5. 通过 npm 安装 @azure/cosmos 模块。 请使用以下命令：
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>设置应用的配置

有了应用以后，需确保它可以与 Azure Cosmos DB 通信。 如以下步骤所示，更新一些配置设置即可将应用设置为与 Azure Cosmos DB 通信：

1. 在你喜欢使用的文本编辑器中打开 config.js  文件。

1. 将以下代码片段复制并粘贴到 config.js  文件中，并将属性 `endpoint` 和 `key` 设置为 Azure Cosmos DB 终结点 URI 和主密钥。 数据库和容器名称设置为“Tasks”和“Items”。   将用于此应用程序的分区键是 /category  。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   可在 [Azure 门户](https://portal.azure.com)的“键”  窗格中找到终结点和键详细信息。

   ![从 Azure 门户获取密钥的屏幕截图][keys]

JavaScript SDK 使用通用术语“容器”和“项”。   容器可以是集合、图或表。 项可以是文档、边缘/顶点或行，是容器中的内容。 在前面的代码片段中，`module.exports = config;` 代码用于导出 config 对象，以便你在 app.js  文件中引用它。

## <a name="create-a-database-and-a-container"></a>创建数据库和容器

1. 在你喜欢使用的文本编辑器中打开 databaseContext.js  文件。

1. 将以下代码复制并粘贴到 databaseContext.js  文件中。 此代码定义了一个函数，该函数创建“Tasks”数据库和“Items”容器（如果它们在 Azure Cosmos 帐户中不存在）：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   数据库是跨容器分区的项的逻辑容器。 使用 Databases  类的 `createIfNotExists` 或 create 函数创建数据库。 在使用 SQL API 的情况下，包含项的容器为 JSON 文档。 使用 **Containers** 类的 `createIfNotExists` 或 create 函数创建容器。 创建容器后，你可以存储并查询数据。

   > [!WARNING]
   > 创建容器涉及到定价。 请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)，了解相关信息。

## <a name="import-the-configuration"></a>导入配置

1. 在你喜欢使用的文本编辑器中打开 app.js  文件。

1. 复制并粘贴以下代码，以便导入在前面的步骤中定义的 `@azure/cosmos` 模块、配置和 databaseContext。 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>连接到 Azure Cosmos 帐户

在 app.js  文件中，复制并粘贴以下代码，以便使用前面保存的终结点和键来创建新的 CosmosClient 对象。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> 如果连接到 **Cosmos DB 模拟器**，请为节点进程禁用 TLS 验证：
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

现已获得用于初始化 Azure Cosmos DB 客户端的代码，接下来请看如何使用 Azure Cosmos DB 资源。

## <a name="query-items"></a><a id="QueryItem"></a>查询项

Azure Cosmos DB 支持对存储在每个容器中的 JSON 项进行各种查询。 以下示例代码展示了一个可以针对容器中项运行的查询。可以使用 `Items` 类的 query 函数来查询项。 将以下代码添加到 app.js  文件中，以便从 Azure Cosmos 帐户查询项：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>创建项

可以使用 `Items` 类的 create 函数创建项。 使用 SQL API 时，项会投射为文档，后者是用户定义的（任意）JSON 内容。 在本教程中，我们在 tasks 数据库中创建一个新项。

1. 在 app.js 文件中，定义项定义：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. 添加以下代码，以便创建前面定义的项：

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>更新项

Azure Cosmos DB 支持替换项的内容。 将以下代码复制并粘贴到 app.js  文件中。 此代码获取容器中的某个项，并将 isComplete  字段更新为 true。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>删除项

Azure Cosmos DB 支持删除 JSON 项。 以下代码展示了如何通过项 ID 获取项并将其删除。 将以下代码复制并粘贴到 app.js  文件中：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>运行 Node.js 应用程序

总起来看，代码应如下所示：

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

在终端中，找到 ```app.js``` 文件并运行以下命令：

```bash 
node app.js
```

应该看到已启动应用的输出。 输出应该匹配下面的示例文本。

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>获取完整的 Node.js 教程解决方案

如果没有时间完成本教程中的步骤，或者只需下载代码，则可从 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) 获取。

若要运行包含本文所有代码的入门解决方案，需要以下项：

* 一个 [Azure Cosmos DB 帐户][create-account]。
* GitHub 上提供的[入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)解决方案。

通过 npm 安装项目的依赖项。 使用以下命令：

* ```npm install``` 

接下来，在 ```config.js``` 文件中更新 config.endpoint 和 config.key 的值，如[步骤 3：设置应用的配置](#Config)中所述。  

然后在终端中找到 ```app.js``` 文件并运行以下命令：  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、Azure Cosmos DB 帐户和所有相关的资源时，可将这些资源删除。 为此，请选择用于 Azure Cosmos DB 帐户的资源组，接着选择“删除”  ，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视 Azure Cosmos DB 帐户](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
