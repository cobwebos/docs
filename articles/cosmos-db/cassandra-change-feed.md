---
title: 更改 Cassandra 的 Azure Cosmos DB API 中的源
description: 了解如何使用 Cassandra API for Azure Cosmos DB 中的更改源来获取对数据所做的更改。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694618"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>更改 Cassandra 的 Azure Cosmos DB API 中的源

Azure Cosmos DB API for Cassandra 中的[更改源](change-feed.md)支持通过 Cassandra 查询语言（CQL）中的查询谓词提供。 使用这些谓词条件，可以查询更改源 API。 应用程序可以使用 CQL 中所需的主键（也称为分区键）来获取对表所做的更改。 然后，你可以根据结果采取进一步操作。 对表中的行所做的更改将按照其修改时间的顺序进行捕获，并按分区键保证排序顺序。

下面的示例演示如何使用 .NET 获取 Cassandra API 密钥空间表中所有行的更改源。 谓词 COSMOS_CHANGEFEED_START_TIME （）直接在 CQL 中用于从指定的开始时间（在本例中为当前日期时间）查询更改源中的项。 可在[此处](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)下载完整示例。

在每次迭代中，将在读取最后一个点时恢复查询，并使用分页状态。 对于密钥空间中的表，我们可以看到不断变化的新更改流。 我们将看到对插入或更新的行所做的更改。 当前不支持在 Cassandra API 中使用更改源监视删除操作。 

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

若要按主键获取对单个行所做的更改，可以在查询中添加主键。 下面的示例演示如何跟踪行（其中 "user_id = 1"）的更改。

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>当前限制

将更改源与 Cassandra API 结合使用时，以下限制适用：

* 当前支持插入和更新。 尚不支持删除操作。 作为一种解决方法，你可以在要删除的行上添加一个软标记。 例如，在名为 "deleted" 的行中添加一个字段，并将其设置为 "true"。
* 上次更新是在核心 SQL API 中保留的，对实体的中间更新不可用。


## <a name="error-handling"></a>错误处理。

在 Cassandra API 中使用更改源时，支持以下错误代码和消息：

* **HTTP 错误代码 429** -更改源的速率限制时，会返回一个空页。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 资源管理器模板管理 Azure Cosmos DB Cassandra API 资源](manage-cassandra-with-resource-manager.md)
