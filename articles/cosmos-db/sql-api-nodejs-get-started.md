---
title: 适用于 Azure Cosmos DB 的 SQL API 的 Node.js 教程 | Microsoft Docs
description: 本 Node.js 教程将创建采用 SQL API 的 Cosmos DB。
keywords: node.js 教程, 节点数据库
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/14/2017
ms.author: sngun
ms.openlocfilehash: 6a98d3b477a3cb06c1454a79b77dffa685853a35
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084818"
---
# <a name="nodejs-tutorial-use-the-sql-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Node.js 教程：在 Azure Cosmos DB 中使用 SQL API 创建 Node.js 控制台应用程序

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [用于 MongoDB 的 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)


欢迎使用 Azure Cosmos DB Node.js SDK 的 Node.js 教程！ 学习本教程后，将拥有一个可创建并查询 Azure Cosmos DB 资源的控制台应用程序。

我们介绍：

* 创建并连接到 Azure Cosmos DB 帐户
* 设置应用程序
* 创建节点数据库
* 创建集合
* 创建 JSON 文档
* 查询集合
* 替换文档
* 删除文档
* 删除节点数据库

没有时间？ 不必担心！ 可在 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)上获取完整的解决方案。 有关快速说明，请参阅 [Get the complete solution](#GetSolution) （获取完整解决方案）。

完成 Node.js 教程后，请使用位于本页顶部和底部的投票按钮向我们提供反馈。 如果希望我们直接与你联系，欢迎将电子邮件地址附在评论中。

现在，让我们开始吧！

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Node.js 教程的先决条件

请确保具有以下内容：

* 有效的 Azure 帐户。 如果没有，可以注册 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) 版本 v0.10.29 或更高版本。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户

