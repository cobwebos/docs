---
title: Azure 流分析中的异常情况检测
description: 本文介绍如何将 Azure 流分析与 Azure 机器学习一起使用以检测异常。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525526"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 流分析中的异常情况检测

Azure 流分析可在云和 Azure IoT Edge 中使用，它提供内置的机器学习异常情况检测功能，此功能可用于监视两种最常见的异常情况：暂时性异常和永久性异常。 使用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函数可以直接在流分析作业中执行异常情况检测。

机器学习模型采用统一采样的时序。 如果时序不统一，你可以在调用异常情况检测之前使用翻转窗口插入一个聚合步骤。

目前，机器学习操作不支持季节性趋势或多变量相关。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>使用 Azure 流分析中的机器学习的异常情况检测

以下视频演示了如何使用 Azure 流分析中的机器学习函数实时检测异常。 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>模型行为

一般情况下，模型的准确度会随着滑动窗口中数据的增多而提升。 指定的滑动窗口中的数据被视为该时间范围内其正态值范围的一部分。 检查当前事件是否为异常事件时，模型只考虑滑动窗口中的事件历史记录。 当滑动窗口移动时，将从模型的训练中逐出旧值。

函数的工作方式是根据它们到目前为止所观测到的值建立特定的法线。 通过在置信度级别内根据建立的法线进行比较来识别离群值。 窗口大小应该基于训练正常行为模型所需的最小事件数，这样，在发生异常时，该模型才能识别它。

由于历史记录大小需要比较较多的过去事件，因此模型的响应时间会随之增加。 建议仅包含所需数量的事件，以提高性能。

时序中的间隙可能是模型在特定的时间点未接收事件而造成的。 此情况由流分析使用插补法逻辑来处理。 历史记录大小以及同一滑动窗口的持续时间用于计算事件预期抵达的平均速率。

[此处](https://aka.ms/asaanomalygenerator)提供的异常生成器可用于向包含不同异常模式的数据馈送 Iot 中心。 可以使用这些异常情况检测函数来设置 ASA 作业，以便从此 Iot 中心读取和检测异常。

## <a name="spike-and-dip"></a>高峰和低谷

时序事件流中的暂时性异常称为高峰和低谷。 可以使用基于机器学习的运算符 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 来监视高峰和低谷。

![高峰和低谷异常示例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在同一滑动窗口中，如果第二个高峰小于第一个高峰，则相比于在指定的置信度级别内为第一个高峰计算的评分，较小高峰的计算评分可能不够明显。 可以尝试降低模型的置信度，以检测此类异常。 但是，如果开始收到过多的警报，则可以使用更高的置信度间隔。

以下示例查询假设在 2 分钟的滑动窗口中，以每秒 1 个事件的统一速率输入事件，历史记录中包含 120 个事件。 最终的 SELECT 语句将提取事件，并输出评分和置信度级别为 95% 的异常状态。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>变化点

时序事件流中的永久性异常是指事件流中的值分布变化，例如级别变化和趋势。 在流分析中，将使用基于机器学习的 [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 运算符检测此类异常。

永久性变化的持续时间比高峰和低谷要长得多，可能表示发生了灾难性事件。 通常肉眼很难观察到永久性变化，但可以使用“AnomalyDetection_ChangePoint”运算符来检测。

下图是级别变化的示例：

![级别变化异常的示例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

下图是趋势变化的示例：

![趋势变化异常的示例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

以下示例查询假设在 20 分钟的滑动窗口中，以每秒 1 个事件的统一速率输入事件，历史记录大小为 1200 个事件。 最终的 SELECT 语句将提取事件，并输出评分和置信度级别为 80% 的异常状态。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>性能特征

这些模型的性能取决于历史记录大小、窗口持续时间、事件负载以及是否使用函数级别分区。 本部分将讨论这些配置，并提供有关如何维持每秒1K

* **历史记录大小**-这些模型以线性方式与**历史记录大小**一起执行。 历史记录大小越长，模型为新事件评分所需的时间就越长。 这是因为这些模型将新的事件与历史缓冲区中的每个过去事件进行比较。
* **窗口持续时间**-**窗口持续时间**应反映接收由历史记录大小指定的多个事件所用的时间。 如果窗口中没有这多事件，Azure 流分析会归结缺失值。 因此，CPU 消耗是历史记录大小的一个函数。
* **事件负载**-**事件负载**越高，模型所执行的工作就越多，从而影响 CPU 消耗。 此作业可以通过使其易并行来进行扩展，假设业务逻辑可以使用更多输入分区。
* **函数级别分区** - **函数级别分区**是通过在异常检测函数调用中使用 ```PARTITION BY``` 来完成的。 这种类型的分区增加了开销，因为需要同时为多个模型维护状态。 在设备级别分区等方案中使用函数级别分区。

### <a name="relationship"></a>关系
历史记录大小、时段和总事件负载按以下方式相关：

windowDuration （以毫秒为单位） = 1000 * historySize/（每秒输入事件总数/输入分区计数）

按 deviceId 对函数进行分区时，请将 "PARTITION BY deviceId" 添加到异常检测函数调用。

### <a name="observations"></a>观测
下表包括针对非分区事例的单个节点（6 SU）的吞吐量观察值：

| 历史记录大小（事件） | 窗口持续时间（毫秒） | 每秒输入事件总数 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

下表包含对分区事例的单个节点（6 SU）的吞吐量观察值：

| 历史记录大小（事件） | 窗口持续时间（毫秒） | 每秒输入事件总数 | 设备计数 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

用于运行上述非分区配置的示例代码位于 Azure 示例的[大规模](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh)存储库中。 此代码将创建一个不带函数级别分区的流分析作业，该作业使用事件中心作为输入和输出。 输入负载是使用测试客户端生成的。 每个输入事件都是一个 1KB json 文档。 事件模拟发送 JSON 数据的 IoT 设备（最多支持1K 个设备）。 历史记录大小、时段和总事件负载随2个输入分区而变化。

> [!Note]
> 若要获得更准确的估计值，请根据你的方案自定义示例。

### <a name="identifying-bottlenecks"></a>确定瓶颈
使用 Azure 流分析作业中的 "指标" 窗格识别管道中的瓶颈。 检查吞吐量和["水印延迟"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)的**输入/输出事件**，或查看**囤积的事件**，查看作业是否与输入速率保持一致。 对于事件中心指标，请查找**限制的请求**并相应地调整阈值单位。 对于 Cosmos DB 度量值，查看 "吞吐量" 下**每个分区键范围内使用的最大 RU 数/秒**，以确保对分区键范围进行统一使用。 对于 Azure SQL DB，监视**日志 IO**和**CPU**。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

