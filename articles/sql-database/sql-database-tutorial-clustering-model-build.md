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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453091"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中生成聚类模型

在这个由三部分组成的教程系列的第二部分中，你将在 R 中构建一个 K-Means 模型来执行聚类分析。 在本系列的下一部分中，将使用 Azure SQL 数据库机器学习服务（预览版）在 SQL 数据库中部署此模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

本文将指导如何进行以下操作：

> [!div class="checklist"]
> * 定义 K-Means 算法的群集数
> * 执行聚类分析
> * 分析结果

在[第 1 部分](sql-database-tutorial-clustering-model-prepare-data.md)中，你已了解如何准备 Azure SQL 数据库中的数据，以执行聚类分析。

在[第 3 部分](sql-database-tutorial-clustering-model-deploy.md)中，你将了解如何在 Azure SQL 数据库中，创建一个可以使用 R 基于新数据执行聚类分析的存储过程。

## <a name="prerequisites"></a>先决条件

* 本教程的第二部分假定你已完成[**第一部分**](sql-database-tutorial-clustering-model-prepare-data.md)及其先决条件。

## <a name="define-the-number-of-clusters"></a>定义群集数

若要对客户数据进行聚类分析，需要使用 K-Means 聚类分析算法，这是最简单、最常见的数据分组方法之一  。
有关 K-Means 的详细信息，请参阅 [K-means 聚类分析算法的完整指南](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)。

该算法接受两个输入：数据本身，以及代表要生成的群集数的预定义数字“k”  。
输出是 K 个群集，输入数据在群集之间进行分区  。

要确定要使用的算法的群集数，请使用组内平方和的图，并按提取的群集数进行绘制。 要使用的适当群集数是在曲线的折弯处或“肘形”处。

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![肘形图](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

根据该图，看起来 k = 4 将是一个不错的尝试值  。 该 ķ 值将客户分组为四个群集  。

## <a name="perform-clustering"></a>执行聚类分析

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

使用[第一部分](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)中定义的变量给出四种群集平均值：

* orderRatio = 退单率（部分或全部退货的订单总数与订单总数的比率） 
* itemsRatio = 退货率（退货总数与购买商品数量的比率） 
* monetaryRatio = 退款率（退货的总货币金额与购买总金额的比率） 
* frequency = 退货频率 

使用 K-Means 进行数据挖掘通常需要对结果进行进一步的分析，并采取进一步的步骤以更好地理解每个群集，但是它可以提供一些优秀的潜在顾客。
下面通过以下几种方法来解释这些结果：

* 聚类 1（最大聚类）似乎是不太活跃的客户组（所有值均为零）。
* 群集 3 似乎是一个在返回行为方面出现的组。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续学习本教程，请从 Azure SQL 数据库服务器中删除 tpcxbb_1gb 数据库。

在 Azure 门户中执行以下步骤：

1. 从 Azure 门户的左侧菜单中，选择“所有资源”或“SQL 数据库”   。
1. 在“按名称筛选...”字段中输入 **tpcxbb_1gb**，然后选择你的订阅。 
1. 选择 **tpcxbb_1gb** 数据库。
1. 在“概览”  页上，选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程系列的第二部分中，你已完成这些步骤：

* 定义 K-Means 算法的群集数
* 执行聚类分析
* 分析结果

若要部署已创建的机器学习模型，请遵循本教程系列的第三部分：

> [!div class="nextstepaction"]
> [教程：使用 Azure SQL 数据库机器学习服务（预览版）在 R 中部署聚类模型](sql-database-tutorial-clustering-model-deploy.md)