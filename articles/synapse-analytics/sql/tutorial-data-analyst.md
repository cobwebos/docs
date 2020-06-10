---
title: 数据分析师教程：在 Azure Synapse Studio（预览版）中使用 SQL 按需版本（预览版）分析 Azure 开放数据集
description: 本教程介绍如何在 Azure Synapse Studio 中使用 SQL 按需版本（预览版）来组合不同的 Azure 开放数据集，从而轻松执行探索数据分析，然后将结果可视化。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 84e808caa033491ce3f2da099459d1242df6decd
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299530"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>在 Azure Synapse Studio（预览版）中使用 SQL 按需版本（预览版）分析 Azure 开放数据集并将结果可视化

本教程介绍如何在 Azure Synapse Studio 中使用 SQL 按需版本来组合不同的 Azure 开放数据集，从而执行探索数据分析，然后将结果可视化。

具体而言，你会分析[纽约市 (NYC) 出租车数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)，其中包括：

- 上车/下车日期和时间。
- 上车/下车地点。 
- 行程距离。
- 费用明细。
- 费率类型。
- 支付类型。 
- 驾驶员报告的乘客计数。

## <a name="automatic-schema-inference"></a>自动架构推理

由于数据以 Parquet 文件格式存储，因此可以使用自动架构推理。 无需列出文件中所有列的数据类型，就能轻松查询数据。 还可以使用虚拟列机制和 filepath 函数筛选出特定的文件子集。

首先，通过运行以下查询来熟悉 NYC 出租车数据：

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

以下片段显示了 NYC 出租车数据的结果：

![NYC 出租车数据结果片段](./media/tutorial-data-analyst/1.png)

同样，你可以通过使用以下查询来查询公共节假日数据集：

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

以下片段显示了公共节假日数据集的结果：

![公共节假日数据集结果片段](./media/tutorial-data-analyst/2.png)

最后，还可以使用以下查询来查询天气数据数据集：

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

以下片段显示了“天气数据”数据集的结果：

![“天气数据”数据集结果片段](./media/tutorial-data-analyst/3.png)

可以在 [NYC 出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)、[公共节假日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)和[天气数据](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)数据集的说明中详细了解各个列的含义。

## <a name="time-series-seasonality-and-outlier-analysis"></a>时序、季节性和离群值分析

可通过使用以下查询轻松汇总每年的出租车搭乘次数：

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

以下片段显示了每年出租车搭乘次数的结果：

![每年出租车搭乘次数结果片段](./media/tutorial-data-analyst/4.png)

在 Synapse Studio 中，可以通过从“表”视图切换到“图表”视图来可视化数据 。 可以在不同的图表类型（如面积图、条形图、柱形图、折线图、饼图和散点图）之间进行选择     。 在本例中，请在“类别”列设置为“current_year”的情况下绘制柱形图  ：

![每年搭乘次数的柱形图](./media/tutorial-data-analyst/5.png)

在此可视化效果中可以清楚地看出，搭乘次数的趋势为逐年下降。 这种下降可能是由于近年来拼车公司越来越受到欢迎所导致的。

> [!NOTE]
> 编写本教程时，2019 年的数据尚不完整。 因此，这一年的搭乘次数大幅下降。

接下来，让我们专注于分析某个年份，例如 2016 年。 以下查询返回该年份的每日搭乘次数：

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

以下片段显示了此查询的结果：

![2016 年每日搭乘次数结果片段](./media/tutorial-data-analyst/6.png)

同样，你可以在“类别”列设置为“current_day”以及“图例（系列）”列设置为“rides_per_day”的情况下绘制柱形图，以轻松地将数据可视化    。

![2016 年每日搭乘次数的柱形图](./media/tutorial-data-analyst/7.png)

在绘图图表中，有一个每周走势，其中星期六为高峰日。 在夏季的几个月份，由于出现了长假，出租车搭乘次数较少。 出租车搭乘次数还出现了几次明显的下降，而且没有明确的模式解释发生这种情况的时间和原因。

接下来，让我们将 NYC 出租车搭乘数据集与公共节假日数据集相联接，来确定这种下降是否与公共节假日相关联：

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC 出租车搭乘和公共节假日数据集结果可视化效果](./media/tutorial-data-analyst/8.png)

这一次，我们希望突出显示公共节假日期间的出租车搭乘次数。 为此，我们对“类别”列选择“none”，对“图例（系列）”列选择“rides_per_day”和“holiday”    。

![公共节假日期间出租车搭乘次数图表](./media/tutorial-data-analyst/9.png)

从绘图图表中可以看出，在公共节假日期间，出租车搭乘次数较少。 1 月 23 日仍然出现了一次原因不明的大幅下降。 让我们通过查询天气数据数据集来检查 NYC 在那一天的天气：

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![天气数据数据集结果可视化效果](./media/tutorial-data-analyst/10.png)

查询结果表明，以下原因导致出租车搭乘次数下降：

- NYC 在那一天出现暴风雪天气，雪非常厚（约有 30 厘米）。
- 天气非常寒冷（温度低于零摄氏度）。
- 风非常大（风速大约 10 米/秒）。

本教程介绍了数据分析师如何快速执行探索数据分析，通过使用 SQL 按需版本轻松组合不同的数据集，以及如何通过使用 Azure Synapse Studio 将结果可视化。

## <a name="next-steps"></a>后续步骤

若要了解如何将 SQL 按需版本连接到 Power BI Desktop 并创建报表，请参阅[将 SQL 按需版本连接到 Power BI Desktop 并创建报表](tutorial-connect-power-bi-desktop.md)。
 
