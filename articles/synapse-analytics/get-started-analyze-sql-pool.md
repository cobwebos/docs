---
title: 教程：开始使用专用 SQL 池分析数据
description: 在本教程中，你将使用纽约市出租车示例数据来探索 SQL 池的分析功能。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3df0959003923a9a4ff2347330fd5a4f22e52245
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300116"
---
# <a name="analyze-data-with-sql-dedicated-pools"></a>使用 SQL 专用池分析数据

Azure Synapse Analytics 为你提供使用专用 SQL 池分析数据的功能。 在本教程中，你将使用纽约市出租车数据来探索专用 SQL 池的功能。

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>将纽约市出租车数据加载到 SQLDB1

1. 在 Synapse Studio 中，导航到“开发”中心，然后新建 SQL 脚本
1. 输入以下代码：
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. 此脚本大约需要 1 分钟的运行时间。 它将 2 百万行纽约市出租车数据加载到一个名为 dbo.Trip 的表中

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>浏览专用 SQL 池中的纽约市出租车数据

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

