---
title: 教程：使用按需 SQL 分析数据入门
description: 在本教程中，你将了解如何通过按需 SQL 使用 Spark 数据库中的数据对数据进行分析。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093227"
---
# <a name="analyze-data-with-sql-on-demand"></a>使用按需 SQL 分析数据

在本教程中，你将了解如何通过按需 SQL 使用 Spark 数据库中的数据对数据进行分析。 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 按需版本分析 Spark 数据库中的纽约市出租车数据

Spark 数据库中的表会自动显示，且 SQL on-demand 可以查询这些表。

1. 在 Synapse Studio 中，转到“开发”中心，然后新建 SQL 脚本。
1. 将“连接到”设置为“SQL 按需版本” 。
1. 将以下文本粘贴到脚本中，然后运行该脚本。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 第一次运行使用 SQL 按需版本的查询时，SQL 按需版本需要大约 10 秒的时间来收集运行查询所需的 SQL 资源。 后续查询的速度要快得多。
  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 spark 进行分析](get-started-analyze-spark.md)
