---
title: 填充时间间隔和输入缺失值-Azure SQL Edge
description: 了解如何在 Azure SQL Edge 中填充时间间隔和输入缺失值
keywords: SQL 边缘，timeseries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 03896f4f7aa4e6efc78b498406e79a299318ed7a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933539"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>填充时间间隔和输入缺失值 

处理时序数据时，通常情况下，时序数据可能缺少属性值。 这也可能是由于数据的性质，或是由于数据收集中断，数据集中存在时间 *间隔* 。

例如，收集智能设备的能源使用统计信息时，如果设备不运行，则使用情况统计信息中会出现间隔。 同样，在计算机遥测数据收集方案中，可能会将不同的传感器配置为以不同的频率发出数据，从而导致传感器缺少值。 例如，如果有两个传感器，电压和压力，分别配置为 100 Hz 和 10 Hz 频率，则电压传感器将每隔1秒发送数据一次，而压力传感器将每隔10秒发出一次数据。

下表描述了在一秒的时间间隔内收集的计算机遥测数据集。 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

前面的数据集有两个重要特征。 

- 数据集不包含任何与多个时间戳 `2020-09-07 06:14:47.000` 、、 `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` 、 `2020-09-07 06:14:53.000` 和 `2020-09-07 06:14:55.000` 相关的数据点。 这些时间戳是数据集中的 *空白* 。  
- 对于电压和压力读数，缺少值（表示为 `null` ）。 

## <a name="gap-filling"></a>空白填充 

间隙填充是一种技术，有助于创建连续的、有序的时间戳集来简化时序数据分析。 在 Azure SQL Edge 中，若要在时序数据集中填充空白，最简单的方法是使用所需的时间分布定义临时表，然后 `LEFT OUTER JOIN` `RIGHT OUTER JOIN` 对数据集表执行或操作。 

`MachineTelemetry`使用上面表示的数据作为示例，可以使用以下查询来生成连续的、有序的时间戳集用于分析。 

> [!NOTE]
> 下面的查询将生成缺少的行，其中包含特性的时间戳值和 `null` 值。 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
上面的查询生成以下输出，其中包含指定范围内的所有 *一秒* 的时间戳。

下面是结果集

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>输入缺失值

上面的查询为数据分析生成了缺少的时间戳，但它并未替换任何缺失的值 (表示为和读数的 null) `voltage` `pressure` 。 在 Azure SQL Edge 中，T-sql 和函数中添加了一个新的语法， `LAST_VALUE()` `FIRST_VALUE()` 这些函数基于数据集中前面或后面的值提供归结缺失值的机制。 

新语法将 `IGNORE NULLS` and `RESPECT NULLS` 子句添加到 `LAST_VALUE()` 和 `FIRST_VALUE()` 函数。 以下针对 `MachineTelemetry` 数据集的查询使用 last_value 函数计算缺失值，其中缺失值将替换为数据集中的最后观察到的值。

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
下面是结果集

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

下面的查询使用和函数 imputes 缺失值 `LAST_VALUE()` `FIRST_VALUE` 。 对于，缺少值的输出列将 `ImputedVoltage` 替换为最后观测到的值，而对于输出列， `ImputedPressure` 缺失值将替换为数据集中的下一个观测值。 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
下面是结果集

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> 上面的查询使用 `FIRST_VALUE()` 函数将缺失值替换为下一个观察到的值。 可以通过将 `LAST_VALUE()` 函数与子句结合使用来实现相同的结果 `ORDER BY <ordering_column> DESC` 。

## <a name="next-steps"></a>后续步骤 

- [FIRST_VALUE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/first-value-transact-sql?toc=/azure/azure-sql-edge/toc.json)
- [LAST_VALUE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/last-value-transact-sql?toc=/azure/azure-sql-edge/toc.json)
- [DATE_BUCKET (Transact-sql) ](date-bucket-tsql.md)
- [聚合函数 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/aggregate-functions-transact-sql)
