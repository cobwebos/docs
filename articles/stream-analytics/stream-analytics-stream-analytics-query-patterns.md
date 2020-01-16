---
title: Azure 流分析中的常见查询模式
description: 本文介绍了几种可在 Azure 流分析作业中发挥作用的常见查询模式和设计。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982317"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure 流分析中的常见查询模式

Azure 流分析中的查询以类似 SQL 的查询语言表示。 这些语言构造记录在[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查询设计可以表达简单的传递逻辑，以将事件数据从一个输入流移动到一个输出数据存储，或者可以执行丰富的模式匹配和临时分析来计算各种时间窗口的聚合，就像在[使用流分析构建 IoT 解决方案](stream-analytics-build-an-iot-solution-using-stream-analytics.md)指南中一样。 您可以联接多个输入中的数据以合并流式处理事件，并且可以对静态引用数据执行查找以丰富事件值。 你还可以将数据写入多个输出。

本文概述了基于实际方案的多个常见查询模式的解决方案。

## <a name="supported-data-formats"></a>支持的数据格式

Azure 流分析支持处理采用 CSV、JSON 和 Avro 数据格式的事件。

JSON 和 Avro 都可能包含嵌套对象（记录）或数组等复杂类型。 有关使用这些复杂数据类型的详细信息，请参阅[分析 JSON 和 AVRO 数据](stream-analytics-parsing-json.md)一文。

## <a name="simple-pass-through-query"></a>简单的传递查询

简单的传递查询可用于将输入流数据复制到输出中。 例如，如果需要将包含实时车辆信息的数据流保存在 SQL 数据库中以进行信件分析，则简单的传递查询会执行该作业。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**输出**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**查询**：

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 查询会投影传入事件的所有字段，并将其发送到输出。 同样，也可以**选择**仅将所需字段用于输入。 在此示例中，如果只*保存车辆和* *时间*，则可以在**SELECT**语句中指定这些字段。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |1000 |
| Make1 |2015-01-01T00：00： 02.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |1500 |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 04.0000000 Z |

**查询**：

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>随时间推移的数据聚合

若要计算某个时间范围内的信息，可以将数据聚合在一起。 在此示例中，将在过去10分钟内为每个特定汽车发出计算一次计数。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |1000 |
| Make1 |2015-01-01T00：00： 02.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |1500 |

**输出**：

| 制造商 | 计数 |
| --- | --- |
| Make1 | 2 |
| Make2 | 第 |

**查询**：

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

此聚合*将*汽车分组，并每10秒计算一次。 输出包含经过收费的汽车的*品牌*和*计数*。

TumblingWindow 是一种用于将事件组合在一起的窗口化函数。 聚合可以应用于所有分组事件。 有关详细信息，请参阅[窗口函数](stream-analytics-window-functions.md)。

有关聚合的详细信息，请参阅[聚合函数](/stream-analytics-query/aggregate-functions-azure-stream-analytics)。

## <a name="data-conversion"></a>数据转换

数据可以使用**转换**方法进行实时强制转换。 例如，可以将 car 权重从类型**nvarchar （max）** 转换为类型**bigint** ，并用于数字计算。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**输出**：

| 制造商 | 重量 |
| --- | --- |
| Make1 |3000 |

**查询**：

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

使用**CAST**语句指定其数据类型。 请参阅[数据类型（Azure 流分析）](/stream-analytics-query/data-types-azure-stream-analytics)中支持的数据类型列表。

有关[数据转换函数](/stream-analytics-query/conversion-functions-azure-stream-analytics)的详细信息。

## <a name="string-matching-with-like-and-not-like"></a>采用 LIKE 和 NOT LIKE 的字符串匹配

**Like**和**NOT LIKE**可用于验证字段是否与特定模式匹配。 例如，可以创建一个筛选器，以仅返回以字母 "A" 开头的许可印版，以数字9结尾。

**输入**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00：00： 03.0000000 Z |

**输出**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00：00： 03.0000000 Z |

**查询**：

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

使用**LIKE**语句检查 " **License_plate** " 字段值。 它应以字母 "A" 开头，并具有零个或多个字符的任何字符串，以数字9结尾。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>指定不同案例/值的逻辑（CASE 语句）

**CASE**语句可以根据特定条件为不同字段提供不同的计算结果。 例如，将通道 "A" 分配给*Make1*的轿车，将通道 "B" 分配给任何其他品牌。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**输出**：

| 制造商 |Dispatch_to_lane | 时间 |
| --- | --- | --- |
| Make1 |的 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |B |2015-01-01T00：00： 02.0000000 Z |

**解决方案**；

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE**表达式将表达式与一组简单表达式进行比较以确定其结果。 在此示例中， *Make1*的车辆将被调度到通道 "A"，而其他任何品牌的车辆都将分配给通道 "B"。

有关详细信息，请参阅[case 表达式](/stream-analytics-query/case-azure-stream-analytics)。

## <a name="send-data-to-multiple-outputs"></a>将数据发送到多个输出

可以使用多个**SELECT**语句将数据输出到不同的输出接收器。 例如，**选择**时可以输出基于阈值的警报，而另一个则可将事件输出到 blob 存储。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**输出 ArchiveOutput**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**输出 AlertOutput**：

| 制造商 | 时间 | 计数 |
| --- | --- | --- |
| Make2 |2015-01-01T00：00： 10.0000000 Z |3 |

**查询**：

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO**子句告知流分析要将数据写入其中的输出。 第一个**选择**定义一个传递查询，该查询接收来自输入的数据并将其发送到名为**ArchiveOutput**的输出。 第二个查询执行一些简单的聚合和筛选，然后将结果发送到名为**AlertOutput**的下游警报系统输出。

请注意，可以使用**WITH**子句来定义多个子查询块。 此选项具有向输入源打开较少的读取器的好处。

**查询**：

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

有关详细信息，请参阅[ **WITH**子句](/stream-analytics-query/with-azure-stream-analytics)。

## <a name="count-unique-values"></a>对唯一值进行计数

**Count**和**DISTINCT**可用于计算在某个时间范围内流中显示的唯一字段值的数目。 可以创建一个查询，以计算在2秒的时间段内通过收费亭*传递的汽车的唯一数目*。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**输出：**

| Count_make | 时间 |
| --- | --- |
| 2 |2015-01-01T00：00： 02.000 Z |
| 第 |2015-01-01T00：00： 04.000 Z |

**查询：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count （DISTINCT Make）** 返回时间范围内的 " **Make** " 列中非重复值的计数。
有关详细信息，请参阅[ **COUNT**聚合函数](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="calculation-over-past-events"></a>计算过去的事件

**LAG**函数可用于查看某个时间范围内的过去事件，并将其与当前事件进行比较。 例如，如果当前汽车品牌与通过了收费的最后一辆汽车不同，则可以输出。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| Make2 |2015-01-01T00：00： 02.0000000 Z |

**查询**：

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

使用**LAG**可查看一个事件返回到的输入流，检索*make*值并将其与当前事件的 "设置" 值*进行*比较并输出该事件。

有关详细信息，请参阅[**滞后**](/stream-analytics-query/lag-azure-stream-analytics)时间。

## <a name="retrieve-the-first-event-in-a-window"></a>检索窗口中的第一个事件

**IsFirst**可用于检索时间范围内的第一个事件。 例如，按每10分钟时间间隔输出第一辆车信息。

**输入**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00：05： 01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |

**查询**：

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst**还可以对数据进行分区，并按每隔10分钟的时间间隔计算每个*特定汽车的*第一个事件。

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**查询**：

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

有关详细信息，请参阅[**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)。

## <a name="return-the-last-event-in-a-window"></a>返回窗口中的最后一个事件

当系统实时使用事件时，没有可用于确定事件是否将成为该时间窗口最后一个事件的函数。 若要实现此目的，输入流需要与另一个输入流联接，其中事件的时间是该窗口中所有事件的最长时间。

**输入**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00：05： 01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**查询**：

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

查询的第一步是在10分钟的窗口中查找最大时间戳，这是该窗口的最后一个事件的时间戳。 第二个步骤将第一个查询的结果与原始流联接起来，以查找与每个窗口中的最后一个时间戳相匹配的事件。 

**DATEDIFF**是特定于日期的函数，用于比较并返回两个日期时间字段之间的时间差。有关详细信息，请参阅[日期函数](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)。

有关联接流的详细信息，请参阅[**JOIN**](/stream-analytics-query/join-azure-stream-analytics)。


## <a name="correlate-events-in-a-stream"></a>关联流中的事件

通过使用**LAG**函数查看过去的事件，可以完成同一流中的事件关联。 例如，每次同一次中的两个连续汽车在过去90秒*内完成了*收费时，就可以生成输出。

**输入**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00：00： 03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00：00： 04.0000000 Z |

**输出**：

| 制造商 | 时间 | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00：00： 02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |

**查询**：

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG**函数可查看一个事件返回到输入流，并检索 " *make* " 值，并将其与当前事件的 "设置" 值*进行*比较。  满足条件后，可以使用**SELECT**语句中的**LAG**来投影上一个事件中的数据。

有关详细信息，请参阅[滞后](/stream-analytics-query/lag-azure-stream-analytics)时间。

## <a name="detect-the-duration-between-events"></a>检测事件之间的持续时间

接收到结束事件后，可以通过查看上一个开始事件来计算事件的持续时间。 此查询可用于确定用户在页面或功能上所花费的时间。

**输入**：  

| 用户 | 功能 | 事件 | 时间 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |开始 |2015-01-01T00：00： 01.0000000 Z |
| user@location.com |RightMenu |结束 |2015-01-01T00：00： 08.0000000 Z |

**输出**：  

| 用户 | 功能 | 持续时间 |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**查询**：

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**Last**函数可用于检索特定条件中的最后一个事件。 在此示例中，条件是类型为 Start 的事件，按**分区按**用户和功能对搜索进行分区。 这样一来，搜索开始事件时，将单独处理每个用户和功能。 **限制持续**时间将在结束事件和开始事件之间向后搜索时间限制为1小时。

## <a name="detect-the-duration-of-a-condition"></a>检测某个条件的持续时间

对于跨多个事件的条件， **LAG**函数可用于标识该条件的持续时间。 例如，假设某个 Bug 导致所有车的重量不正确（超出 20000 磅），因此必须计算该 Bug 的持续时间。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 02.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 03.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 05.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 06.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 07.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 08.0000000 Z |2000 |

**输出**：

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00：00： 02.000 Z |2015-01-01T00：00： 07.000 Z |

**查询**：

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
第一个**SELECT**语句将当前权重度量与先前的度量值相关联，并将其与当前度量值组合在一起。 第二个**SELECT**返回到最新事件，其中*previous_weight*小于20000，当前权重小于20000，当前事件的*previous_weight*大于20000。

End_fault 是上一个事件出错的当前非故障事件，Start_fault 是之前的最后一个非故障事件。

## <a name="periodically-output-values"></a>定期输出值

如果发生异常或缺失事件，可以通过更稀疏的数据输入生成常规间隔输出。 例如，每间隔 5 秒生成一个事件，报告最新发现的数据点。

**输入**：

| 时间 | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |第 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**输出（前 10 行）** ：

| Window_end | Last_event。阶段 | Last_event。负值 |
| --- | --- | --- |
| 2014-01-01T14：01： 00.000 Z |2014-01-01T14：01： 00.000 Z |第 |
| 2014-01-01T14：01： 05.000 Z |2014-01-01T14：01： 05.000 Z |2 |
| 2014-01-01T14：01： 10.000 Z |2014-01-01T14：01： 10.000 Z |3 |
| 2014-01-01T14：01： 15.000 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 20.000 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 25.000 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 30.000 Z |2014-01-01T14：01： 30.000 Z |5 |
| 2014-01-01T14：01： 35.000 Z |2014-01-01T14：01： 35.000 Z |6 |
| 2014-01-01T14：01： 40.000 Z |2014-01-01T14：01： 35.000 Z |6 |
| 2014-01-01T14：01： 45.000 Z |2014-01-01T14：01： 35.000 Z |6 |

**查询**：

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

此查询每隔 5 秒生成一个事件，并输出上次收到的最后一个事件。 **HOPPINGWINDOW**持续时间确定查询查找最新事件的距离。

有关详细信息，请参阅[跳跃窗口](/stream-analytics-query/hopping-window-azure-stream-analytics)。

## <a name="process-events-with-independent-time-substreams"></a>独立时间处理事件（Substreams）

由于事件生成器之间的时钟偏差、分区之间的时钟偏差或网络延迟，事件可能会迟到或不按顺序到达。
例如， *TollID* 2 的设备时钟在*TollID* 1 后5秒， *TollID* 3 的设备时钟为*TollID* 1 后面的十秒。 对于每次收费，计算都可以独立发生，只考虑其自己的时钟数据作为时间戳。

**输入**：

| LicensePlate | 制造商 | 时间 | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 01.0000000 Z | 第 |
| YHN 6970 |Make2 |2015-07-27T00：00： 05.0000000 Z | 第 |
| QYF 9358 |Make1 |2015-07-27T00：00： 01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00：00： 04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00：00： 10.0000000 Z | 第 |
| RMV 8282 |Make1 |2015-07-27T00：00： 03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00：00： 11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00：00： 07.0000000 Z | 3 |

**输出**：

| TollID | 计数 |
| --- | --- |
| 第 | 2 |
| 2 | 2 |
| 第 | 第 |
| 3 | 第 |
| 2 | 第 |
| 3 | 第 |

**查询**：

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**TIMESTAMP by**子句分别使用 substreams 查看每个设备时间线。 每个*TollID*的 output 事件都是在计算时生成的，这意味着，每个*TollID*的事件按顺序排列，而不是在所有设备都处于同一时钟时重新排序。

有关详细信息，请参阅[TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)。

## <a name="remove-duplicate-events-in-a-window"></a>删除时间范围内的重复事件

执行某项操作（例如计算给定时间范围内事件的平均值）时，应筛选出重复事件。 在下面的示例中，第二个事件是第一个事件的副本。

**输入**：  

| DeviceId | 时间 | Attribute | 值 |
| --- | --- | --- | --- |
| 第 |2018-07-27T00：00： 01.0000000 Z |温度 |50 |
| 第 |2018-07-27T00：00： 01.0000000 Z |温度 |50 |
| 2 |2018-07-27T00：00： 01.0000000 Z |温度 |40 |
| 第 |2018-07-27T00：00： 05.0000000 Z |温度 |60 |
| 2 |2018-07-27T00：00： 05.0000000 Z |温度 |50 |
| 第 |2018-07-27T00：00： 10.0000000 Z |温度 |100 |

**输出**：  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 第 |
|45 | 2 |

**查询**：

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**计数（非重复时间）** 返回时间范围内的时间列中非重复值的数目。 然后，可以使用第一步的输出来计算每个设备的平均值，方法是丢弃重复项。

有关详细信息，请参阅 "[计数（非重复时间）](/stream-analytics-query/count-azure-stream-analytics)"。

## <a name="session-windows"></a>会话窗口

"会话" 窗口是一种窗口，它会持续发生事件发生，并在特定时间内未收到任何事件时或窗口达到其最大持续时间时关闭进行计算。
在计算用户交互数据时，此窗口特别有用。 当用户开始与系统交互时，将启动一个窗口，并在未观察到更多事件时关闭，这意味着用户已停止交互。
例如，用户正在与 web 页面交互，其中记录了点击数，会话窗口可用于确定用户与站点交互的时间。

**输入**：

| User_id | 时间 | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00：00： 00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00：00： 20.0000000 Z | "www.example.com/b.html" |
| 第 | 2017-01-26T00：00： 55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00：01： 10.0000000 Z | "www.example.com/d.html" |
| 第 | 2017-01-26T00：01： 15.0000000 Z | "www.example.com/e.html" |

**输出**：

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00：00： 00.0000000 Z | 2017-01-26T00：01： 10.0000000 Z | 70 |
| 第 | 2017-01-26T00：00： 55.0000000 Z | 2017-01-26T00：01： 15.0000000 Z | 20 |

**查询**：

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**选择**项目与用户交互相关的数据，以及交互的持续时间。 按用户对数据进行分组，并在1分钟内未发生交互时关闭的**SessionWindow** ，最大窗口大小为60分钟。

有关**SessionWindow**的详细信息，请参阅 "[会话" 窗口](/stream-analytics-query/session-window-azure-stream-analytics)。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript 和中的用户定义函数的语言扩展性C#

可以用 JavaScript 或C#语言编写的自定义函数扩展 Azure 流分析查询语言。 用户定义的函数（UDF）是自定义/复杂计算，不能使用**SQL**语言轻松表示。 这些 Udf 只能定义一次，并在查询中多次使用。 例如，UDF 可用于将十六进制*nvarchar （max）* 值转换为*bigint*值。

**输入**：

| Device_id | HexValue |
| --- | --- |
| 第 | B4 |
| 2 | 11B |
| 3 | "121" |

**输出**：

| Device_id | Decimal |
| --- | --- |
| 第 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

用户定义函数将从每个使用的事件的 HexValue 计算*bigint*值。

有关详细信息，请参阅[JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions)和[C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)。

## <a name="advanced-pattern-matching-with-match_recognize"></a>与 MATCH_RECOGNIZE 的高级模式匹配

**MATCH_RECOGNIZE**是一种高级模式匹配机制，可用于将一系列事件与明确定义的正则表达式模式相匹配。
例如，如果在 ATM 的操作过程中出现两个连续的警告消息，则会实时监视 ATM，要求管理员需要获得通知。

**输入**：

| ATM_id | Operation_id | Return_Code | 时间 |
| --- | --- | --- | --- |
| 第 | "输入 Pin 码" | “Success” | 2017-01-26T00：10： 00.0000000 Z |
| 2 | "开钱槽" | “Success” | 2017-01-26T00：10： 07.0000000 Z |
| 2 | "收盘 Money 时隙" | “Success” | 2017-01-26T00：10： 11.0000000 Z |
| 第 | "正在输入收回量" | “Success” | 2017-01-26T00：10： 08.0000000 Z |
| 第 | "开钱槽" | 出现 | 2017-01-26T00：10： 14.0000000 Z |
| 第 | "打印银行余额" | 出现 | 2017-01-26T00：10： 19.0000000 Z |

**输出**：

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 第 | "开钱槽" | 2017-01-26T00：10： 14.0000000 Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

此查询匹配至少两个连续的失败事件，并在满足条件时生成警报。
**PATTERN**定义要在匹配时使用的正则表达式，在本例中，为任意数量的成功操作，后跟至少两个连续失败。
成功和失败都使用 Return_Code 值定义，满足条件后，将使用*ATM_id*、第一个警告操作和第一个警告时间来投影**度量值**。

有关详细信息，请参阅[MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)。

## <a name="geofencing-and-geospatial-queries"></a>地理围栏和地理空间查询
Azure 流分析提供内置的地理空间功能，这些功能可用于实现各种方案，如汽油管理、联系、共享、连接汽车和资产跟踪。
地理空间数据可引入为 GeoJSON 或 WKT 格式，作为事件流或引用数据的一部分。
例如，一家专用于制造计算机的公司，用于打印护照，将其计算机租赁到了政府和 consulates。 这些计算机的位置在很大程度上受到了控制，以避免 misplacing 并可能使用伪造的护照。 每台计算机都适合使用 GPS 跟踪器，该信息会中继回 Azure 流分析作业。
该制造商要跟踪这些计算机的位置，并在其中一个用户离开授权区域时发出警报，这种方法可以远程禁用、警报机构和检索设备。

**输入**：

| Equipment_id | Equipment_current_location | 时间 |
| --- | --- | --- |
| 第 | "POINT （-122.13288797982818 47.64082002051315）" | 2017-01-26T00：10： 00.0000000 Z |
| 第 | "POINT （-122.13307252987875 47.64081350934929）" | 2017-01-26T00：11： 00.0000000 Z |
| 第 | "POINT （-122.13308862313283 47.6406508603241）" | 2017-01-26T00：12： 00.0000000 Z |
| 第 | "POINT （-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13： 00.0000000 Z |

**引用数据输入**：

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 第 | "多边形（（-122.13326028450979 47.6409833866794，-122.13261655434621 47.6409833866794，-122.13261655434621 47.64061471602751，-122.13326028450979 47.64061471602751，-122.13326028450979 47.6409833866794）" |

**输出**：

| Equipment_id | Equipment_alert_location | 时间 |
| --- | --- | --- |
| 第 | "POINT （-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13： 00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

此查询使制造商能够自动监视计算机位置，并在计算机离开允许的地域隔离区内时收到警报。 内置地理空间函数允许用户在没有第三方库的情况下使用查询中的 GPS 数据。

有关详细信息，请参阅[地理围栏和地理空间聚合方案和 Azure 流分析](geospatial-scenarios.md)一文。

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
