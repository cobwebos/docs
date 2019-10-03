---
title: 教程：在 R 中部署聚类模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文是由三个部分组成的教程系列的第三篇文章，其中介绍了如何使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署聚类模型。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ms.openlocfilehash: 6f4d237d5e923aab61ae34a235d2e1f759399e6d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640905"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署聚类模型

在这个由三部分组成的教程系列的第三部分中，将使用 Azure SQL 数据库机器学习服务（预览版）将聚类分析模型（使用 R 开发）部署到 SQL 数据库中。

你将使用嵌入式 R 脚本创建一个可执行聚类的存储过程。 由于模型在 Azure SQL 数据库中执行，因此可用存储在数据库中的数据轻松地对其进行训练。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建生成模型的存储过程
> * 在 SQL 数据库中执行聚类
> * 使用聚类信息

在[第 1 部分](sql-database-tutorial-clustering-model-prepare-data.md)中，你已了解如何准备 Azure SQL 数据库中的数据，以执行聚类分析。

在[第二部分](sql-database-tutorial-clustering-model-build.md)中，你已了解如何使用 R 创建和训练 K-Means 群集模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* 本系列教程的第三部分假定你已完成[第一部分](sql-database-tutorial-clustering-model-prepare-data.md)和[第二部分](sql-database-tutorial-clustering-model-build.md)   。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>创建生成模型的存储过程

运行以下 T-SQL 脚本创建该存储过程。 该过程会重新创建在本教程系列的第一和第二部分制定的步骤：

* 根据客户的采购和退货历史记录将客户分类
* 使用 K 平均值算法生成客户的四个聚类

该过程将生成的客户聚类映射存储在数据库表 **customer_return_clusters** 中。

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>在 SQL 数据库中执行聚类

创建该存储过程后，请执行以下脚本来执行聚类。

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

确认该脚本是否正常运行，并实际返回了客户及其聚类映射的列表。

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>使用聚类信息

由于聚类过程已存储在数据库中，因此它可以针对同一个数据库中存储的客户数据的有效执行聚类。 每当更新客户数据和使用更新的聚类信息时，都可以执行该过程。

假设你要将促销电子邮件发送到聚类 3 中的客户，该组的退货行为较活跃（可以在[第二部分](sql-database-tutorial-clustering-model-build.md#analyze-the-results)了解四个聚类的描述方式）。 以下代码选择聚类 3 中客户的电子邮件地址。

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

可以更改 **r.cluster** 值，以返回其他聚类中客户的电子邮件地址。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以从 Azure SQL 数据库服务器中删除 tpcxbb_1gb 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中输入 **tpcxbb_1gb**，然后选择你的订阅。 
1. 选择 **tpcxbb_1gb** 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第三部分，你已完成以下步骤：

* 创建生成模型的存储过程
* 在 SQL 数据库中执行聚类
* 使用聚类信息

要了解有关在 Azure SQL 数据库机器学习服务（预览版）中使用 R 的详细信息，请参阅：

* [教程：准备数据以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中训练预测模型](sql-database-tutorial-predictive-model-prepare-data.md)
* [使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数](sql-database-machine-learning-services-functions.md)
* [在 Azure SQL 数据库机器学习服务中使用 R 和 SQL 数据（预览版）](sql-database-machine-learning-services-data-issues.md)
* [将 R 包添加到 Azure SQL 数据库机器学习服务（预览版）](sql-database-machine-learning-services-add-r-packages.md)
