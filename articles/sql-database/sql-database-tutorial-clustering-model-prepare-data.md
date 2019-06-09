---
title: 教程：准备数据以在 R 中执行聚类
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文是由三个部分组成的教程系列的第一部分，其中介绍了如何准备 Azure SQL 数据库中的数据，以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中执行聚类。
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
ms.date: 05/17/2019
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419795"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：准备数据以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中执行聚类

本文是由三个部分组成的教程系列的第一部分，其中介绍了如何准备 Azure SQL 数据库中的数据，以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中执行聚类。

“聚类”是指将数据组织成组，其中的成员在某个方面具有类似性。 
你将使用“K 平均值”算法针对某个产品采购和退货数据集中的客户执行聚类。  将客户聚类之后，可以专门针对特定的组，以更有效的方式展开营销活动。
K 平均值聚类是一种非监督式学习算法，它会在数据中根据类似性查找模式。 

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 将示例数据库导入 Azure SQL 数据库
> * 在不同的维上分隔客户
> * 使用 R 将 Azure SQL 数据库中的数据加载到数据帧中

在[第二部分](sql-database-tutorial-clustering-model-build.md)，你将了解如何创建和训练 K 平均值聚类模型。

在[第 3 部分](sql-database-tutorial-clustering-model-deploy.md)，你将了解如何在 Azure SQL 数据库中，创建一个可以基于新数据执行聚类的存储过程。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - 如果没有 Azure 订阅，请在开始前[创建帐户](https://azure.microsoft.com/free/)。

* 启用了机器学习服务的 Azure SQL 数据库服务器 - 在公共预览版期间，Microsoft 会将你加入预览并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

* RevoScaleR 包 - 有关在本地安装此包的选项，请参阅 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms)。

* R IDE - 此教程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* SQL 查询工具 - 此教程假设使用的是 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="import-the-sample-database"></a>导入示例数据库

本教程中使用的示例数据集已保存到 .bacpac 数据库备份文件中，可供下载和使用  。 此数据集派生自[事务处理性能委员会 (TPC)](http://www.tpc.org/default.asp) 提供的 [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) 数据集。

1. 下载文件 [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac)。

1. 按照[导入 BACPAC 文件以创建 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-import)中的说明执行操作，并使用以下详细信息：

   * 从下载的 **tpcxbb_1gb.bacpac** 文件导入数据
   * 在公共预览版中，为新数据库选择 Gen5/vCore 配置 
   * 将新数据库命名为“tpcxbb_1gb”

## <a name="separate-customers"></a>分隔客户

在 RStudio 中创建新的 RScript 文件并运行以下脚本。
在 SQL 查询中，在以下维上分隔客户：

* **orderRatio** = 退货订单率（部分退货或全部退货的订单总数与订单总数之比）
* **itemsRatio** = 退货率（退货总数与采购货品数之比）
* **monetaryRatio** = 退货金额率（退货的总货币金额与采购金额之比）
* **frequency** = 退货频率

在 **paste** 函数中，将 **Server**、**UID** 和 **PWD** 替换为你自己的连接信息。

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>将数据加载到数据帧

现在，运行以下脚本以使用 **rxSqlServerData** 函数将查询结果返回给 R 数据帧。
在此过程中，需要定义所选列的类型（使用 colClasses），以确保将类型正确传输到 R。

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

应看到如下所示的结果。

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续学习本教程，请从 Azure SQL 数据库服务器中删除 tpcxbb_1gb 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中输入 **tpcxbb_1gb**，然后选择你的订阅。 
1. 选择 **tpcxbb_1gb** 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第一部分，你已完成以下步骤：

* 将示例数据库导入 Azure SQL 数据库
* 在不同的维上分隔客户
* 使用 R 将 Azure SQL 数据库中的数据加载到数据帧中

若要创建使用此客户数据的机器学习模型，请参阅本教程系列的第二部分：

> [!div class="nextstepaction"]
> [教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中创建预测模型](sql-database-tutorial-clustering-model-build.md)
