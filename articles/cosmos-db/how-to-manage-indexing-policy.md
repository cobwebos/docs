---
title: 管理 Azure Cosmos DB 中的索引策略
description: 了解如何管理索引策略、在索引中包括或排除属性、如何使用不同的 Azure Cosmos DB SDK 定义索引
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: tisande
ms.openlocfilehash: b913ba58252f4cb84d010aea39d371316582bd6d
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869926"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的索引策略

在 Azure Cosmos DB 中，数据是按照为每个容器定义的[索引策略](index-policy.md)编制索引的。 新建容器的默认索引策略会对任何字符串或数字强制使用范围索引。 可以使用你自己的自定义索引策略覆盖此策略。

## <a name="indexing-policy-examples"></a>索引策略示例

下面是以 [JSON 格式](index-policy.md#include-exclude-paths)显示的一些索引策略示例，该格式是在 Azure 门户上公开索引策略的方式。 可以通过 Azure CLI 或任何 SDK 设置相同的参数。

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>用以有选择地排除某些属性路径的选择退出策略

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

此索引策略等同于下面的手动将 ```kind```、```dataType``` 和 ```precision``` 设置为默认值的策略。 这些属性不再需要显式设置，可以从索引策略中完全省略它们（如上例所示）。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>用以有选择地包括某些属性路径的选择加入策略

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

此索引策略等同于下面的手动将 ```kind```、```dataType``` 和 ```precision``` 设置为默认值的策略。 这些属性不再需要显式设置，可以从索引策略中完全省略它们（如上例所示）。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> 通常情况下，建议使用**选择退出**索引策略来让 Azure Cosmos DB 主动为可能会添加到模型的任何新属性编制索引。

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>仅在特定属性路径上使用空间索引

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>组合索引策略示例

除了包含或排除各属性的路径，还可以指定一个组合索引。 如果要执行具有针对多个属性的 `ORDER BY` 子句的查询，需要使用这些属性上的[组合索引](index-policy.md#composite-indexes)。 此外，对于具有筛选器且对不同属性使用 ORDER BY 子句的查询，组合索引将具有性能优势。

> [!NOTE]
> 复合路径具有隐式`/?` ，因为仅为该路径上的标量值编制索引。 复合`/*`路径中不支持通配符。 不应在`/?`复合`/*`路径中指定或。

### <a name="composite-index-defined-for-name-asc-age-desc"></a>针对（name asc、age desc）定义的组合索引：

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

查询 #1 和查询 #2 需要上述对 name 和 age 的组合索引：

查询 #1：

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

查询 #2：

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

此组合索引将使查询 #3 和查询 #4 受益，并优化筛选器：

查询 #3：

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

查询 #4：

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>为 (name ASC, age ASC) 和 (name ASC, age DESC) 定义的组合索引：

可以在同一个索引策略中定义多个不同的组合索引。

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>为 (name ASC, age ASC) 定义的组合索引：

可以选择指定顺序。 如果未指定，顺序为升序。

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>排除所有属性路径，但使索引保持活动状态

当[生存时间 (TTL) 功能](time-to-live.md)处于活动状态但不需要第二索引时（使用 Azure Cosmos DB 作为纯键-值存储），可以使用此策略。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>无索引

此策略将关闭索引。 如果 `indexingMode` 设置为 `none`，则无法在容器上设置 TTL。

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>更新索引策略

在 Azure Cosmos DB 中，可以使用以下任一方法更新索引策略：

- 从 Azure 门户
- 使用 Azure CLI
- 使用 PowerShell
- 使用某个 SDK

[索引策略更新](index-policy.md#modifying-the-indexing-policy)会触发索引转换。 还可以通过 SDK 跟踪此转换的进度。

> [!NOTE]
> 更新索引策略时，对 Azure Cosmos DB 的写入不会中断。 在重新编制索引期间，查询可能会在更新索引时返回部分结果。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

Azure Cosmos 容器将其索引策略存储为 JSON 文档，可以在 Azure 门户中直接编辑这些文档。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 创建新的 Azure Cosmos 帐户或选择现有的帐户。

1. 打开“数据资源管理器”窗格，选择要使用的容器。 

1. 单击“缩放设置”。 

1. 修改索引策略 JSON 文档（请参阅[下文](#indexing-policy-examples)中的示例）

1. 完成后，单击“保存”。 

![使用 Azure 门户管理索引编制](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

若要创建具有自定义索引策略的容器，请参阅[使用 CLI 创建具有自定义索引策略的容器](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>使用 PowerShell

若要创建具有自定义索引策略的容器，请参阅[使用 Powershell 创建具有自定义索引策略的容器](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 中的 `DocumentCollection` 对象公开了一个 `IndexingPolicy` 属性，可以通过该属性更改 `IndexingMode` 以及添加或删除 `IncludedPaths` 和 `ExcludedPaths`。

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

若要跟踪索引转换进度，请传递一个 `RequestOptions` 对象，用以将 `PopulateQuotaInfo` 属性设置为 `true`。

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 中的 `ContainerProperties` 对象（请参阅有关其用法的[此快速入门](create-sql-api-dotnet.md)）公开了一个 `IndexingPolicy` 属性，可以通过该属性更改 `IndexingMode` 以及添加或删除 `IncludedPaths` 和 `ExcludedPaths`。

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

若要跟踪索引转换进度，请传递一个用以将 `PopulateQuotaInfo` 属性设置为 `true` 的 `RequestOptions` 对象，然后从 `x-ms-documentdb-collection-index-transformation-progress` 响应标头中检索该值。

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

在创建新容器的同时定义自定义索引策略时，SDK V3 的 fluent API 可让你以简洁高效的方式编写这个定义：

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>使用 Java SDK

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 中的 `DocumentCollection` 对象（请参阅有关其用法的[此快速入门](create-sql-api-java.md)）公开了 `getIndexingPolicy()` 和 `setIndexingPolicy()` 方法。 通过它们操作的 `IndexingPolicy` 对象，你可以更改索引模式，以及添加或删除包括的和排除的路径。

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

若要在容器上跟踪索引转换进度，请传递一个用以请求要填充的配额信息的 `RequestOptions` 对象，然后从 `x-ms-documentdb-collection-index-transformation-progress` 响应标头中检索该值。

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 中的 `ContainerDefinition` 接口（请参阅有关其用法的[此快速入门](create-sql-api-nodejs.md)）公开了一个 `indexingPolicy` 属性，可以通过该属性更改 `indexingMode` 以及添加或删除 `includedPaths` 和 `excludedPaths`。

检索容器的详细信息

```javascript
const containerResponse = await client.database('database').container('container').read();
```

将索引模式设置为“一致”

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

添加包含的路径（包括空间索引）

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

添加排除的路径

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

使用更改更新容器

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

若要在容器上跟踪索引转换进度，请传递一个用以将 `populateQuotaInfo` 属性设置为 `true` 的 `RequestOptions` 对象，然后从 `x-ms-documentdb-collection-index-transformation-progress` 响应标头中检索该值。

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>使用 Python SDK

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

使用 [Python SDK V3](https://pypi.org/project/azure-cosmos/) 时（有关其用法，请参阅[此快速入门](create-sql-api-python.md)），容器配置将作为字典进行管理。 从此字典中，可以访问索引策略及其所有属性。

检索容器的详细信息

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

将索引模式设置为“一致”

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

使用包含的路径和空间索引定义索引策略

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

使用排除的路径定义索引策略

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

添加组合索引

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

使用更改更新容器

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK V4](#tab/pythonv4)

使用 [Python SDK V4](https://pypi.org/project/azure-cosmos/) 时，容器配置将作为字典进行管理。 从此字典中，可以访问索引策略及其所有属性。

检索容器的详细信息

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

将索引模式设置为“一致”

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

使用包含的路径和空间索引定义索引策略

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

使用排除的路径定义索引策略

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

添加组合索引

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

使用更改更新容器

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```
---

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](index-overview.md)
- [索引策略](index-policy.md)
