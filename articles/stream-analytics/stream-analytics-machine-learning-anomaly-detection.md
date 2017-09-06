---
title: "Azure 用法指南（预览版）中的异常情况检测 | Microsoft Docs"
description: "使用流分析和机器学习来检测异常。"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 7ab489f6ae7da2640ba199b20e7727da60497918
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---

# <a name="using-the-anomalydetection-operator"></a>使用 ANOMALYDETECTION 运算符

> [!IMPORTANT]
> 此功能处于预览状态。 不建议在生产中使用。

可使用 ANOMALYDETECTION 运算符在事件流中检测异常。
例如，在一段较长时间内可用内存缓慢减少，可能预示存在内存泄露，或稳定在一定范围的 Web 服务请求数有可能显著增加或减少。

在指定的持续时间内，检查下列异常的类型：

- 双向级别更改
- 慢速正趋势
- 慢速负趋势

使用 LIMIT DURATION 语句，限制历史记录中需要查看的距当前事件的时间间隔。 可选择将 ANOMALYDETECTION 限制为仅匹配某种属性的事件，或正在使用 WHEN 子句的条件。

还可根据 PARTITION BY 子句中指定的关键字选择单独处理事件组。 在每个分区中独立进行培训和预测。

## <a name="syntax"></a>语法

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>用法示例

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>参数

- scalar_expression

  执行异常情况检测所用的标量表达式。 它是 float 或 bigint 类型的表达式，返回单个（标量）值。 不可使用通配符表达式**\***。 scalar_expression 不能包含其他分析函数或外部函数。

- OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])

- partition_by_clause 

  `PARTITION BY \<partition key\>` 子句将学习和培训划分为不同分区。 换言之，将根据 `\<partition key\>` 的值使用单独的模型，仅含有该值的事件才会用于该模型中的学习和培训。 例如，

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  将培训阅读，并仅对照同一传感器的其他阅读进行评分。

- limit_duration clause DURATION(\<unit\>, \<length\>)

  指定在 ANOMALYDETECTION 计算中应考虑历史记录中距当前事件的时间间隔。 有关受支持的单位及其缩写的详细说明，请参阅 DATEDIFF。

- when_clause 

  为 ANOMALYDETECTION 计算中考虑的事件指定布尔条件。

## <a name="return-types"></a>返回类型

该函数返回一个包含所有三种分数的记录作为输出。 调用与不同类型异常情况检测器相关联的属性：

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

要从记录中提取单个值，请使用 GetRecordPropertyValue 函数。 例如：

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


上述异常情况分数如果有一项超过阈值，将检测到某个特定类型的异常。 阈值可以是任意浮点数 \>= 0。 阈值是敏感度和置信度之间的权衡值。 例如，过低的阈值使得检测对变化更敏感，生成更多警报，而较高的阈值会使检测的敏感性下降，其置信度虽然变高，但容易过滤某些异常。 阈值的精确值取决于具体情况。 虽然不设上限，但建议使用 3.25-5 范围内的值。

## <a name="algorithm"></a>算法

ANOMALYDETECTION 使用滑动窗口语义，这意味着计算将根据进入函数的事件执行，并生成该事件的分数。 该计算基于 Exchangeability Martingales，它通过检查事件值的分布是否被更改来执行操作。 如果是，则检测出潜在的异常。 返回的分数表示该异常的置信度级别。 作为一种内部优化，ANOMALYDETECTION 根据 d 到 2d 的事件值来计算事件的异常分数，其中 d 是指定的检测窗口大小。

ANOMALYDETECTION 期望输入时列是一致的。 通过聚合翻转或跳跃窗口可以使事件流一致。 当事件间的间隙总是小于聚合窗口时，一个翻转窗口就足以使时序一致。 当间隙较大时，可使用跳跃窗口重复最后一个值来填充间隙。 以下示例演示了如何处理这些情况。 目前不可跳过 `FillInMissingValuesStep` 步骤。 缺少此步骤将造成编译错误。

## <a name="performance-guidance"></a>性能指南

- 使用 6 SU 作业。 
- 以最少 1 秒的间隔发送事件。
- 使用 ANOMALYDETECTION 函数的非分区查询产生结果时，计算延迟平均约为 25 ms。
- 随着计算量的增加，分区查询中出现的延迟将与分区数略有不同。 但是，延迟与所有分区中可比较的事件总数的非分区事例大致相同。
- 在读取非实时数据时，快速引入了大量数据。 目前，处理此数据的速度明显减慢。 此类情况中的延迟会随窗口中数据点数目变化呈线性增加，但不受窗口大小或事件间隔本身的影响。 要降低非实时事例中的延迟，请考虑使用较小的窗口大小。 或者，考虑从当前时间开始作业。 以下是关于非分区查询中延迟的几个示例： 
    - 检测窗口中如果有 60 个数据点，就能造成时长 10 秒的延迟，吞吐量为 3 Mbps。 
    - 当数据点为 600 个时，延迟可高达 80 秒，吞吐量将至 0.4 Mbps。

## <a name="example"></a>示例

可使用下列查询在检测到异常时输出警报。
当输入流不一致时，聚合步骤可帮助将其转换为一致的时序。 该示例使用了 AVG，但聚合的特定类型取决于用户方案本身。 此外，当时序的间隙大于聚合窗口时，（根据滑动窗口语义）时序中将不再有任何能触发异常检测的事件。 因此，当下一事件到达时，一致性假设将不成立。 在这种情况下，需要一种在时序中填充间隙的方法。 一种可行方法是在每个跳跃窗口中采用最后一个事件，如下所示。

如前所述，请勿跳过 `FillInMissingValuesStep` 步骤。 省略此步骤将造成编译错误。

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>参考

* [Anomaly Detection – Using Machine Learning to Detect Abnormalities in Time Series Data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)（异常情况检测 - 使用机器学习在时序数据中检测异常）
* [机器学习异常情况检测 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Time Series Anomaly Detection](https://msdn.microsoft.com/library/azure/mt775197.aspx)（时序异常情况检测）

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


