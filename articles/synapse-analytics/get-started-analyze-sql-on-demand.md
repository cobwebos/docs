---
title: 教程：开始使用无服务器 SQL 分析数据
description: 在本教程中，你将了解如何通过按需 SQL 使用 Spark 数据库中的数据对数据进行分析。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 9c42d1d988bc280d5e62c24f109225d91cb446ce
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893486"
---
# <a name="analyze-data-with-sql-on-demand"></a>使用按需 SQL 分析数据

本教程介绍如何通过按需 SQL 池，使用无服务器 SQL 来分析 Spark 数据库中的数据。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>使用按需 SQL 池分析 Blob 存储中的纽约市出租车数据

1. 在“链接”下的“数据”中心中，右键单击“Azure Blob 存储”>“示例数据集”>“nyc_tlc_yellow”，然后选择“选择前 100 行”   
1. 这将会使用以下代码创建新 SQL 脚本：

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. 单击“**运行**”

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
> [分析存储中的数据](get-started-analyze-storage.md)
