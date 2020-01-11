---
title: 将一对多关系数据迁移到 Azure Cosmos DB SQL API
description: 了解如何在 SQL API 中处理对一对多关系的复杂数据迁移
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896631"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>将一对多关系数据迁移到 Azure Cosmos DB SQL API 帐户

为了从关系数据库迁移到 Azure Cosmos DB SQL API，可能需要对数据模型进行更改以进行优化。

一个常见的转换是通过将相关子项嵌入到一个 JSON 文档中来非规范化数据。 在这里，我们将使用 Azure 数据工厂或 Azure Databricks 来查看几个选项。 有关 Cosmos DB 的数据建模的一般指南，请查看[Azure Cosmos DB 中的数据建模](modeling-data.md)。  

## <a name="example-scenario"></a>示例方案

假设我们在 SQL 数据库中有以下两个表： Orders 和 OrderDetails。


![订单详细信息](./media/migrate-relational-to-cosmos-sql-api/orders.png)

我们想要在迁移期间将此一对多关系合并到一个 JSON 文档中。 为此，我们可以使用 "FOR JSON" 创建 T-sql 查询，如下所示：

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

此查询的结果如下所示： 

![订单详细信息](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


理想情况下，你需要使用单个 Azure 数据工厂（ADF）复制活动将 SQL 数据作为源进行查询，并将输出直接写入 Azure Cosmos DB 接收器作为正确的 JSON 对象。 目前，不能在一个复制活动中执行所需的 JSON 转换。 如果尝试将上述查询的结果复制到 Azure Cosmos DB SQL API 容器，我们将看到 OrderDetails 字段作为文档的字符串属性，而不是预期的 JSON 数组。

可以通过以下方式之一来解决此当前限制：

* **使用包含两个复制活动的 Azure 数据工厂**： 
  1. 从 SQL 中获取 JSON 格式的数据到中间 blob 存储位置的文本文件中，并 
  2. 将数据从 JSON 文本文件加载到 Azure Cosmos DB 中的容器。

* **使用 Azure Databricks 从 SQL 中读取并写入 Azure Cosmos DB** -我们将在此处提供两个选项。


让我们更详细地了解这些方法：

## <a name="azure-data-factory"></a>Azure 数据工厂

尽管我们不能将 OrderDetails 作为 JSON 数组嵌入到目标 Cosmos DB 文档中，但可以使用两个单独的复制活动来解决该问题。

### <a name="copy-activity-1-sqljsontoblobtext"></a>复制活动 #1： SqlJsonToBlobText

对于源数据，我们使用 SQL 查询以单个列的形式获取结果集，每个行包含一个使用 SQL Server OPENJSON 和 JSON 路径功能的 JSON 对象（表示顺序）：

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


对于 SqlJsonToBlobText 复制活动的接收器，我们选择 "分隔文本"，并将其指向 Azure Blob 存储中的特定文件夹，并动态生成唯一的文件名（例如，"@concat（管道（）。RunId "。
由于我们的文本文件并不真正 "分隔"，我们不希望使用逗号将其解析为单独的列，并且想要保留双引号（"），我们将" 列分隔符 "设置为选项卡（" \t "），或者在数据中出现其他字符和" 引号字符 " 为 "无引号字符"。

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>复制活动 #2： BlobJsonToCosmos

接下来，我们将通过为第一个活动所创建的文本文件添加在 Azure Blob 存储中查找的第二个复制活动来修改 ADF 管道。 它将其作为 "JSON" 源处理，以在文本文件中每个 JSON 行的一个文档中插入 Cosmos DB 接收器。

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

另外，我们还将 "删除" 活动添加到管道，以便在每次运行之前删除/Orders/文件夹中剩余的所有以前的文件。 ADF 管道现在如下所示：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

在我们触发了上述管道后，我们会看到一个文件，该文件在中间 Azure Blob 存储位置中创建，其中每行包含一个 JSON 对象：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

我们还会看到订单文档，并将正确嵌入的 OrderDetails 插入到 Cosmos DB 集合中：

![ADF 复制](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

我们还可以在[Azure Databricks](https://azure.microsoft.com/services/databricks/)中使用 Spark，将 SQL 数据库源中的数据复制到 Azure Cosmos DB 目标，而无需在 Azure Blob 存储中创建中间文本/JSON 文件。 

> [!NOTE]
> 为清楚起见，以下代码片段中的代码片段显式嵌入了虚拟数据库密码，但你应始终使用 Azure Databricks 的机密。
>

首先，创建所需的[SQL 连接器](https://docs.databricks.com/data/data-sources/sql-databases-azure.html)并将[Azure Cosmos DB 连接器](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html)库连接到 Azure Databricks 群集。 重新启动群集以确保加载库。

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

接下来，我们为 Scala 和 Python 提供了两个示例。 

### <a name="scala"></a>Scala
在这里，我们将使用 "FOR JSON" 输出获取 SQL 查询的结果：

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

接下来，我们将连接到 Cosmos DB 数据库和集合：

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

最后，定义架构，并在将数据帧保存到 CosmosDB 集合之前，使用 from_json 应用该架构。

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

作为替代方法，你可能需要在 Spark 中执行 JSON 转换（如果源数据库不支持 "FOR JSON" 或类似的操作），或者你可能希望对非常大的数据集使用并行操作。 这里提供了一个 PySpark 示例。 首先配置第一个单元中的源数据库和目标数据库连接：

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

接下来，我们将在源数据库（在本例中为 SQL Server）查询订单和订单详细信息记录，并将结果放入 Spark Dataframes。 我们还将创建一个列表，其中包含所有订单 Id 和并行操作的线程池：

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

然后，创建一个函数用于将订单写入目标 SQL API 集合。 此函数将筛选给定订单 ID 的所有订单详细信息，将其转换为 JSON 数组，然后将该数组插入 JSON 文档中，该文档将写入到目标 SQL API 集合中以实现此顺序：

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

最后，我们将使用线程池上的 map 函数调用上面的，以便并行执行，并传入前面创建的顺序 Id 列表：

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
在这两种方法中，最终都应在 Cosmos DB 集合中的每个订单文档中正确保存嵌入的 OrderDetails：

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>后续步骤
* 了解[Azure Cosmos DB 中的数据建模](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* 了解[如何在 Azure Cosmos DB 上对数据进行建模和分区](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
