---
title: Azure 流分析中的常见查询模式
description: 本文介绍了在 Azure 流分析作业中很有用的多个常见查询模式和设计。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: c79d810979641d1dc128c741c2124d9b5887aa3d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020740"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure 流分析中的常见查询模式

Azure 流分析中的查询以类似 SQL 的查询语言表示。 这些语言构造记录在[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查询设计可以表达简单的传递逻辑来将事件数据从一个输入流移动到一个输出数据存储，它还可以执行丰富的模式匹配和时态分析来计算各时间范围的聚合值，如[使用流分析生成 IoT 解决方案](stream-analytics-build-an-iot-solution-using-stream-analytics.md)指南中所述。 可以联接多个输入的数据，以合并流事件，并且可以对静态参考数据进行查找，以丰富事件值。 此外，还可以将数据写入到多个输出。

本文档概述了以真实情况为基础的多个常见查询模式的解决方案。

## <a name="supported-data-formats"></a>受支持的数据格式

Azure 流分析支持处理采用 CSV、JSON 和 Avro 数据格式的事件。

JSON 和 Avro 都可能包含嵌套对象（记录）或数组等复杂类型。 有关使用这些复杂数据类型的详细信息，请参阅[解析 JSON 和 AVRO 数据](stream-analytics-parsing-json.md)一文。

## <a name="simple-pass-through-query"></a>简单的传递查询

简单的传递查询可用于将输入流数据复制到输出中。 例如，如果需要将包含实时车辆信息的数据流保存到 SQL 数据库中以供稍后分析，则简单的传递查询将执行此工作。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 制造商1 |2015-01-01T00:00:02.0000000Z |"2000" |

**输出**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 制造商1 |2015-01-01T00:00:02.0000000Z |"2000" |

**查询**：

```SQL
SELECT
    *
INTO Output
FROM Input
```

SELECT * 查询可投影传入事件的所有字段，并将其发送到输出。 同样，SELECT 也可用于仅从输入投影必填字段。 在此示例中，如果车辆“制造商”和“时间”是唯一要保存的必填字段，则可以在 SELECT 语句中指定这些字段 。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |1000 |
| 制造商1 |2015-01-01T00:00:02.0000000Z |2000 |
| 制造商2 |2015-01-01T00:00:04.0000000Z |1500 |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商1 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:04.0000000Z |

**查询**：

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>随时间的数据聚合

若要计算某个时间范围内的信息，可以将数据聚合在一起。 在此示例中，对每个特定汽车发出的过去10秒的时间计算一次计数。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |1000 |
| 制造商1 |2015-01-01T00:00:02.0000000Z |2000 |
| 制造商2 |2015-01-01T00:00:04.0000000Z |1500 |

**输出**：

| 制造商 | Count |
| --- | --- |
| 制造商1 | 2 |
| 制造商2 | 1 |

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

此聚合按“制造商”对汽车进行分组，并每隔 10 秒钟对其进行计数。 输出包含通过收费站的汽车的“制造商”和“计数” 。

TumblingWindow 是一种用于将事件组合在一起的窗口函数。 聚合可应用于所有分组的事件。 有关详细信息，请参阅[窗口函数](stream-analytics-window-functions.md)。

有关聚合的详细信息，请参阅[聚合函数](/stream-analytics-query/aggregate-functions-azure-stream-analytics)。

## <a name="data-conversion"></a>数据转换

可以使用 CAST 方法对数据进行实时强制转换。 例如，车辆重量可以从类型 nvarchar(max) 转换为类型 bigint，并且可用于数值计算 。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 制造商1 |2015-01-01T00:00:02.0000000Z |"2000" |

**输出**：

| 制造商 | 重量 |
| --- | --- |
| 制造商1 |3000 |

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

使用 CAST 语句来指定其数据类型。 请参阅[数据类型（Azure 流分析）](/stream-analytics-query/data-types-azure-stream-analytics)中支持的数据类型列表。

有关详细信息，请参阅[数据转换函数](/stream-analytics-query/conversion-functions-azure-stream-analytics)。

## <a name="string-matching-with-like-and-not-like"></a>使用 LIKE 和 NOT LIKE 匹配字符串

LIKE 和 NOT LIKE 可用于验证某个字段是否与特定模式匹配 。 例如，可以创建一个筛选器，以仅返回以字母“A”开头以数字 9 结尾的车辆牌照。

**输入**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| 制造商1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 制造商3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**输出**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| 制造商2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 制造商3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**查询**：

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

使用 LIKE 语句检查 License_plate 字段的值 。 它应当以字母“A”开头，其中包含零个或多个字符的任意字符串，并以数字 9 结尾。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>指定不同案例/值的逻辑（CASE 语句）

CASE 语句可根据特定条件为不同字段提供不同计算。 例如，将车道“A”分配给制造商1 的汽车，并将车道“B”分配给任何其他制造商的汽车。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:03.0000000Z |

**输出**：

| 制造商 |Dispatch_to_lane | 时间 |
| --- | --- | --- |
| 制造商1 |“A” |2015-01-01T00:00:01.0000000Z |
| 制造商2 |“B” |2015-01-01T00:00:02.0000000Z |

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

CASE 表达式将表达式与一组简单表达式进行比较以确定结果。 在此示例中，制造商1 的车辆被分派给车道“A”，而任何其他制造商的车辆都将分配给车道“B”。

有关详细信息，请参阅 [case 表达式](/stream-analytics-query/case-azure-stream-analytics)。

## <a name="send-data-to-multiple-outputs"></a>将数据发送到多个输出

可使用多个 SELECT 语句将数据输出到不同的输出接收器。 例如，一个 SELECT 可以输出基于阈值的警报，而另一个则可将事件输出到 blob 存储。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商1 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:03.0000000Z |

输出 ArchiveOutput：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商1 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:03.0000000Z |

输出 AlertOutput：

| 制造商 | 时间 | Count |
| --- | --- | --- |
| 制造商2 |2015-01-01T00:00:10.0000000Z |3 |

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

INTO 子句告知流分析要将数据写入到哪一个输出。 第一个 SELECT 定义传递查询，该查询接收来自输入的数据，并将其发送到名为 ArchiveOutput 的输出 。 第二个查询进行了一些简单的聚合和筛选操作，然后将结果发送到名为 AlertOutput 的下游报警系统输出。

请注意，可使用 WITH 子句来定义多个子查询块。 此选项具有以下好处，即在输入源打开较少的读取器。

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

有关详细信息，请参阅 [WITH 子句](/stream-analytics-query/with-azure-stream-analytics)。

## <a name="count-unique-values"></a>对唯一值进行计数

COUNT 和 DISTINCT 可用于计算时间范围内流中显示的唯一字段值数 。 可以创建一个查询，以计算在 2 秒的时间范围内，通过收费站的同一制造商的汽车数量是多少。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商1 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |2015-01-01T00:00:03.0000000Z |

**输出：**

| Count_make | 时间 |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**查询：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

COUNT(DISTINCT Make) 返回时间范围内的“制造商”列的非重复值数目 。
有关详细信息，请参阅 [COUNT 聚合函数](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="calculation-over-past-events"></a>过去事件的计算

LAG 函数可用于查看某个时间范围内的过去事件，并将其与当前事件进行比较。 例如，如果当前汽车制造商与通过收费站的最后一辆汽车不同，则可以输出当前汽车制造商。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |
| 制造商2 |2015-01-01T00:00:02.0000000Z |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| 制造商2 |2015-01-01T00:00:02.0000000Z |

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

使用 LAG 来查看某个事件后的输入流，检索“制造商”值，并将其与当前事件的“制造商”值相比较并输出该事件 。

有关详细信息，请参阅 [LAG](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="retrieve-the-first-event-in-a-window"></a>检索时间范围内的第一个事件

IsFirst 可用于检索时间范围内的第一个事件。 例如，每隔 10 分钟输出一次第一辆汽车的信息。

**输入**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |制造商1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |制造商3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |制造商1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |制造商2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |制造商2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |制造商1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |制造商4 |2015-07-27T00:13:45.0000000Z |

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |制造商1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |制造商1 |2015-07-27T00:12:02.0000000Z |

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

IsFirst 还可以对数据进行分区，并计算每隔 10 分钟发现的每个特定汽车“制造商”的第一个事件。

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |制造商1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |制造商3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |制造商2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |制造商1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |制造商4 |2015-07-27T00:13:45.0000000Z |

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

有关详细信息，请参阅 [IsFirst](/stream-analytics-query/isfirst-azure-stream-analytics)。

## <a name="return-the-last-event-in-a-window"></a>返回时间范围内的最后一个事件

由于系统实时使用事件，因此没有函数可用于确定某个事件是否是该时间范围内要实现的最后一个事件。 若要实现此目的，需要将输入流与另一个输入流联接，其中事件的时间是该范围内所有事件的最大时间。

**输入**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |制造商1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |制造商3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |制造商1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |制造商2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |制造商2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |制造商1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |制造商4 |2015-07-27T00:13:45.0000000Z |

**输出**：

| License_plate | 制造商 | 时间 |
| --- | --- | --- |
| VFE 1616 |制造商2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |制造商4 |2015-07-27T00:13:45.0000000Z |

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

查询的第一步是查找 10 分钟范围内的最大时间戳，该时间戳是该范围的最后一个事件的时间戳。 第二个步骤是将第一个查询的结果与原始流联接，查找每个时间范围内与最后一个时间戳相匹配的事件。 

DATEDIFF 是一种特定于日期的函数，用于比较并返回两个 DateTime 字段之间的时间差。有关详细信息，请参阅 [date 函数](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)。

有关联接流的详细信息，请参阅 [JOIN](/stream-analytics-query/join-azure-stream-analytics)。


## <a name="correlate-events-in-a-stream"></a>关联流中的事件

可使用 LAG 函数查看过去的事件来关联同一流中的事件。 例如，每次同一“制造商”的连续两辆汽车在过去 90 秒通过收费站时，便可以生成一个输出。

**输入**：

| 制造商 | License_plate | 时间 |
| --- | --- | --- |
| 制造商1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| 制造商1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 制造商2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| 制造商1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**输出**：

| 制造商 | 时间 | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| 制造商1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

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

LAG 函数可查看一个事件后的输入流，并检索“制造商”值并将该值与当前事件的“制造商”值进行比较 。  满足条件后，可以使用 SELECT 语句中的 LAG 投影上一个事件中的数据 。

有关详细信息，请参阅 [LAG](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="detect-the-duration-between-events"></a>检测事件之间的持续时间

接收到“结束”事件后，可以通过查看上一个“开始”事件来计算事件的持续时间。 此查询可用于确定用户在某一页面或功能上所花费的时间。

**输入**：  

| 用户 | Feature | 事件 | 时间 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |开始 |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |结束 |2015-01-01T00:00:08.0000000Z |

**输出**：  

| 用户 | Feature | Duration |
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

LAST 函数可用于检索特定条件中的最后一个事件。 在此示例中，条件是类型为“开始”的事件，通过“分区条件”用户和功能对搜索进行分区。 这样，搜索“开始”事件时，将单独处理每个用户和功能。 LIMIT DURATION 将“结束”和“开始”事件之间的向后搜索时间限制为 1 小时。

## <a name="detect-the-duration-of-a-condition"></a>检测某个条件的持续时间

对于跨多个事件的条件，可使用 LAG 函数标识该条件的持续时间。 例如，假设某个 Bug 导致所有车的重量不正确（超出 20000 磅），因此必须计算该 Bug 的持续时间。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| 制造商1 |2015-01-01T00:00:01.0000000Z |2000 |
| 制造商2 |2015-01-01T00:00:02.0000000Z |25000 |
| 制造商1 |2015-01-01T00:00:03.0000000Z |26000 |
| 制造商2 |2015-01-01T00:00:04.0000000Z |25000 |
| 制造商1 |2015-01-01T00:00:05.0000000Z |26000 |
| 制造商2 |2015-01-01T00:00:06.0000000Z |25000 |
| 制造商1 |2015-01-01T00:00:07.0000000Z |26000 |
| 制造商2 |2015-01-01T00:00:08.0000000Z |2000 |

**输出**：

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

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
第一个 SELECT 语句将当前重量测量值与之前的测量值关联，并将其与当前测量值一起投影。 第二个 SELECT 查看 previous_weight 小于 20000 的最后一个事件，其中，当前重量小于 20000，当前事件的 previous_weight 大于 20000 。

End_fault 是前一个事件出错的当前无错误事件，Start_fault 是 End_fault 之前的最后一个无错误事件。

## <a name="periodically-output-values"></a>定期输出值

如果事件无规律或丢失，可以通过更稀疏的数据输入生成固定间隔输出。 例如，每间隔 5 秒生成一个事件，报告最新发现的数据点。

**输入**：

| 时间 | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**输出（前 10 行）** ：

| Window_end | Last_event.Time | Last_event.Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

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

此查询每隔 5 秒生成一个事件，并输出上次收到的最后一个事件。 HOPPINGWINDOW 持续时间确定查询查找最新事件需要追溯到多久以前。

有关详细信息，请参阅[跳跃窗口](/stream-analytics-query/hopping-window-azure-stream-analytics)。

## <a name="process-events-with-independent-time-substreams"></a>处理具有独立时间的事件（子流）

由于事件生成器之间的时钟偏差、分区之间的时钟偏差或网络延迟，事件可能会迟到或不按顺序到达。
例如，TollID 2 的设备时钟比 TollID 1 慢 5 秒，TollID 3 的设备时钟比 TollID 1 慢 10 秒   。 对于每次收费，计算都可以独立发生，并且仅考虑其自己的时钟数据作为时间戳。

**输入**：

| LicensePlate | 制造商 | 时间 | TollID |
| --- | --- | --- | --- |
| DXE 5291 |制造商1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |制造商2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |制造商1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |制造商3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |制造商2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |制造商1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |制造商3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |制造商4 |2015-07-27T00:00:07.0000000Z | 3 |

**输出**：

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**查询**：

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

TIMESTAMP OVER BY 子句分别使用子流来查看每个设备时间线。 每个 TollID 的输出事件都是在计算时生成的，这意味着事件按照每个 TollID 的顺序排列，而不是像所有设备都在同一个时钟上那样重新排序 。

有关详细信息，请参阅 [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)。

## <a name="remove-duplicate-events-in-a-window"></a>删除时间范围内的重复事件

执行某项操作（例如计算给定时间范围内事件的平均值）时，应筛选出重复事件。 在下面的示例中，第二个事件是第一个事件的副本。

**输入**：  

| DeviceId | 时间 | Attribute | 值 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |温度 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |温度 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |温度 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |温度 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |温度 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |温度 |100 |

**输出**：  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
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

**COUNT(DISTINCT Time)** 返回时间范围内的“时间”列的非重复值数目。 然后，可以使用第一步的输出按设备计算平均值，只需去掉重复值即可。

有关详细信息，请参阅 [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="session-windows"></a>会话窗口

会话窗口是一种窗口，它会在事件发生时不断扩展，并在特定时间后未收到任何事件时或窗口达到其最大持续时间时关闭以进行计算。
在计算用户交互数据时，此窗口特别有用。 当用户开始与系统交互时，将启动一个窗口，该窗口在不再观察到事件时关闭，意味着用户已停止交互。
例如，在用户与其中记录了点击数的网页进行交互的情况下，可以使用会话窗口确定用户与站点交互的时长。

**输入**：

| User_id | 时间 | 代码 |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | “www.example.com/a.html” |
| 0 | 2017-01-26T00:00:20.0000000Z | “www.example.com/b.html” |
| 1 | 2017-01-26T00:00:55.0000000Z | “www.example.com/c.html” |
| 0 | 2017-01-26T00:01:10.0000000Z | “www.example.com/d.html” |
| 1 | 2017-01-26T00:01:15.0000000Z | “www.example.com/e.html” |

**输出**：

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

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

SELECT 将与用户交互相关的数据和交互持续时间一起投影。 按用户和 SessionWindow 对数据进行分组，该窗口在 1 分钟内未发生交互的情况下关闭，且最大窗口大小为 60 分钟。

有关 SessionWindow 的详细信息，请参阅[会话窗口](/stream-analytics-query/session-window-azure-stream-analytics)。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>通过采用 JavaScript 和 C# 的用户定义函数扩展语言

可以通过采用 JavaScript 或 C# 语言编写的自定义函数来扩展 Azure 流分析查询语言。 用户定义的函数 (UDF) 是无法使用 SQL 语言轻松表达的自定义/复杂计算。 这些 UDF 只能定义一次，并且可在查询中多次使用。 例如，UDF 可用于将十六进制 nvarchar(max) 值转换为 bigint 值 。

**输入**：

| Device_id | HexValue |
| --- | --- |
| 1 | “B4” |
| 2 | “11B” |
| 3 | “121” |

**输出**：

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
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

用户定义的函数将从每个使用的事件的 HexValue 计算 bigint 值。

有关详细信息，请参阅 [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) 和 [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)。

## <a name="advanced-pattern-matching-with-match_recognize"></a>使用 MATCH_RECOGNIZE 进行高级模式匹配

MATCH_RECOGNIZE 是一种高级模式匹配机制，可用于将一系列事件与明确定义的正则表达式模式相匹配。
例如，会实时监视 ATM 是否出现故障，如果在 ATM 运行期间出现两条连续的警告消息，则需要通知管理员。

**输入**：

| ATM_id | Operation_id | Return_Code | 时间 |
| --- | --- | --- | --- |
| 1 | “输入 Pin” | “Success” | 2017-01-26T00:10:00.0000000Z |
| 2 | “打开投币口” | “Success” | 2017-01-26T00:10:07.0000000Z |
| 2 | “关闭投币口” | “Success” | 2017-01-26T00:10:11.0000000Z |
| 1 | “输入取款数” | “Success” | 2017-01-26T00:10:08.0000000Z |
| 1 | “打开投币口” | “警告” | 2017-01-26T00:10:14.0000000Z |
| 1 | “打印银行结余” | “警告” | 2017-01-26T00:10:19.0000000Z |

**输出**：

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | “打开投币口” | 2017-01-26T00:10:14.0000000Z |

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
PATTERN 定义要用于匹配的正则表达式，在本例中，为后跟至少两个连续失败事件的任意数量的成功操作。
“成功”和“失败”使用 Return_Code 值定义，满足条件后，将使用 ATM_id、第一个警告操作和第一次警告时间投影 MEASURES。

有关详细信息，请参阅 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)。

## <a name="geofencing-and-geospatial-queries"></a>地理围栏和地理空间查询
Azure 流分析提供了内置的地理空间函数，可用于实现各种方案，例如车队管理、拼车、联网汽车和资产跟踪。
地理空间数据可作为事件流或参考数据的一部分以 GeoJSON 或 WKT 格式引入。
例如，一家专门生产用于打印护照的制造用机器的公司，将其机器租给了政府和领事馆。 这些机器的位置受到严格控制，以避免错放和用于伪造护照。 每台机器都配有 GPS 跟踪器，该信息会传回 Azure 流分析作业。
制造商会跟踪这些机器的位置，并在其中某台机器离开授权区域时收到警报，这样，他们便可以远程禁用、警告机构并找回设备。

**输入**：

| Equipment_id | Equipment_current_location | 时间 |
| --- | --- | --- |
| 1 | “POINT(-122.13288797982818 47.64082002051315)” | 2017-01-26T00:10:00.0000000Z |
| 1 | “POINT(-122.13307252987875 47.64081350934929)” | 2017-01-26T00:11:00.0000000Z |
| 1 | “POINT(-122.13308862313283 47.6406508603241)” | 2017-01-26T00:12:00.0000000Z |
| 1 | “POINT(-122.13341048821462 47.64043760861279)” | 2017-01-26T00:13:00.0000000Z |

参考数据输入：

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | “POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))” |

**输出**：

| Equipment_id | Equipment_alert_location | 时间 |
| --- | --- | --- |
| 1 | “POINT(-122.13341048821462 47.64043760861279)” | 2017-01-26T00:13:00.0000000Z |

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

此查询使制造商能够自动监视机器位置，并在机器离开允许的地理围栏时收到警报。 内置的地理空间函数允许用户在查询中使用 GPS 数据，而无需第三方库。

有关详细信息，请参阅[使用 Azure 流分析的地理围栏和地理空间聚合方案](geospatial-scenarios.md)一文。

## <a name="get-help"></a>获取帮助

若要获得进一步的帮助，可前往 [Azure 流分析的 Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
