---
title: 教程：使用 SQL 池分析数据入门
description: 在本教程中，你将使用纽约市出租车示例数据来探索 SQL 池的分析功能。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325213"
---
# <a name="analyze-data-with-sql-pools"></a>使用 SQL 池分析数据

Azure Synapse Analytics 为你提供使用 SQL 池分析数据的功能。 在本教程中，你将使用纽约市出租车示例数据来探索 SQL 池的分析功能。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>将纽约市出租车示例数据加载到 SQLDB1 数据库

1. 在 Synapse Studio 最顶部的蓝色菜单中，选择问号 (?) 图标。
1. 选择“入门” > “入门中心” 。
1. 在标有“查询示例数据”的卡中，选择名为 SQLDB1 的 SQL 池 。
1. 选择“查询数据”。 “正在加载示例数据”通知将短暂出现。 Synapse Studio 的顶部附近的淡蓝色状态栏指示正在将数据加载到 SQLDB1 中。
1. 在状态栏变为绿色后，请将其关闭。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>浏览 SQL 池中的纽约市出租车数据

1. 在 Synapse Studio 中，转到“数据”中心。
1. 转到“SQLDB1” > “表” 。 你将看到几个已加载的表。
1. 右键单击 dbo.Trip 表，然后选择“新建 SQL 脚本” > “选择前 100 行”  。
1. 等待新的 SQL 脚本创建并运行。
1. 请注意，在 SQL 脚本的顶部，“连接到”自动设置为名为“SQLDB1”的 SQL 池 。
1. 将 SQL 脚本的文本替换为此代码并运行。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    此查询显示总行程距离和平均行程距离与乘客数之间的关系。
1. 在“SQL 脚本结果”窗口中，将“视图”更改为“图表”，从而以折线图形式查看结果的可视化效果 。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Spark 进行分析](get-started-analyze-spark.md)

