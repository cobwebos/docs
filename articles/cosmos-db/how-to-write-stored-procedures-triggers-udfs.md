---
title: 在 Azure Cosmos DB 中编写存储过程、触发器和 UDF
description: 了解如何在 Azure Cosmos DB 中定义存储过程、触发器和用户定义的函数
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.openlocfilehash: 3c0ac8ac419b3cdd2b154974d3ccbcce6896e847
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982286"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中编写存储过程、触发器和用户定义的函数

Azure Cosmos DB 提供 JavaScript 的语言集成式事务执行用于编写**存储过程**、**触发器**和**用户定义的函数 (UDF)** 。 在 Azure Cosmos DB 中使用 SQL API 时，可以采用 JavaScript 语言定义存储过程、触发器和 UDF。 可在 JavaScript 中编写逻辑，并在数据库引擎内部执行该逻辑。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure Cosmos DB 中的 JavaScript 语言集成式查询 API](javascript-query-api.md) 和 [Cosmos DB SQL API 客户端 SDK](sql-api-dotnet-samples.md) 来创建及执行触发器、存储过程与 UDF。 

若要调用存储过程、触发器和用户定义的函数，需将其注册。 有关详细信息，请参阅[如何在 Azure Cosmos DB 中使用存储过程、触发器和用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md)。

> [!NOTE]
> 对于已分区的容器，在执行存储过程时，必须在请求选项中提供分区键值。 存储过程的范围始终限定为分区键。 存储过程看不到具有不同分区键值的项。 这一点也适用于触发器。
> [!Tip]
> Cosmos 支持使用存储过程、触发器和用户定义的函数部署容器。 有关详细信息，请参阅[使用服务器端功能创建 Azure Cosmos DB 容器](manage-sql-with-resource-manager.md#create-sproc)。

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>如何编写存储过程

存储过程是使用 JavaScript 编写的，它们可以在 Azure Cosmos 容器中创建、更新、读取、查询和删除项。 存储过程按集合注册，可以针对该集合中的任何文档或附件运行。

下面是一个可以返回“Hello World”响应的简单存储过程。

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

上下文对象提供对所有可在 Azure Cosmos DB 中执行的操作的访问，以及对请求和响应对象的访问。 在本例中，我们将使用响应对象来设置要发回到客户端的响应正文。

编写存储过程后，必须将其注册到集合。 有关详细信息，请参阅[如何在 Azure Cosmos DB 中使用存储过程](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)一文。

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>使用存储过程创建项

使用存储过程创建某个项时，该项将会插入到 Azure Cosmos 容器，并返回新建项的 ID。 创建项是一种异步操作，依赖于 JavaScript 回调函数。 回调函数包含两个参数 - 一个参数用于操作失败时返回的错误对象，另一个参数用于返回值（在本例中为创建的对象）。 在回调内部，可以处理异常或引发错误。 如果未提供回调并出现错误，则 Azure Cosmos DB 运行时将引发错误。

存储过程还包含一个用于设置说明的参数（一个布尔值）。 如果该参数设置为 true，同时缺少说明，则存储过程将引发异常。 否则，存储过程的剩余部分将继续运行。

以下示例存储过程采用新的 Azure Cosmos 项作为输入，将此项插入到 Azure Cosmos 容器，然后返回新建项的 ID。 此示例利用[快速入门 .NET SQL API](create-sql-api-dotnet.md) 中的 ToDoList 示例

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>将数组用作存储过程的输入参数

使用 Azure 门户定义存储过程时，输入参数始终以字符串的形式发送到该存储过程。 即使将字符串数组作为输入传递，该数组也会转换为字符串发送到存储过程。 若要解决此问题，可在存储过程中定义一个函数以将字符串作为数组进行分析。 以下代码演示如何将字符串输入参数作为数组进行分析：

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>存储过程中的事务

可以使用存储过程对容器中的项实现事务。 以下示例使用梦幻足球游戏应用中的事务，通过单个操作在两支球队之间交易球员。 该存储过程尝试读取两个 Azure Cosmos 项，其中每个项对应于作为参数传递的球员 ID。 如果找到了这两个球员，则存储过程将通过交换其所在球队来更新这些项。 如果在此过程中遇到了任何错误，则存储过程将引发 JavaScript 异常，从而隐式终止事务。

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>存储过程中的绑定执行

下面是将项批量导入到 Azure Cosmos 容器的存储过程示例。 存储过程通过检查来自 `createDocument` 的布尔返回值来处理绑定执行，然后使用每次调用存储过程时插入的项计数，来跟踪不同的批及恢复其进度。

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>异步 await 与存储过程

下面是使用 helper 函数将 async await 与承诺结合使用的存储过程的示例。 存储过程会查询项并替换项。

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>如何编写触发器

Azure Cosmos DB 支持前触发器和后触发器。 前触发器是在修改数据库项之前执行的，后触发器是在修改数据库项之后执行的。

### <a name="pre-triggers"></a><a id="pre-triggers"></a>前触发器

以下示例演示如何使用前触发器来验证正在创建的 Azure Cosmos 项的属性。 此示例利用[快速入门 .NET SQL API](create-sql-api-dotnet.md) 中的 ToDoList 示例，将时间戳属性添加到新添加的项（如果其中不包含此属性）。

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

预触发器不能有任何输入参数。 使用触发器中的请求对象来处理与操作关联的请求消息。 在前面的示例中，创建 Azure Cosmos 项时将运行前触发器，请求消息正文包含要以 JSON 格式创建的项。

注册触发器后，可以指定可对哪些操作运行该触发器。 应使用 `TriggerOperation.Create` 的 `TriggerOperation` 值创建此触发器，这意味着，不允许在以下代码所示的 replace 操作中使用此触发器。

有关如何注册和调用前触发器的示例，请参阅[前触发器](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[后触发器](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)文章。 

### <a name="post-triggers"></a><a id="post-triggers"></a>后触发器

以下示例演示了一个后触发器。 此触发器查询元数据项，并在其中更新有关新建项的详细信息。


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

必须注意的一个要点是 Azure Cosmos DB 中触发器的事务执行。 后触发器作为基础项本身的同一事务的一部分运行。 后触发器执行期间的异常将导致整个事务失败。 提交的任何内容都将回退并返回异常。

有关如何注册和调用前触发器的示例，请参阅[前触发器](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[后触发器](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)文章。 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>如何编写用户定义的函数

以下示例创建一个 UDF 用于计算各个收入阶层的所得税。 然后，在查询中使用此用户定义的函数。 此示例假设有一个名为“Incomes”的容器，其中包含以下属性：

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

下面是用于计算各个收入阶层的所得税的函数定义：

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

有关如何注册和使用用户定义的函数的示例，请参阅[如何在 Azure Cosmos DB 中使用用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md#udfs)一文。

## <a name="logging"></a>Logging 

使用存储过程、触发器或用户定义的函数时，可以使用 `console.log()` 命令来记录步骤。 当 `EnableScriptLogging` 设置为 true 时，该命令会专注于一个字符串进行调试，如以下示例所示：

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>后续步骤

详细了解概念以及如何在 Azure Cosmos DB 中编写或使用存储过程、触发器和用户定义的函数：

* [如何在 Azure Cosmos DB 中注册和使用存储过程、触发器与用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md)

* [如何在 Azure Cosmos DB 中使用 Javascript 查询 API 编写存储过程和触发器](how-to-write-javascript-query-api.md)

* [在 Azure Cosmos DB 中使用 Azure Cosmos DB 存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)

* [在 Azure Cosmos DB 中使用 JavaScript 语言集成式查询 API](javascript-query-api.md)
