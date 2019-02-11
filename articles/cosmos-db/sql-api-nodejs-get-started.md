---
title: 适用于 Azure Cosmos DB 的 SQL API 的 Node.js 教程
description: 一个 Node.js 教程，演示如何使用 SQL API 连接到 Azure Cosmos DB 并对其进行查询
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 9c0255382e2cfe09683931408d25ffb3f60419d1
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508889"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>教程：使用 JavaScript SDK 生成 Node.js 控制台应用以管理 Azure Cosmos DB SQL API 数据

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

作为开发人员，你可能有使用 NoSQL 文件数据的应用程序。 可以使用 Azure Cosmos DB 中的 SQL API 帐户存储和访问此文档数据。 本教程介绍如何生成 Node.js 控制台应用程序，以便创建 Azure Cosmos DB 资源并对其进行查询。

在本教程中，将：

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

让我们创建一个 Azure Cosmos DB 帐户。 如果已经有想要使用的帐户，可以跳到 [安装 Node.js 应用程序](#SetupNode)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[设置 Node.js 应用程序](#SetupNode)。 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>设置 Node.js 应用程序

在开始编写生成应用程序所需的代码之前，可以生成应用的框架。 运行以下步骤，设置包含框架代码的 Node.js 应用程序：

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

## <a id="Config"></a>设置应用的配置

有了应用以后，需确保它可以与 Azure Cosmos DB 通信。 如以下步骤所示，更新一些配置设置即可将应用设置为与 Azure Cosmos DB 通信：

1. 在喜爱的文本编辑器中打开 ```config.js``` 。

1. 复制并粘贴以下代码片段，并将属性 ```config.endpoint``` 和 ```config.primaryKey``` 设置为 Azure Cosmos DB 终结点 URI 和主密钥。 这两项配置都可以在 [Azure 门户](https://portal.azure.com)中找到。

   ![从 Azure 门户获取密钥的屏幕截图][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. 复制 ```database```、```container``` 和 ```items``` 数据并将其粘贴到在其中设置了 ```config.endpoint``` 和 ```config.primaryKey``` 属性的 ```config``` 对象（见下）。 如果已有要在数据库中存储的数据，则可以使用 Azure Cosmos DB 中的数据迁移工具，而不是在此处定义数据。

   ```javascript
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

   JavaScript SDK 使用通用术语“容器”和“项”。 容器可以是集合、图或表。 项可以是文档、边缘/顶点或行，是容器中的内容。 

1. 最后，导出 ```config``` 对象，以便可以在 ```app.js``` 文件中引用。

   ```javascript
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>连接到 Azure Cosmos DB 帐户

1. 在文本编辑器中打开空的 ```app.js``` 文件。 复制并粘贴以下代码，以导入 ```@azure/cosmos``` 模块和新建的 ```config``` 模块。

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');
   ```

1. 复制并粘贴以下代码，以使用前面保存的 ```config.endpoint``` 和 ```config.primaryKey``` 来创建新的 CosmosClient。

   ```javascript
   const url = require('url');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> 如果连接到 **Cosmos DB 模拟器**，请通过创建自定义连接策略来禁用 SSL 验证。
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

现已获得用于初始化 Azure Cosmos DB 客户端的代码，接下来请看如何使用 Azure Cosmos DB 资源。

## <a name="create-a-database"></a>创建数据库

1. 复制并粘贴以下代码，以设置数据库 ID 和容器 ID。 通过这些 ID，可了解 Azure Cosmos DB 客户端如何查找正确的数据库和容器。

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   可以使用 **Databases** 类的 `createIfNotExists` 或 create 函数创建数据库。 数据库是跨容器分区的项的逻辑容器。 

2. 复制 **createDatabase** 和 **readDatabase** 方法并将其粘贴到 app.js 文件的 ```databaseId``` 和 ```containerId``` 定义下。 **createDatabase** 函数会使用通过 ```config``` 对象指定的 ID ```FamilyDatabase``` 来创建新数据库（如果不存在数据库）。 **readDatabase** 函数会读取数据库的定义，确保数据库存在。

   ```javascript
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

   ```javascript
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

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   此时 ```app.js``` 中的代码应如以下代码所示：

   ```javascript
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

## <a id="CreateContainer"></a>创建容器

接下来在 Azure Cosmos DB 帐户中创建一个容器，用于存储和查询数据。 

> [!WARNING]
创建容器涉及到定价。 请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)，了解相关信息。

可以使用 **Containers** 类的 `createIfNotExists` 或 create 函数创建容器。  容器包含项（在使用 SQL API 的情况下为 JSON 文档）和关联的 JavaScript 应用程序逻辑。

1. 复制 **createContainer** 和 **readContainer** 函数并将其粘贴到 app.js 文件的 **readDatabase** 函数下。 **createContainer** 函数会使用通过 ```config``` 对象指定的 ```containerId``` 来创建新容器（如果不存在容器）。 **readContainer** 函数会读取容器定义，以便验证容器是否存在。

   ```javascript
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

   ```javascript
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

   ```javascript
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

## <a id="CreateItem"></a>创建项

可以使用 **Items** 类的 create 函数创建项。 使用 SQL API 时，项会投射为文档，后者是用户定义的（任意）JSON 内容。 现在，可以将项插入 Azure Cosmos DB 中。

1. 将 **createFamilyItem** 函数复制并粘贴到 **readContainer** 函数下面。 **createFamilyItem** 函数创建的项包含在 ```config``` 对象中保存的 JSON 数据。 我们会进行检查，确保在创建项之前不存在具有相同 ID 的项。

   ```javascript
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

   ```javascript
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


## <a id="Query"></a>查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个容器中的 JSON 文档进行各种查询。 下面的示例代码演示了一个可以针对容器中文档运行的查询。

1. 复制 **queryContainer** 函数并将其粘贴到 app.js 文件的 **createFamilyItem** 函数下面。 Azure Cosmos DB 支持类似 SQL 的查询，如下所示。

   ```javascript
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

   ```javascript
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


## <a id="ReplaceItem"></a>替换项
Azure Cosmos DB 支持替换项的内容。

1. 复制 **replaceFamilyItem** 函数并将其粘贴到 app.js 文件的 **queryContainer** 函数下面。 请注意，我们已将某个子项的属性“grade”从以前的值 5 更改为 6。

   ```javascript
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

   ```javascript
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


## <a id="DeleteItem"></a>删除项

Azure Cosmos DB 支持删除 JSON 项。

1. 将 **deleteFamilyItem** 函数复制并粘贴到 **replaceFamilyItem** 函数下面。

   ```javascript
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. 复制以下代码并将其粘贴到对第二个 **queryContainer** 的调用下面，以便执行 **deleteFamilyItem** 函数。

   ```javascript
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


## <a id="DeleteDatabase"></a>删除数据库

删除已创建的数据库会删除该数据库及其所有子资源（容器、项等）。

1. 复制 **cleanup** 函数并将其粘贴到 **deleteFamilyItem** 函数下面，以便删除数据库及其所有子资源。

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. 复制以下代码并将其粘贴到对 **deleteFamilyItem** 的调用下面，以便执行 **cleanup** 函数。

   ```javascript
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

## <a id="Run"></a>运行 Node.js 应用程序

总起来看，代码应如下所示：

```javascript
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

## <a id="GetSolution"></a>获取完整的 Node.js 教程解决方案 

如果没有时间完成本教程中的步骤，或者只需下载代码，则可从 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) 获取。 

若要运行包含本文所有代码的入门解决方案，需要以下项： 

* 一个 [Azure Cosmos DB 帐户][create-account]。 
* GitHub 上提供的[入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)解决方案。 

通过 npm 安装 **@azure/cosmos** 模块。 请使用以下命令： 

* ```npm install @azure/cosmos --save``` 

接下来，在 ```config.js``` 文件中更新 config.endpoint 和 config.primaryKey 的值，如[步骤 3：设置应用的配置](#Config)中所述。  

然后在终端中找到 ```app.js``` 文件并运行以下命令：  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、Azure Cosmos DB 帐户和所有相关的资源时，可将这些资源删除。 为此，请选择用于 Azure Cosmos DB 帐户的资源组，接着选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视 Azure Cosmos DB 帐户](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
