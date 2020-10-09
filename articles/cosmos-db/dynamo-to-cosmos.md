---
title: 将应用程序从 Amazon DynamoDB 迁移到 Azure Cosmos DB
description: 了解如何将 .NET 应用程序从 Amazon DynamoDB 迁移到 Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: cfdeda8ac3957da272ab4c47fb93930c826d55aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261862"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>将应用程序从 Amazon DynamoDB 迁移到 Azure Cosmos DB

Azure Cosmos DB 是一种全球分布的可扩展的全托管式数据库。 它能够确保数据访问延迟较低。 若要详细了解 Azure Cosmos DB，请参阅[概述](introduction.md)一文。 本文介绍如何在最低限度更改代码的情况下将 .NET 应用程序从 DynamoDB 迁移到 Azure Cosmos DB。

## <a name="conceptual-differences"></a>概念差异

下面是 Azure Cosmos DB 与 DynamoDB 之间的主要概念差异：

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|不适用|  数据库 |
|表      |  集合 |
|  项目 |  文档 |
|属性|字段|
|辅助索引|辅助索引|
|主键 - 分区键|分区键|
|主键 - 排序键| 不需要 |
|Stream|ChangeFeed|
|写入计算单位|请求单位（灵活，可用于读取或写入）|
|读取计算单位    |请求单位（灵活，可用于读取或写入）|
|全局表| 不需要。 预配 Azure Cosmos 帐户时可直接选择区域（稍后可更改区域）|

## <a name="structural-differences"></a>结构差异

与 DynamoDB 相比，Azure Cosmos DB 具有更简单的 JSON 结构。 以下示例显示了两者的差异

**DynamoDB**：

以下 JSON 对象表示 DynamoDB 中的数据格式

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**：

以下 JSON 对象表示 Azure Cosmos DB 中的数据格式

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>迁移数据

有多种选项可用于将数据迁移到 Azure Cosmos DB。 要了解详细信息，请参阅[用于将本地或云数据迁移到 Azure Cosmos DB 的选项](cosmosdb-migrationchoices.md)一文。

## <a name="migrate-your-code"></a>迁移代码

本文主要讲的是将应用程序的代码迁移到 Azure Cosmos DB，这是数据库迁移的关键方面。 为帮助你更快获得知识，以下部分中有 Amazon DynamoDB 与 Azure Cosmos DB 的等效代码片段之间的代码并排比较。

要下载源代码，请克隆以下存储库：

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>先决条件

- .NET Framework 4.7.2
- Visual Studio 2019
- 对 Azure Cosmos DB SQL API 帐户的访问权限
- Amazon DynamoDB 的本地安装
- Java 8
- 在端口 8000 上运行 Amazon DynamoDB 的可下载版本（可更改和配置代码）

### <a name="set-up-your-code"></a>设置代码

在项目中添加以下 NuGet 包：

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>建立连接

**DynamoDB**：

在 Amazon DynamoDB 中，下列代码用于连接：

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**：

若要连接 Azure Cosmos DB，请将代码更新为：

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**在 Azure Cosmos DB 中优化连接**

借助 Azure Cosmos DB，可使用以下代码来优化连接：

* **ConnectionMode** 使用直接连接模式连接到 Azure Cosmos DB 服务中的数据节点。 使用网关模式仅初始化和缓存逻辑地址，并在更新时进行刷新。 有关更多详细信息，请查看[连接模式](performance-tips.md#networking)一文。

* **ApplicationRegion** - 此选项用于设置首选异地复制区域，该区域用来与 Azure Cosmos DB 进行交互。 要了解详细信息，请查看[全球分发](distribute-data-globally.md)一文。

* **ConsistencyLevel** - 此选项用于替代默认一致性级别。 要了解详细信息，请查看[一致性级别](consistency-levels.md)一文。

* **BulkExecutionMode** - 此选项通过将 AllowBulkExecution 属性设置为 true 来执行批量操作。 若要了解详细信息，请参阅[运行作业](tutorial-sql-api-dotnet-bulk-import.md)文章。

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>预配容器

**DynamoDB**：

若要将数据存储到 Amazon DynamoDB 中，需要先创建表。 在此过程中，要定义架构、键类型和属性，如以下代码中所示：

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**：

在 Amazon DynamoDB 中，需要预配读取计算单位和写入计算单位。 而在 Azure Cosmos DB 中，则要将吞吐量指定为[请求单位 (RU/s)](request-units.md)，它可动态用于任何操作。 数据按“数据库”-->“容器”-->“项”的顺序整理。 你可在数据库级别和/或集合级别指定吞吐量。

若要创建数据库：

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

若要创建容器：

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>加载数据

**DynamoDB**：

以下代码演示了如何在 Amazon DynamoDB 中加载数据。 moviesArray 包含 JSON 文档的列表，你需要遍历它并将 JSON 文档加载到 Amazon DynamoDB 中：

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**：

在 Azure Cosmos DB，你可使用 `moviesContainer.CreateItemStreamAsync()` 选择进行流式传输和写入。 但在本例中，JSON 将反序列化为 MovieModel 类型，以演示类型强制转换功能。 代码是多线程的，这将使用 Azure Cosmos DB 的分布式体系结构并加快加载速度：

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>创建文档

**DynamoDB**：

在 Amazon DynamoDB 中写入新文档不是类型安全的，以下示例使用 newItem 作为文档类型：

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**：

Azure Cosmos DB 通过数据模型来确保类型安全。 我们使用名为“MovieModel”的数据模型：

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

在 Azure Cosmos DB 中，newItem 将成为 MovieModel：

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>读取文档

**DynamoDB**：

若要在 Amazon DynamoDB 中进行读取，需要定义基元：

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**：

但是，使用 Azure Cosmos DB 时，查询是一种自然查询 (linq)：

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

上例中的文档集合将：

- 是类型安全的
- 提供一个自然查询选项。

### <a name="update-an-item"></a>更新项

**DynamoDB**：更新 Amazon DynamoDB 中的项：

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**：

在 Azure Cosmos DB，更新将被看作是一项更新插入操作，即在没有文档时插入文档：

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>删除文档

**DynamoDB**：

若要删除 Amazon DynamoDB 中的项，需要再次使用基元：

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，可获取文档并异步删除它们：

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>查询文档

**DynamoDB**：

在 Amazon DynamoDB 中，需使用 API 函数来查询数据：

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，可在简单的 SQL 查询中进行投影和筛选：

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

对于范围操作（例如针对“介于”范围），需要在 Amazon DynamoDB 中进行扫描：

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

在 Azure Cosmos DB 中，可使用 SQL 查询和单行语句：

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>删除容器

**DynamoDB**：

若要删除 Amazon DynamoDB 中的表，可指定：

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**：

若要删除 Azure Cosmos DB 中的集合，可指定：

```csharp
await moviesContainer.DeleteContainerAsync();
```
需要时，还可删除数据库：

```csharp
await cosmosDatabase.DeleteAsync();
```

正如你所见，Azure Cosmos DB 支持自然查询 (SQL)、采用异步操作且操作简单得多。 你可将复杂的代码轻松迁移到 Azure Cosmos DB，代码在迁移后会变得简单。

### <a name="next-steps"></a>后续步骤

- 了解[性能优化](performance-tips.md)。
- 了解[优化读取和写入](key-value-store-cost.md)
- 了解 [Cosmos DB 中的监视](monitor-cosmos-db.md)

