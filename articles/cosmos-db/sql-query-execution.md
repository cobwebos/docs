---
title: Azure Cosmos DB 中的 SQL 查询执行
description: 了解如何创建 SQL 查询并在 Azure Cosmos DB 中执行它。 本文介绍如何使用 REST API、.Net SDK、JavaScript SDK 和其他各种 Sdk 来创建和执行 SQL 查询。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871255"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 查询执行

任何能够发出 HTTP/HTTPS 请求的语言都可以调用 Cosmos DB REST API。 Cosmos DB 还提供适用于 .NET、node.js、JavaScript 和 Python 编程语言的编程库。 REST API 和库均支持通过 SQL 进行查询，.NET SDK 还支持[LINQ 查询](sql-query-linq-to-sql.md)。

下面的示例演示如何创建查询并根据 Cosmos 数据库帐户提交查询。

## <a id="REST-API"></a>REST API

Cosmos DB 通过 HTTP 提供开放的 RESTful 编程模型。 资源模型由一个数据库帐户下的一组资源组成，Azure 订阅会预配这些资源。 数据库帐户由一组数据库组成，其中每个*数据库*都可以包含多个*容器*，后者又包含*项*、udf 和其他资源类型。 每个 Cosmos DB 资源均可使用逻辑和稳定的 URI 进行寻址。 一组资源称为一个*源*。 

具有这些资源的基本交互模型是通过 HTTP 谓词 `GET`、`PUT`、`POST`和 `DELETE`，它们具有标准的解释。 使用 `POST` 创建新资源、执行存储过程或发出 Cosmos DB 查询。 查询始终为只读操作，且无任何副作用。

下面的示例演示针对示例项的 SQL API 查询的 `POST`。 查询对 JSON `name` 属性有一个简单的筛选器。 `x-ms-documentdb-isquery` 和 Content-type： `application/query+json` 标头表示该操作是一个查询。 将 `mysqlapicosmosdb.documents.azure.com:443` 替换为 Cosmos DB 帐户的 URI。

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

接下来，更复杂的查询从联接返回多个结果：

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

如果查询的结果不能容纳在单个页面中，则 REST API 通过 `x-ms-continuation-token` response 标头返回继续标记。 客户端可以通过在后续结果中包含标头来对结果进行分页。 还可以通过 `x-ms-max-item-count` 编号标头控制每页的结果数。

如果查询有一个聚合函数（如 COUNT），则查询页可能会在一个结果页上返回部分聚合的值。 客户端必须对这些结果执行二级聚合，才能生成最终结果。 例如，对各个页面中返回的计数进行求和以返回总计数。

若要管理查询的数据一致性策略，请在所有 REST API 请求中使用 `x-ms-consistency-level` 标头。 会话一致性还需要回显查询请求中最新的 `x-ms-session-token` cookie 标头。 查询容器的索引策略也可以影响查询结果的一致性。 对于容器的默认索引策略设置，索引始终是项内容的最新，查询结果与为数据选择的一致性匹配。 有关详细信息，请参阅 [Azure Cosmos DB 一致性级别] [一致性级别]。

如果容器中配置的索引策略不能支持指定的查询，则 Azure Cosmos DB 服务器返回 400 "错误的请求"。 对于具有从索引中显式排除的路径的查询，将返回此错误消息。 您可以指定 `x-ms-documentdb-query-enable-scan` 标头以允许查询在索引不可用时执行扫描。

可以通过将 `x-ms-documentdb-populatequerymetrics` 标头设置为 `true`来获取有关查询执行的详细指标。 有关详细信息，请参阅 [Azure Cosmos DB 的 SQL 查询指标](sql-api-query-metrics.md)。

## <a name="c-net-sdk"></a>C#（.NET SDK）

.NET SDK 支持 LINQ 和 SQL 查询。 下面的示例演示如何通过 .NET 执行上述筛选器查询：

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

下面的示例比较每个项内是否相等的两个属性，并使用匿名投影。

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

下一个示例演示通过 LINQ `SelectMany`表示的联接。

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

.NET 客户端自动循环访问 `foreach` 块中所有的查询结果页，如前面的示例中所示。 使用 `CreateDocumentQuery` 方法中的 `FeedOptions` 和 `FeedResponse` 类，还可以在 .NET SDK 中使用[REST API](#REST-API)部分介绍的查询选项。 您可以使用 `MaxItemCount` 设置来控制页数。

还可以通过使用 `IQueryable` 对象创建 `IDocumentQueryable`，然后读取 `ResponseContinuationToken` 值并将它们作为 `FeedOptions`中的 `RequestContinuationToken` 传递回来，来显式控制分页。 当配置的索引策略不支持查询时，可将 `EnableScanInQuery` 设置为启用扫描。 对于分区容器，可以使用 `PartitionKey` 来针对单个分区运行查询，不过 Azure Cosmos DB 可以自动从查询文本中提取此内容。 您可以使用 `EnableCrossPartitionQuery` 对多个分区运行查询。

有关包含查询的更多 .NET 示例，请参阅 GitHub 中的[Azure Cosmos DB .net 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)。

## <a id="JavaScript-server-side-API"></a>JavaScript 服务器端 API

Azure Cosmos DB 提供了一个编程模型，用于使用存储过程和触发器直接在容器上[执行基于 JavaScript 的应用程序](stored-procedures-triggers-udfs.md)逻辑。 然后，在容器级别注册的 JavaScript 逻辑就可以对给定容器的项（包装在环境 ACID 事务中）发出数据库操作。

下面的示例演示如何使用 JavaScript 服务器 API 中的 `queryDocuments` 从存储过程和触发器中执行查询：

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
