---
title: Azure 用法指南（预览版）中的异常检测 | Microsoft Docs
description: 使用流分析和机器学习来检测异常。
services: stream-analytics
documentationcenter: ''
author: dubansal
manager: katicad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: 9d301f8586038f635ee97a3acdc9c4dc8a2bbcc6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 流分析中的异常检测

> [!IMPORTANT]
> 此功能目前以预览版提供，不建议在生产环境中使用。

可以使用 **AnomalyDetection** 运算符在事件流中检测不同类型的异常。 例如，在一段较长时间内可用内存缓慢减少，可能预示存在内存泄露，或稳定在一定范围的 Web 服务请求数有可能显著增加或减少。  

AnomalyDetection 运算符检测三种类型的异常： 

* **双向级别更改**：值的级别持续向上和向下增大或减小。 此值不同于峰值和降值，属于瞬时或短时间的更改。  

* **慢速正趋势**：一段时间内趋势的缓慢递增。  

* **慢速负趋势**：一段时间内趋势的缓慢递减。  

使用 AnomalyDetection 运算符时，必须指定 **Limit Duration** 子句。 此子句指定在检测异常时应考虑时间间隔（历史记录发生在当前事件之前的多长时间）。 可以选择使用  **When**  子句将此运算符限制为仅匹配某种属性或条件的事件。 此运算符还可根据  **Partition by**  子句中指定的关键字选择单独处理事件组。 可对每个分区独立进行训练和预测。 

## <a name="syntax-for-anomalydetection-operator"></a>AnomalyDetection 运算符的语法

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**示例用法**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>参数

* **scalar_expression** - 执行异常检测所用的标量表达式。 此参数的允许值包括返回单个（标量）值的浮点数据类型或 Bigint 数据类型。 不可使用通配符表达式**\***。 scalar_expression 不能包含其他分析函数或外部函数。 

* **partition_by_clause** - `PARTITION BY <partition key>` 子句将学习和训练划分为不同分区。 换言之，将根据 `<partition key>` 的值使用单独的模型，仅含有该值的事件才会用于该模型中的学习和培训。 例如，以下查询训练一个读数，并仅对照同一传感器的其他读数进行评分：

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration 子句** `DURATION(<unit>, <length>)` - 指定在检测异常时应考虑时间间隔（历史记录发生在当前事件之前的多长时间）。 有关受支持的单位及其缩写的详细说明，请参阅 [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics)。 

* **when_clause** - 为异常检测计算中考虑的事件指定布尔条件。

### <a name="return-types"></a>返回类型

AnomalyDetection 运算符返回一个包含所有三种评分的记录作为输出。 与不同类型异常检测器相关联的属性为：

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

要从记录中提取单个值，请使用 GetRecordPropertyValue 函数。 例如：

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

上述异常评分如果有一项超过阈值，将检测到某个异常。 阈值可以是任意浮点数 >= 0。 阈值是敏感度和置信度之间的权衡值。 例如，过低的阈值使得检测对变化更敏感，生成更多警报，而较高的阈值会使检测的敏感性下降，其置信度虽然变高，但容易过滤某些异常。 阈值的精确值取决于具体情况。 虽然不设上限，但建议使用 3.25-5 范围内的值。 

## <a name="anomaly-detection-algorithm"></a>异常检测算法

* AnomalyDetection 运算符使用**非监督式学习法**，不假设事件中存在任何类型的分布。 一般情况下，在任意给定时间同时维护两个模型，其中至少一个模型用于评分，另一个模型在后台训练。 异常检测模型是使用当前流中的数据训练的，而不是使用带外机制训练的。 用于训练的数据量取决于用户在 Limit Duration 参数中指定的窗口大小 d。 每个模型最终根据 d 到 2d 事件值进行训练。 为获得最佳结果，建议在每个窗口中至少保留 50 个事件。 

* AnomalyDetection 运算符使用**滑动窗口语义**来训练模型以及为事件评分。 这意味着，将评估每个事件中的异常并返回评分。 评分表示该异常的置信度级别。 

* AnomalyDetection 运算符提供**可重复性保证**，相同的输入始终生成相同的评分，不管作业输出开始时间如何。 作业输出开始时间表示作业生成第一个输出事件的时间。 用户可将此值设置为当前时间、自定义值或上次输出时间（如果作业以前生成了输出）。 

