---
title: "流分析中常见使用模式的查询示例 | Microsoft 文档"
description: "常见的 Azure 流分析查询模式"
keywords: "查询示例"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: cb0a948416983f33a4ca8d9211a3a114ba011685
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>常用流分析使用模式的查询示例
## <a name="introduction"></a>介绍
Azure 流分析中的查询以类似 SQL 的查询语言表示。 这些查询记录在[流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)指南中。 本文档概述了以真实情况为基础的多个常见查询模式的解决方案。 此项工作仍在进行，将继续使用新的模式不断进行更新。

## <a name="query-example-convert-data-types"></a>查询示例：转换数据类型
**说明**：定义输入流中的属性类型。
例如，在输入流中，车重是字符串，需要将它转换为 INT 类型才能执行 SUM 运算。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**输出**：

| 制造商 | 重量 |
| --- | --- |
| Honda |3000 |

**解决方案**；

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**说明**：在“重量”字段中使用 CAST 语句来指定它的数据类型。 请参阅[数据类型（Azure 流分析）](https://msdn.microsoft.com/library/azure/dn835065.aspx)中支持的数据类型列表。

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>查询示例：使用 Like/Not like 进行模式匹配
**说明**： 检查事件上的字段值是否与特定的模式相匹配。
例如，检查返回以 A 开头并以 9 结尾的车牌的结果。

**输入**：

| 制造商 | LicensePlate | 时间 |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**输出**：

| 制造商 | LicensePlate | 时间 |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**解决方案**；

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**说明**：使用 LIKE 语句检查 LicensePlate 字段的值。 它应当以 A 开头，其中包含零个或多个字符的任意字符串，并以 9 结尾。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>查询示例：指定不同案例/值的逻辑（CASE 语句）
**说明**：根据某些特定条件对字段进行各种计算。
例如，同一制造商的汽车通过数量为 1 时，为这种特殊情况提供一个字符串说明。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出**：

| CarsPassed | 时间 |
| --- | --- | --- |
| 1 辆 Honda |2015-01-01T00:00:10.0000000Z |
| 2 辆 Toyota |2015-01-01T00:00:10.0000000Z |

**解决方案**；

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**说明**：可通过 CASE 子句根据某些条件（在此示例中为聚合窗口中车辆的计数）提供不同的计算操作。

## <a name="query-example-send-data-to-multiple-outputs"></a>查询示例：将数据发送到多个输出
**说明**：从单个作业中将数据发送到多个输出目标。
例如，分析基于阈值的警报数据，并将所有事件保存到 blob 存储。

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出 1**：

| 制造商 | 时间 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出 2**：

| 制造商 | 时间 | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**解决方案**；

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
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

**说明**：INTO 子句告知流分析哪一个输出可通过此语句写入数据。
第一个查询将我们接收到的数据传递到名为 ArchiveOutput 的输出。
第二个查询进行了一些简单的聚合和筛选操作，并将结果发送到下游的警报系统。

请注意，还可重复使用多个输出语句中的公用表表达式 (CTE) 结果（例如 WITH 语句）。 此选项可提供额外权益，即在输入源打开较少的读取器。
例如： 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-count-unique-values"></a>查询示例：对唯一值进行计算
**说明**：计算时间范围内流中显示的唯一字段值数。
例如，在 2 秒的时间范围内，通过收费站的同一制造商的汽车数量是多少？

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出：**

| CountMake | 时间 |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**解决方案：**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**说明：**
COUNT(DISTINCT Make) 返回时间范围内的“制造商”列的非重复值数目。

## <a name="query-example-determine-if-a-value-has-changed"></a>查询示例：确定某个值是否已更改
**说明**：可通过查看前一个值来确定它与当前的值是否相同。
例如：在收费路段前一辆汽车与当前汽车的制造商是否相同？

**输入**：

| 制造商 | 时间 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**解决方案**；

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**说明**：使用 LAG 来查看后退一个事件之后的输入流，并获得“制造商”字段的值。 然后，将它与当前事件的“制造商”字段进行比较，如果二者不同，则输出该事件。

## <a name="query-example-find-the-first-event-in-a-window"></a>查询示例：查找时间范围内的第一个事件
**说明**：查找每 10 分钟时间间隔内的第一辆汽车。

**输入**：

| LicensePlate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**输出**：

| LicensePlate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**解决方案**；

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

现在，我们来变一下这个问题，查找每 10 分钟时间间隔内特定制造商的第一辆汽车。

| LicensePlate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解决方案**；

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>查询示例：查找时间范围内的最后一个事件
**说明**：查找每 10 分钟时间间隔内的最后一辆汽车。

**输入**：

| LicensePlate | 制造商 | 时间 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**输出**：

| LicensePlate | 制造商 | 时间 |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解决方案**；

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**说明**：查询中包含两个步骤。 第一个步骤是在 10 分钟的时间范围内查找最新的时间戳。 第二个步骤是将第一个查询的结果与原始流联接，查找每个时间范围内与最后一个时间戳相匹配的事件。 

## <a name="query-example-detect-the-absence-of-events"></a>查询示例：检测缺少的事件
**说明**：查看流的值是否不与某个条件相匹配。
例如，两辆同一制造商的汽车是否在 90 秒内先后进入收费路段？

**输入**：

| 制造商 | LicensePlate | 时间 |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**输出**：

| 制造商 | 时间 | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**解决方案**；

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**说明**：使用 LAG 来查看后退一个事件之后的输入流，并获得“制造商”字段的值。 将它与当前事件的“制造商”字段进行比较，如果二者相同，则输出该事件。 还可使用 LAG 获取前一辆汽车的数据。

## <a name="query-example-detect-the-duration-between-events"></a>查询示例：检测事件之间的持续时间
**说明**：查找给定事件的持续时间。 例如：给定一个 Web 点击流，确定某项功能花费的时间。

**输入**：  

| 用户 | 功能 | 事件 | 时间 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |开始 |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |结束 |2015-01-01T00:00:08.0000000Z |

**输出**：  

| 用户 | 功能 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**解决方案**；

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**说明**：使用 LAST 函数检索上次事件类型为“开始”时的时间值。 LAST 函数使用 PARTITION BY [user] 指示结果应按唯一用户计算。 该查询在“开始”和“停止”事件之间有 1 小时的最大时差阈值，但也可按需配置 (LIMIT DURATION(hour, 1)。

## <a name="query-example-detect-the-duration-of-a-condition"></a>查询示例：检测某个条件的持续时间
**说明**：查看某个条件的持续时间。
例如，假设某个 Bug 导致所有汽车的重量均不正确（超出 20000 磅）。 我们需要计算该 Bug 的持续时间。

**输入**：

| 制造商 | 时间 | 重量 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**输出**：

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**解决方案**；

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**说明**：使用 LAG 查看 24 小时内的输入流并查找因重量 < 20000 而持续的 StartFault 和 StopFault 实例。

## <a name="query-example-fill-missing-values"></a>查询示例：填充缺失值
**说明**：对于值缺失的事件流，以固定的间隔生成事件流。
例如，每间隔 5 秒生成一个事件，报告最新发现的数据点。

**输入**：

| t | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**输出（前 10 行）**：

| windowend | lastevent.t | lastevent.value |
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

**解决方案**；

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**说明**：此查询每隔 5 秒生成一个事件，并输出上次收到的最后一个事件。 [跳跃窗口](https://msdn.microsoft.com/library/dn835041.aspx "跳跃窗口 - Azure 流分析")持续时间确定查询将查找最新事件的时间（在本例中为 300 秒）。


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>查询示例：在同一流中关联两个事件类型
**说明**：有时我们需要基于某个特定时间范围内发生的多个事件类型生成警报。
例如，在家用烤箱的 IoT 方案中，我们想要在风扇温度小于 40 且在过去 3 分钟内最大功率小于 10 时引发警报。

**输入**：

| time | deviceId | sensorName | 值 |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**输出**：

| EventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | “加热元件短路” |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | “加热元件短路” |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | “加热元件短路” |15 |

**解决方案**；

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
````

**说明**：第一个查询 `max_power_during_last_3_mins` 使用[滑动窗口](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/sliding-window-azure-stream-analytics)查找在过去 3 分钟内每个设备的功率传感器最大值。 将第二个查询联接到第一个查询，以便在与当前事件有关的最近窗口中查找功率值。 然后，假如满足条件，将为设备生成警报。


## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