让我们创建一个 Azure Cosmos DB 帐户。 如果已经有想要使用的帐户，可以跳到 [安装 Node.js 应用程序](#SetupNode)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[安装 Node.js 应用程序](#SetupNode)。

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
4. 通过 npm 安装 documentdb 模块。 请使用以下命令：
   * ```npm install documentdb --save```

很好！ 既然已完成安装设置，让我们开始编写一些代码。

## <a id="Config"></a>步骤 3：设置应用配置

在喜爱的文本编辑器中打开 ```config.js``` 。

然后，复制并粘贴以下代码片段，并将属性 ```config.endpoint``` 和 ```config.primaryKey``` 设置为 Azure Cosmos DB 终结点 URI 和主密钥。 这两项配置都可以在 [Azure 门户](https://portal.azure.com)中找到。

![Node.js 教程 - Azure 门户的屏幕截图，显示了一个 Azure Cosmos DB 帐户，在“Azure Cosmos DB 帐户”边栏选项卡上突出显示了“ACTIVE”中心、“密钥”按钮，在“密钥”边栏选项卡上突出显示了 URI、主密钥、辅助密钥的值 - Node 数据库][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

复制 ```database id```、```collection id``` 和 ```JSON documents``` 并将其粘贴到在其中设置了 ```config.endpoint``` 和 ```config.primaryKey``` 属性的 ```config``` 对象下面。 如果已有要在数据库中存储的数据，则可以使用 Azure Cosmos DB 的[数据迁移工具](import-data.md)而不是添加文档定义。

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
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

数据库、集合和文档定义将充当 Azure Cosmos DB ```database id```、```collection id``` 和文档的数据。

最后，导出 ```config``` 对象，以便可以在 ```app.js``` 文件中引用。

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>步骤 4：连接到 Azure Cosmos DB 帐户

在文本编辑器中打开空的 ```app.js``` 文件。 复制并粘贴以下代码，以导入 ```documentdb``` 模块和新建的 ```config``` 模块。

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    const uriFactory = require('documentdb').UriFactory;
    var config = require("./config");

复制并粘贴以下代码，以使用前面保存的 ```config.endpoint``` 和 ```config.primaryKey``` 来创建新的 DocumentClient。

    var config = require("./config");

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

现已获得用于初始化 Azure Cosmos DB 客户端的代码，接下来请看如何使用 Azure Cosmos DB 资源。

## <a name="step-5-create-a-node-database"></a>步骤 5：创建节点数据库

复制并粘贴以下代码，以设置“找不到”消息的 HTTP 状态、数据库 ID 和集合 ID。通过这些 ID，可了解 Azure Cosmos DB 客户端如何查找正确的数据库和集合。

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseId = config.database.id;
    var collectionId = config.collection.id;

可以使用 **DocumentClient** 类的 [createDatabase](/javascript/api/documentdb/documentclient) 函数创建[数据库](sql-api-resources.md#databases)。 数据库是跨集合分区的文档存储的逻辑容器。

复制并粘贴 **getDatabase** 函数，以便使用 ```config``` 对象中指定的 ```databaseId``` 在 app.js 文件中创建新数据库。 该函数会检查是否不存在具有相同 ```FamilyRegistry``` 的数据库。 如果确实存在，我们将返回该数据库而不是创建新的。

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${databaseId}\n`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase({ id: databaseId }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

在设置 **getDatabase** 函数的位置下面复制并粘贴以下代码添加帮助器函数 **exit**，该函数将列显退出消息并调用 **getDatabase** 函数。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    };

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功创建了 Azure Cosmos DB 数据库。

## <a id="CreateColl"></a>步骤 6：创建集合

> [!WARNING]
> createCollection 将创建新的集合，它牵涉定价。 有关详细信息，请访问 [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

可以使用 **DocumentClient** 类的 [createCollection](/javascript/api/documentdb/documentclient) 函数创建[集合](sql-api-resources.md#collections)。 集合是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。

将 **getCollection** 函数复制并粘贴到 app.js 文件中 **getDatabase** 函数下面，以便使用 ```config``` 对象中指定的 ```collectionId``` 创建新集合。 同样，我们将首先检查以确保不存在具有相同 ```FamilyCollection``` ID 的集合。 如果确实存在，我们将返回该集合而不是创建新的。

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${collectionId}\n`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
                        client.createCollection(databaseUrl, { id: collectionId }, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

将以下代码复制并粘贴到对 **getDatabase** 的调用下面，以执行 **getCollection** 函数。

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功创建 Azure Cosmos DB 集合。

## <a id="CreateDoc"></a>步骤 7：创建文档

可以使用 **DocumentClient** 类的 [createDocument](/javascript/api/documentdb/documentclient) 函数创建[文档](sql-api-resources.md#documents)。 文档为用户定义的（任意）JSON 内容。 现在，可以将文档插入 Azure Cosmos DB 中。

将 **getFamilyDocument** 函数复制并粘贴到 **getCollection** 函数下面，创建包含 ```config``` 对象中保存的 JSON 数据的文档。 同样，我们将首先检查以确保不存在具有相同 ID 的文档。

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        console.log(`Getting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

将以下代码复制并粘贴到对 **getCollection** 的调用下面，以执行 **getFamilyDocument** 函数。

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功创建 Azure Cosmos DB 文档。

![Node.js 教程 - 说明帐户、数据库、集合和文档间层次关系的关系图 - 节点数据库](./media/sql-api-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>步骤 8：查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行[各种查询](sql-api-sql-query.md)。 下面的示例代码演示了一个针对集合中文档运行的查询。

将 **queryCollection** 函数复制并粘贴到 app.js 文件中的 **getFamilyDocument** 函数下面。 Azure Cosmos DB 支持类似 SQL 的查询，如下所示。 有关构建复杂查询的详细信息，请参阅[查询演练](https://www.documentdb.com/sql/demo)和[查询文档](sql-api-sql-query.md)。

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${collectionId}`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };

下图说明了如何针对所创建的集合调用 Azure Cosmos DB SQL 查询语法。

![Node.js 教程 - 说明查询的范围和含义的关系图 - 节点数据库](./media/sql-api-nodejs-get-started/node-js-tutorial-collection-documents.png)

查询中的关键字 [FROM](sql-api-sql-query.md#FromClause) 是可选项，因为 Azure Cosmos DB 查询已限制为单个集合。 因此，“FROM Families f”可与“FROM root r”或者任何其他所选变量名进行交换。 Azure Cosmos DB 将推断所选 Families、root 或变量名，并默认引用当前集合。

将以下代码复制并粘贴到对 **getFamilyDocument** 的调用下面，以执行 **queryCollection** 函数。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功查询了 Azure Cosmos DB 文档。

## <a id="ReplaceDocument"></a>步骤 9：替换文档
Azure Cosmos DB 支持替换 JSON 文档。

将 **replaceFamilyDocument** 函数复制并粘贴到 app.js 文件中的 **queryCollection** 函数下面。

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        console.log(`Replacing document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        document.children[0].grade = 6;
        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

将以下代码复制并粘贴到对 **queryCollection** 的调用下面，以执行 **replaceDocument** 函数。 此外，再次将此代码添加到 **queryCollection** 调用，以验证是否已成功更改文档。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功替换了 Azure Cosmos DB 文档。

## <a id="DeleteDocument"></a>步骤 10：删除文档

Azure Cosmos DB 支持删除 JSON 文档。

将 **deleteFamilyDocument** 函数复制并粘贴到 **replaceFamilyDocument** 函数下面。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        console.log(`Deleting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

将以下代码复制并粘贴到对第二个 **queryCollection** 的调用下面，以执行 **deleteDocument** 函数。

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

祝贺你！ 已成功删除了 Azure Cosmos DB 文档。

## <a id="DeleteDatabase"></a>步骤 11：删除 Node 数据库

删除已创建的数据库将删除该数据库及其所有子资源（集合、文档等）。

将 **cleanup** 函数复制并粘贴到 **deleteFamilyDocument** 函数下面，删除数据库和所有子资源。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${databaseId}`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    };

将以下代码复制并粘贴到对 **deleteFamilyDocument** 的调用下面，以执行 **cleanup** 函数。

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>步骤 12：运行整个 Node.js 应用程序！

总体而言，调用函数的顺序应如下所示：

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在终端中，找到 ```app.js``` 文件并运行命令：```node app.js```

应该看到已启动应用的输出。 输出应该匹配下面的示例文本。

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

祝贺你！ 已完成 Node.js 教程，并成功创建了第一个 Azure Cosmos DB 控制台应用程序！

## <a id="GetSolution"></a>获取完整的 Node.js 教程解决方案

如果没有时间完成本教程中的步骤，或者只需下载代码，则可从 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) 获取。

若要运行包含本文所有示例的 GetStarted 解决方案，需要做好以下准备：

* [Azure Cosmos DB 帐户][create-account]。
* GitHub 上提供的 [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) 解决方案。

通过 npm 安装 **documentdb** 模块。 请使用以下命令：

* ```npm install documentdb --save```

接下来如[步骤 3：设置应用的配置](#Config)中所述，在 ```config.js``` 文件中更新 config.endpoint 和 config.primaryKey 的值。 

然后在终端中找到 ```app.js``` 文件并运行命令 ```node app.js```。

就这么简单，生成以后即可开始操作！ 

## <a name="next-steps"></a>后续步骤
* 想要更复杂的 Node.js 示例？ 请参阅[构建使用 Azure Cosmos DB 的 Node.js Web 应用程序](sql-api-nodejs-application.md)。
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中对示例数据集运行查询。

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
