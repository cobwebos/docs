---
title: Azure Cosmos DB 中的 SQL 查询执行
description: 了解 Azure Cosmos DB 中的 SQL 查询执行
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: c42732df1bcfa8649c89899febc364bb1f5f9b5a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999917"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 查询执行

能够发出 HTTP/HTTPS 请求的任何语言都可以调用 Cosmos DB REST API。 Cosmos DB 还为 .NET、Node.js、JavaScript 和 Python 编程语言提供编程库。 REST API 和库全部支持通过 SQL 执行的查询，.NET SDK 还支持 [LINQ 查询](sql-query-linq-to-sql.md)。

以下示例演示了如何对 Cosmos 数据库帐户创建和提交该查询。

## <a id="REST-API"></a>REST API

Cosmos DB 通过 HTTP 提供开放的 RESTful 编程模型。 资源模型由 Azure 订阅预配的数据库帐户下的一组资源组成。 该数据库帐户由一组数据库组成，其中的每个数据库可以包含多个容器，而每个容器又可以包含项、UDF 和其他资源类型。 可以使用稳定的逻辑 URI 对每个 Cosmos DB 资源进行寻址。 一组资源称作一个源。 

这些资源的基本交互模型是通过 HTTP 谓词 `GET`、`PUT`、`POST` 和 `DELETE` 及其标准解释实现的。 使用 `POST` 可以创建新的资源、执行存储过程或发出 Cosmos DB 查询。 查询始终为只读操作，且无任何副作用。

以下示例演示了如何根据示例项针对 SQL API 查询发出 `POST`。 该查询对 JSON `name` 属性应用一个简单的筛选器。 `x-ms-documentdb-isquery` 和 Content-Type: `application/query+json` 标头表示该操作是一个查询。 请将 `mysqlapicosmosdb.documents.azure.com:443` 替换为 Cosmos DB 帐户的 URI。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

其结果是：

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

下面这个更复杂的查询从某个联接操作返回多个结果：

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

其结果是： 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

如果查询的结果无法包含在一页中，则 REST API 会通过 `x-ms-continuation-token` 响应标头返回继续标记。 客户端可以通过在后续结果中包含该标头对结果进行分页。 也可以通过 `x-ms-max-item-count` 数字标头控制每页的结果数。

如果查询包含 COUNT 等聚合函数，则查询页可以仅通过一个结果页返回部分聚合的值。 客户端必须对这些结果执行二级聚合才能生成最终结果。 例如，将各个页中返回的计数求和可以返回总计数。

若要管理查询的数据一致性策略，请使用 `x-ms-consistency-level` 标头（如所有的 REST API 请求）。 对于会话一致性，还需要回显查询请求中最新的 `x-ms-session-token` Cookie 标头。 查询容器的索引策略也可以影响查询结果的一致性。 使用默认的索引策略设置，容器的索引始终与项内容保持同步，且查询结果将与为数据选择的一致性匹配。 有关详细信息，请参阅 [Azure Cosmos DB 一致性级别][一致性级别]。

如果容器上配置的索引策略不能支持指定的查询，Azure Cosmos DB 服务器会返回 400“错误的请求”。 使用从索引中显式排除的路径执行查询时，将返回此错误消息。 可以指定 `x-ms-documentdb-query-enable-scan` 标头，以便在索引不可用时允许查询执行扫描。

可以通过将 `x-ms-documentdb-populatequerymetrics` 标头设置为 `true` 来获取有关查询执行的详细指标。 有关详细信息，请参阅 [Azure Cosmos DB 的 SQL 查询指标](sql-api-query-metrics.md)。

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK 支持 LINQ 和 SQL 查询。 以下示例演示如何使用 .NET 执行前面所示的筛选查询：

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

以下示例比较了每个项内等式的两个属性，并使用匿名投影。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

以下示例演示通过 LINQ `SelectMany` 表达的联接。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 客户端自动循环访问 `foreach` 块中所有的查询结果页，如前面的示例中所示。 [REST API](#REST-API) 部分介绍的查询选项也适用于在 `CreateDocumentQuery` 方法中使用 `FeedOptions` 和 `FeedResponse` 类的 .NET SDK。 可以使用 `MaxItemCount` 设置控制页数。

还可以通过使用 `IQueryable` 对象创建 `IDocumentQueryable`，并读取 `ResponseContinuationToken` 值并将它们作为 `FeedOptions` 中的 `RequestContinuationToken` 向回传递，从而显式控制分页。 可以设置 `EnableScanInQuery`，以便在配置的索引策略不支持该查询时启用扫描。 对于分区容器，可以使用 `PartitionKey` 针对单个分区运行查询，不过，Azure Cosmos DB 可以自动从查询文本中提取此信息。 可以使用 `EnableCrossPartitionQuery` 针对多个分区运行查询。

有关更多包含查询的 .NET 示例，请参阅 GitHub 中的 [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)。

## <a id="JavaScript-server-side-API"></a>JavaScript 服务器端 API

Azure Cosmos DB 提供一种编程模型，用于通过存储过程和触发器对容器直接[执行基于 JavaScript 的应用程序](stored-procedures-triggers-udfs.md)逻辑。 然后，在容器级别注册的 JavaScript 逻辑可以针对给定容器中的、包装在环境 ACID 事务中的项发出数据库操作。

以下示例演示了如何在 JavaScript 服务器 API 中使用 `queryDocuments` 从存储过程和触发器内部进行查询。

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure Cosmos DB 一致性级别](consistency-levels.md)
