---
title: 适用于 Azure Cosmos DB 的 SQL API 的 Node.js 教程
description: 一个 Node.js 教程，演示如何使用 SQL API 连接到 Azure Cosmos DB 并对其进行查询
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 8d2272ca468f7c415d9d03506ecaa3fbe667f0c0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873166"
---
# <a name="tutorial-build-a-nodejs-console-app-with-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教程：使用 JavaScript SDK 生成 Node.js 控制台应用以管理 Azure Cosmos DB SQL API 数据

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

本教程介绍如何生成 Node.js 控制台应用程序，以便创建 Azure Cosmos DB 资源并对其进行查询。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建并连接到 Azure Cosmos DB 帐户
> * 设置应用程序
> * 创建数据库
> * 创建容器
> * 向容器添加项
> * 对项、容器和数据库执行 CRUD 操作

没有时间创建应用程序？ 不必担心！ 可在 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started )上获取完整的解决方案。 有关快速说明，请参阅本文的[获取完整解决方案](#GetSolution)部分。

## <a name="prerequisites"></a>先决条件 

请确保具有以下资源：

* 有效的 Azure 帐户。 如果没有，可以注册 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 或更高版本。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户

让我们创建一个 Azure Cosmos DB 帐户。 如果已经有想要使用的帐户，可以跳到 [安装 Node.js 应用程序](#SetupNode)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[设置 Node.js 应用程序](#SetupNode)。 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>步骤 2：安装 Node.js 应用程序

1. 打开最喜爱的终端。
2. 找到想要在其中保存 Node.js 应用程序的文件夹或目录。
3. 使用以下命令创建两个空的 JavaScript 文件：

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X：
     * ```touch app.js```
     * ```touch config.js```

4. 通过 npm 安装 @azure/cosmos 模块。 请使用以下命令：
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>步骤 3：设置应用配置

1. 在喜爱的文本编辑器中打开 ```config.js``` 。

1. 复制并粘贴以下代码片段，并将属性 ```config.endpoint``` 和 ```config.primaryKey``` 设置为 Azure Cosmos DB 终结点 URI 和主密钥。 这两项配置都可以在 [Azure 门户](https://portal.azure.com)中找到。

   ![从 Azure 门户获取密钥的屏幕截图][keys]

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. 复制 ```database```、```container``` 和 ```items``` 数据并将其粘贴到在其中设置了 ```config.endpoint``` 和 ```config.primaryKey``` 属性的 ```config``` 对象（见下）。 如果已有要在数据库中存储的数据，则可以使用 Azure Cosmos DB 的[数据迁移工具](import-data.md)，而不是在此处定义数据。

   ```nodejs
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   注意，如果你熟悉旧版 JavaScript SDK，则可能习惯于看到术语“集合”和“文档”。 由于 Azure Cosmos DB 支持[多 API 模型](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities)，因此 2.0+ 版的 JavaScript SDK 使用通用术语“容器”和“项”。 容器可以是集合、图或表。 项可以是文档、边缘/顶点或行，是容器中的内容。 

1. 最后，导出 ```config``` 对象，以便可以在 ```app.js``` 文件中引用。

   ```nodejs
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>步骤 4：连接到 Azure Cosmos DB 帐户

1. 在文本编辑器中打开空的 ```app.js``` 文件。 复制并粘贴以下代码，以导入 ```@azure/cosmos``` 模块和新建的 ```config``` 模块。

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');
   ```

1. 复制并粘贴以下代码，以使用前面保存的 ```config.endpoint``` 和 ```config.primaryKey``` 来创建新的 CosmosClient。

   ```nodejs
   const url = require('url');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```

现已获得用于初始化 Azure Cosmos DB 客户端的代码，接下来请看如何使用 Azure Cosmos DB 资源。

## <a name="step-5-create-a-database"></a>步骤 5：创建数据库

1. 复制并粘贴以下代码，以设置数据库 ID 和容器 ID。通过这些 ID，可了解 Azure Cosmos DB 客户端如何查找正确的数据库和容器。

   ```nodejs
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   可以使用 **Databases** 类的 [createIfNotExists](/javascript/api/%40azure/cosmos/databases) 或 [create](/javascript/api/%40azure/cosmos/databases) 函数创建数据库。 数据库是跨容器分区的项的逻辑容器。 

2. 复制 **createDatabase** 和 **readDatabase** 方法并将其粘贴到 app.js 文件的 ```databaseId``` 和 ```containerId``` 定义下。 **createDatabase** 函数会使用通过 ```config``` 对象指定的 ID ```FamilyDatabase``` 来创建新数据库（如果不存在数据库）。 **readDatabase** 函数会读取数据库的定义，确保数据库存在。

   ```nodejs
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. 复制并粘贴以下代码，以便设置 **createDatabase** 和 **readDatabase** 函数，添加可列显退出消息的帮助程序函数 **exit**。 

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. 复制并粘贴以下代码，以便设置 **exit** 函数，方便调用 **createDatabase** 和 **readDatabase** 函数。

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   此时 ```app.js``` 中的代码应如以下代码所示：

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. 在终端中，找到 ```app.js``` 文件并运行以下命令： 

   ```bash 
   node app.js
   ```

祝贺你！ 已成功创建了 Azure Cosmos DB 数据库。

## <a id="CreateContainer"></a>步骤 6：创建容器

> [!WARNING]
> 调用函数 **createContainer** 会创建新的容器，牵涉到定价。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

可以使用 **Containers** 类的 [createIfNotExists](/javascript/api/%40azure/cosmos/containers) 或 [create](/javascript/api/%40azure/cosmos/containers) 函数创建容器。  容器包含项（在使用 SQL API 的情况下为 JSON 文档）和关联的 JavaScript 应用程序逻辑。

1. 复制 **createContainer** 和 **readContainer** 函数并将其粘贴到 app.js 文件的 **readDatabase** 函数下。 **createContainer** 函数会使用通过 ```config``` 对象指定的 ```containerId``` 来创建新容器（如果不存在容器）。 **readContainer** 函数会读取容器定义，以便验证容器是否存在。

   ```nodejs
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. 复制以下代码并将其粘贴到对 **readDatabase** 的调用下面，以便执行 **createContainer** 和 **readContainer** 函数。

   ```nodejs
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   此时 ```app.js``` 中的代码应如下所示：

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在终端中，找到 ```app.js``` 文件并运行以下命令： 

   ```bash 
   node app.js
   ```

祝贺你！ 已成功创建 Azure Cosmos DB 容器。

## <a id="CreateItem"></a>步骤 7：创建项

可以使用 **Items** 类的 [create](/javascript/api/%40azure/cosmos/items) 函数创建项。 使用 SQL API 时，项会投射为文档，后者是用户定义的（任意）JSON 内容。 现在，可以将项插入 Azure Cosmos DB 中。

1. 将 **createFamilyItem** 函数复制并粘贴到 **readContainer** 函数下面。 **createFamilyItem** 函数创建的项包含在 ```config``` 对象中保存的 JSON 数据。 我们会进行检查，确保在创建项之前不存在具有相同 ID 的项。

   ```nodejs
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. 复制以下代码并将其粘贴到对 **readContainer** 的调用下面，以便执行 **createFamilyItem** 函数。

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在终端中，找到 ```app.js``` 文件并运行以下命令： 

   ```bash 
   node app.js
   ```

祝贺你！ 已成功创建了 Azure Cosmos DB 项。


## <a id="Query"></a>步骤 8：查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个容器中的 JSON 文档进行[各种查询](how-to-sql-query.md)。 下面的示例代码演示了一个可以针对容器中文档运行的查询。

1. 复制 **queryContainer** 函数并将其粘贴到 app.js 文件的 **createFamilyItem** 函数下面。 Azure Cosmos DB 支持类似 SQL 的查询，如下所示。 有关构建复杂查询的详细信息，请参阅[查询演练](https://www.documentdb.com/sql/demo)和[查询文档](how-to-sql-query.md)。

   ```nodejs
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. 复制以下代码并将其粘贴到对 **createFamilyItem** 的调用下面，以便执行 **queryContainer** 函数。

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在终端中，找到 ```app.js``` 文件并运行以下命令：

   ```bash 
   node app.js
   ```

祝贺你！ 已成功查询了 Azure Cosmos DB 项。

## <a id="ReplaceItem"></a>步骤 9：替换项
Azure Cosmos DB 支持替换项的内容。

1. 复制 **replaceFamilyItem** 函数并将其粘贴到 app.js 文件的 **queryContainer** 函数下面。 请注意，我们已将某个子项的属性“grade”从以前的值 5 更改为 6。

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. 复制以下代码并将其粘贴到对 **queryContainer** 的调用下面，以便执行 **replaceFamilyItem** 函数。 另外，请再次将此代码添加到 **queryContainer** 调用，以验证是否已成功更改项。

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在终端中，找到 ```app.js``` 文件并运行以下命令：

   ```bash 
   node app.js
   ```

祝贺你！ 已成功替换了 Azure Cosmos DB 项。

## <a id="DeleteItem"></a>步骤 10：删除项

Azure Cosmos DB 支持删除 JSON 项。

1. 将 **deleteFamilyItem** 函数复制并粘贴到 **replaceFamilyItem** 函数下面。

   ```nodejs
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. 复制以下代码并将其粘贴到对第二个 **queryContainer** 的调用下面，以便执行 **deleteFamilyItem** 函数。

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 在终端中，找到 ```app.js``` 文件并运行以下命令： 

   ```bash 
   node app.js
   ```

祝贺你！ 已成功删除了 Azure Cosmos DB 项。

## <a id="DeleteDatabase"></a>步骤 11：删除数据库

删除已创建的数据库会删除该数据库及其所有子资源（容器、项等）。

1. 复制 **cleanup** 函数并将其粘贴到 **deleteFamilyItem** 函数下面，以便删除数据库及其所有子资源。

   ```nodejs
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. 复制以下代码并将其粘贴到对 **deleteFamilyItem** 的调用下面，以便执行 **cleanup** 函数。

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>步骤 12：运行整个 Node.js 应用程序！

总起来看，代码应如下所示：

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

在终端中，找到 ```app.js``` 文件并运行以下命令： 

```bash 
node app.js
```

应该看到已启动应用的输出。 输出应该匹配下面的示例文本。

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

祝贺你！ 已完成了 Node.js 教程并创建了第一个 Azure Cosmos DB 控制台应用程序！

## <a id="GetSolution"></a>获取完整的 Node.js 教程解决方案

如果没有时间完成本教程中的步骤，或者只需下载代码，则可从 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) 获取。

若要运行包含本文所有代码的入门解决方案，需执行以下步骤：

* 一个 [Azure Cosmos DB 帐户][create-account]。
* GitHub 上提供的[入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)解决方案。

通过 npm 安装 **@azure/cosmos** 模块。 请使用以下命令：

* ```npm install @azure/cosmos --save```

接下来如[步骤 3：设置应用的配置](#Config)中所述，在 ```config.js``` 文件中更新 config.endpoint 和 config.primaryKey 的值。 

然后在终端中找到 ```app.js``` 文件并运行以下命令： 

```bash 
node app.js
```

就这么简单，你可以继续操作了！ 

## <a name="next-steps"></a>后续步骤

* 想要更复杂的 Node.js 示例？ 请参阅[构建使用 Azure Cosmos DB 的 Node.js Web 应用程序](sql-api-nodejs-application.md)。
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中对示例数据集运行查询。

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
