---
title: 教程：使用 R 生成聚类分析模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文是由三个部分组成的教程系列的第二部分，其中介绍了如何生成一个 K 平均值模型，以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中执行聚类分析。
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
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420225"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中生成聚类模型

本文是由三个部分组成的教程系列的第二部分，其中介绍了如何生成一个 K 平均值模型，以使用 Azure SQL 数据库机器学习服务（预览版）在 R 中执行聚类分析。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 定义 K 平均值算法的聚类数
> * 执行聚类
> * 分析结果

在[第 1 部分](sql-database-tutorial-clustering-model-prepare-data.md)，你已了解如何准备 Azure SQL 数据库中的数据，以在 R 中执行聚类。

在[第 3 部分](sql-database-tutorial-clustering-model-deploy.md)，你将了解如何在 Azure SQL 数据库中，创建一个可以基于新数据执行聚类的存储过程。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

* 本教程的第二部分假定你已完成[**第一部分**](sql-database-tutorial-clustering-model-prepare-data.md)及其先决条件。

## <a name="define-the-number-of-clusters"></a>定义聚类数

若要聚类客户数据，可以使用“K 平均值”聚类算法 - 最简单、最有名的数据分组方式。 
可以在 [K 平均值聚类算法完整指南](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)中详细了解 K 平均值。

该算法接受两种输入：数据本身，以及预定义的数字“*k*”（代表要生成的聚类数）。
输出为 *k* 个聚类，其中包含已在聚类之间分区的输入数据。

若要确定算法使用的聚类数，请使用组内平方和乘以提取的聚类数绘图。 要使用的适当聚类数位于绘图的转折或“拐弯”处。

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![拐弯图](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

根据图形，*k = 4* 似乎是可以尝试的适当值。 该 *k* 值会将客户分组为四个聚类。

## <a name="perform-clustering"></a>执行聚类

以下 R 脚本使用函数 **rxKmeans**，即 RevoScaleR 包中的 K 平均值函数。

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>分析结果

使用 K 平均值完成聚类后，下一步是分析结果，并看看是否可以找到任何可行的信息。

**clust** 对象包含 K 平均值聚类的结果。

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

使用[第 1 部分](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)中定义的变量可获得四个聚类平均值：

* *orderRatio* = 退货订单率（部分退货或全部退货的订单总数与订单总数之比）
* *itemsRatio* = 退货率（退货总数与采购货品数之比）
* *monetaryRatio* = 退货金额率（退货的总货币金额与采购金额之比）
* *frequency* = 退货频率

使用 K 平均值的数据挖掘经常需要进一步分析结果，并采取更多的步骤来更好地了解每个聚类，但可以提供一些很好的线索。
可通过以下几种方式来解释这些结果：

* 聚类 1（最大聚类）似乎是不太活跃的客户组（所有值均为零）。
* 聚类 3 似乎是在退货行为方面较为突出的组。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续学习本教程，请从 Azure SQL 数据库服务器中删除 tpcxbb_1gb 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中输入 **tpcxbb_1gb**，然后选择你的订阅。 
1. 选择 **tpcxbb_1gb** 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第二部分，你已完成以下步骤：

* 定义 K 平均值算法的聚类数
* 执行聚类
* 分析结果

若要部署已创建的机器学习模型，请遵循本教程系列的第三部分：

> [!div class="nextstepaction"]
> [教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署聚类模型](sql-database-tutorial-clustering-model-deploy.md)