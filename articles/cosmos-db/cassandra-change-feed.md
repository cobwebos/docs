---
title: Azure Cosmos DB API for Cassandra 中的更改源
description: 了解如何使用 Azure Cosmos DB API for Cassandra 中的更改源来获取对数据所做的更改。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 43743f62b08bb00403f5dac88682d06daab757a4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872560"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Azure Cosmos DB API for Cassandra 中的更改源

Azure Cosmos DB API for Cassandra 中的[更改源](change-feed.md)支持通过 Cassandra 查询语言 (CQL) 中的查询谓词提供。 使用这些谓词条件可以查询更改源 API。 应用程序可以使用 CQL 中必需的主键（也称为分区键）来获取对表所做的更改。 然后，可以根据结果采取进一步的措施。 对表中的行所做的更改将按照其修改时间顺序捕获，而排序顺序是按分区键提供保证的。

以下示例演示如何使用 .NET 获取 Cassandra API 密钥空间表中所有行上的更改源。 直接在 CQL 中使用谓词 COSMOS_CHANGEFEED_START_TIME()，以从指定的开始时间（在本例中为当前日期时间）查询更改源中的项。 可[在此处下载适用于 c #](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)的完整示例，并在[此处](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)下载 Java。

在每个迭代中，查询将使用分页状态从上次读取更改的时间点恢复。 可以看到，新的更改不断地流式传输到密钥空间中的表。 我们将会看到对已插入或更新的行所做的更改。 目前不支持使用 Cassandra API 中的更改源来监视删除操作。

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```
---

若要按主键获取对单个行所做的更改，可以在查询中添加主键。 以下示例演示如何跟踪指定了“user_id = 1”的行的更改。

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>当前限制

使用 Cassandra API 的更改源时，以下限制适用：

* 目前支持插入和更新操作。 尚不支持删除操作。 一种解决方法是，对正在删除的行添加一个软标记。 例如，在名为“deleted”的行中添加一个字段，并将其设置为“true”。
* 与在核心 SQL API 中一样，上次更新将会保留；对实体的中间性更新将不可用。


## <a name="error-handling"></a>错误处理。

使用 Cassandra API 中的更改源时，支持以下错误代码和消息：

* **HTTP 错误代码 429** - 当更改源的速率受到限制时，它会返回一个空页。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 资源管理器模板管理 Azure Cosmos DB Cassandra API 资源](manage-cassandra-with-resource-manager.md)