### <a name="training-the-models"></a>训练模型 

随着时间的推移，将使用不同的数据训练模型。 若要生成有意义的评分，最好是了解用于训练模型的基础机制。 此处，**t<sub>0</sub>** 是**作业输出开始时间**，**d** 是 Limit Duration 参数中的**窗口大小**。 假设时间划分为从 `01/01/0001 00:00:00` 开始的**大小为 d 的跃点**。 使用以下步骤训练模型并为事件评分：

* 当作业启动时，它会从 t<sub>0</sub> – 2d 开始读取数据。  
* 当时间到达下一跃点时，将创建新模型 M1 并开始对其进行训练。  
* 当时间继续转到下一跃点时，将创建新模型 M2 并开始对其进行训练。  
* 当时间到达 t<sub>0</sub> 时，将会激活 M1 并开始输出其评分。  
* 在下一跃点，同时执行三个操作：  

  * 不再需要 M1，因此将其丢弃。  
  * M2 已经过足够的训练，可用于评分。  
  * 创建新模型 M3，并开始在后台对其进行训练。  

* 此周期针对每个跃点重复：丢弃活动的模型、切换到并行模型，然后开始在后台训练第三个模型。 

步骤图示如下： 

![训练模型](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **训练开始时间** | **开始使用模型评分的时间** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* 模型 M1 在上午 11:20 开始训练，这是作业开始在上午 11:13 读取数据后到达的下一跃点。 在使用 13 分钟的数据训练 M1 后，它会在上午 11:33 生成第一个输出。 

* 新模型 M2 也开始在上午 11:30 训练，但只会在使用 10 分钟的数据训练该模型之后，才会在上午 11:40 开始使用其评分。 

* M3 遵循与 M2 相同的模式。 

### <a name="scoring-with-the-models"></a>使用模型评分 

在机器学习级别，异常检测算法通过将每个传入事件的奇异值与历史记录窗口中的事件进行比较来计算该值。 每种类型的异常的奇异值计算各不相同。  

让我们详细了解奇异值计算（假设存在一组包含事件的历史窗口）： 

1. **双向级别更改：**根据历史记录窗口，正常操作范围计算方式为 [第 10 百分位, 第 90 百分位]，即，第 10 百分位表示下限，第 90 百分位值表示上限。 当前事件的奇异值计算方式为：  

   - 如果 event_value 在正常操作范围内，则为 0  
   - 如果 event_value > 90th_percentile，则为 event_value/90th_percentile  
   - 如果 event_value < 10th_percentile，则为 10th_percentile/event_value  

2. **慢速正趁势：**根据历史记录窗口中的事件值计算趋势线，可以观察该线中的正趋势。 奇异值计算方式为：  

   - 如果斜率为正，则为斜率  
   - 否则为 0 

1. **慢速负趁势：**根据历史记录窗口中的事件值计算趋势线，可以观察该线中的负趋势。 奇异值计算方式为： 

   - 如果斜率为负，则为斜率  
   - 否则为 0  

计算传入事件的奇异值后，可根据计算该奇异值计算鞅值（有关如何计算鞅值的详细信息，请参阅[机器学习博客](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)）。 此鞅值作为异常评分返回。 鞅值在响应奇异值时缓慢递增，因此，检测器能够稳定地检测偶发性更改并减少误报。 它也是一个有用的属性： 

概率 [例如 M<sub>t</sub> > λ] < 1/λ，其中，M<sub>t</sub> 是瞬时 t 处的鞅值，λ 是实际值。 例如，如果 M<sub>t</sub>> 100 时发出警报，则误报概率小于 1/100。  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>有关使用双向级别更改检测器的指导 

可以在断电、恢复或高峰流量等场合下使用双向级别更改检测器。但是，它的工作方式为：一旦使用某些数据对模型进行训练，则当且仅当新值高于前一上限（对于向上级别更改的情况）或低于前一下限（对于向下级别更低的情况）时，另一个级别更改才是异常的。 因此，模型在其训练窗口的新级别（向上或向下）范围中，不会看到任何被视为异常的数据值。 

使用此检测器时，请注意以下几点： 

1. 当时序突然发现值增大或下降时，AnomalyDetection 运算符会检测该更改。 但检测恢复正常状态需要更多的规划。 时序在异常之前是否处于稳定状态。这可以通过将 AnomalyDetection 运算符的检测窗口设置为异常长度的最多一半来实现这种检测。 此方案假设可以提前评估异常的最短持续时间，并且该时间范围中有足够的事件可用于充分训练模型（至少 50 个事件）。 

   下图 1 和 2 使用上限更改显示了此方案（相同的逻辑适用于下限更改）。 在这两个示意图中，波形表示异常级别更改。 橙色竖线表示跃点边界，跃点大小与 AnomalyDetection 运算符中指定的检测窗口相同。 绿线表示训练窗口的大小。 在图 1 中，跃点大小与异常持续时间相同。 在图 2 中，跃点大小是异常持续时间的一半。 在所有情况下，都会检测向上更改，因为用于评分的模型是根据正常数据训练的。 但是，根据双向级别更改检测器的工作原理，我们必须从为恢复正常状态评分的模型的训练窗口中排除正常值。 在图 1 中，评分模型的训练包括一些正常事件，因此无法检测恢复正常状态。 但在图 2 中，训练仅包括异常部分，因此可确保检测恢复正常状态。 出于相同的原因，任何小于一半的项目也适用，而任何更大的项目最终会包括一部分正常事件。 

   ![窗口大小等于异常长度的 AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![窗口大小等于异常长度一半的 AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. 如果无法预测异常的长度，则此探测器会尽最大努力检测。 但是，选择较窄的时间窗口会限制训练数据，从而提高检测到恢复正常状态的概率。 

3. 在以下方案中，检测不到较长持续时间的异常，因为训练窗口已包括具有相同高值的异常。 

   ![大小相同的异常](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>用于检测异常的示例查询 

可使用下列查询在检测到异常时输出警报。
当输入流不一致时，聚合步骤可帮助将其转换为一致的时序。 该示例使用了 AVG，但聚合的特定类型取决于用户方案本身。 此外，当时序的间隙大于聚合窗口时，（根据滑动窗口语义）时序中将不再有任何能触发异常检测的事件。 因此，当下一事件到达时，一致性假设将不成立。 在这种情况下，应在时序中填充间隙。 一种可行方法是在每个跳跃窗口中采用最后一个事件，如下所示。

```sql
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

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>性能指南

* 为作业使用六个流单元。 
* 以最少一秒的间隔发送事件。
* 使用 AnomalyDetection 运算符的非分区查询产生结果时，计算延迟平均约为 25 毫秒。
* 随着计算量的增加，分区查询中出现的延迟将与分区数略有不同。 但是，延迟与所有分区中可比较的事件总数的非分区事例大致相同。
* 在读取非实时数据时，快速引入了大量数据。 目前，处理此数据的速度会减慢。 此类情况中的延迟会随窗口中数据点数目变化呈线性增加，而不受窗口大小或事件间隔本身的影响。 要降低非实时事例中的延迟，请考虑使用较小的窗口大小。 或者，考虑从当前时间开始作业。 以下是关于非分区查询中延迟的几个示例： 
    - 检测窗口中如果有 60 个数据点，就能造成时长 10 秒的延迟，吞吐量为 3 MBps。 
    - 当数据点为 600 个时，延迟可高达 80 秒，吞吐量将至 0.4 MBps。

## <a name="limitations-of-the-anomalydetection-operator"></a>AnomalyDetection 运算符的限制 

* 目前，此运算符不支持峰值和下降检测。 峰值和下降是时序的自发性或短时间更改。 

* 当前，此运算符无法处理季节性模式。 季节性模式是数据重复模式，例如，周末出现的不同 Web 流量行为，或旺季出现的不同购物趁势，这都不属于异常，而是预期的行为模式。 

* 此运算符期望输入时列是一致的。 通过聚合翻转或跳跃窗口可以使事件流一致。 当事件间的间隙总是小于聚合窗口时，一个翻转窗口就足以使时序一致。 当间隙较大时，可使用跳跃窗口重复最后一个值来填充间隙。 

## <a name="references"></a>参考

* [异常检测 - 使用机器学习在时序数据中检测异常](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [机器学习异常检测 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [时序异常检测](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

