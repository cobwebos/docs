---
title: Azure 流分析中的异常情况检测
description: 本文介绍如何将 Azure 流分析与 Azure 机器学习一起使用以检测异常。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 706311e2895f311c228b55db971eb88a859530f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441681"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 流分析中的异常情况检测

Azure 流分析可在云和 Azure IoT Edge 中使用，它提供内置的机器学习异常情况检测功能，此功能可用于监视两种最常见的异常情况：暂时性异常和永久性异常。 使用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函数可以直接在流分析作业中执行异常情况检测。

机器学习模型采用统一采样的时序。 如果时序不统一，你可以在调用异常情况检测之前使用翻转窗口插入一个聚合步骤。

机器学习操作不支持季节性趋势或多重 variate 相关性这一次。

## <a name="model-behavior"></a>模型行为

一般情况下，模型的准确度会随着滑动窗口中数据的增多而提升。 指定的滑动窗口中的数据被视为该时间范围内其正态值范围的一部分。 检查当前事件是否为异常事件时，模型只考虑滑动窗口中的事件历史记录。 当滑动窗口移动时，将从模型的训练中逐出旧值。

函数的工作方式是根据它们到目前为止所观测到的值建立特定的法线。 通过在置信度级别内根据建立的法线进行比较来识别离群值。 窗口大小应该基于训练正常行为模型所需的最小事件数，这样，在发生异常时，该模型才能识别它。

模型的响应时间随着历史记录的大小，因为它需要与更多的过去的事件进行比较。 建议仅包含所需数量的事件，以提高性能。

时序中的间隙可能是模型在特定的时间点未接收事件而造成的。 这种情况下处理通过 Stream Analytics 使用插补的逻辑。 历史记录大小以及同一滑动窗口的持续时间用于计算事件预期抵达的平均速率。

可用的异常情况生成器[此处](https://aka.ms/asaanomalygenerator)可用于源和数据具有不同的异常模式的 Iot 中心。 可以使用这些异常情况检测函数来读取此 Iot 中心和检测异常设置 ASA 作业。

## <a name="spike-and-dip"></a>高峰和低谷

时序事件流中的暂时性异常称为高峰和低谷。 可以使用基于机器学习的运算符 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 来监视高峰和低谷。

![高峰和低谷异常示例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在同一滑动窗口中，如果第二个高峰小于第一个高峰，则相比于在指定的置信度级别内为第一个高峰计算的评分，较小高峰的计算评分可能不够明显。 你可以尝试减少模型的置信度级别，以检测此类异常。 但是，如果开始收到过多的警报，则可以使用更高的置信度间隔。

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

永久性变化的持续时间比高峰和低谷要长得多，可能表示发生了灾难性事件。 通常肉眼很难观察到永久性变化，但可以使用“AnomalyDetection_ChangePoint”运算符来检测  。

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

这些模型的性能取决于历史记录的大小、 窗口持续时间、 事件负载，以及是否使用分区函数级别。 本部分讨论这些配置，并提供如何维持 1k、 5 K 和每秒 10 个事件的引入速率的样本。

* **历史记录的大小**-这些模型使用执行线性**历史记录大小**。 时间越长历史记录大小、 较长的模型采用要评分的新事件。 这是因为模型比较为过去的事件历史记录缓冲区中的每个新的事件。
* **窗口持续时间**-**时段持续时间**应反映它所需的时间接收指定的历史记录大小的多个事件。 而无需在窗口中的许多事件，Azure Stream Analytics 会输入缺失值。 因此，CPU 占用率是历史记录大小的函数。
* **事件负载**-越大**事件负载**，将更多工作的执行模型，这将影响 CPU 占用率。 作业可以通过使其易并行，假设适合业务逻辑以使用更多输入的分区向外扩展。
* **函数级分区** - **函数级别分区**可通过使用```PARTITION BY```异常情况检测函数调用中。 这种类型的分区将添加一项开销，因为状态需要在同一时间维护多个模型。 设备级别分区这类情况下使用函数级别分区。

### <a name="relationship"></a>关系
按以下方式相关的历史记录的大小、 窗口持续时间和总事件负载：

windowDuration （以毫秒为单位） = 1000年 * historySize / (总事件每秒输入 / 输入分区计数)

按 deviceId 分区函数，将添加"按分区 deviceId"到异常情况检测函数调用。

### <a name="observations"></a>观测值
下表包含单个节点 (6 SU) 为非分区事例的吞吐量观测值：

| 历史记录的大小 （事件） | 窗口持续时间 （毫秒） | 每秒的输入的事件总数 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

下表包含单个节点 (6 SU) 为分区的情况下的吞吐量观测值：

| 历史记录的大小 （事件） | 窗口持续时间 （毫秒） | 每秒的输入的事件总数 | 设备计数 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

若要运行上面的非分区配置的示例代码位于[流式处理在规模存储库](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh)的 Azure 示例。 该代码创建流分析作业没有函数级别进行分区，它使用事件中心作为输入和输出。 输入的负载是使用测试客户端生成的。 每个输入的事件是一个为 1 KB json 文档。 事件模拟 IoT 设备发送 JSON 数据 （适用于最大为 1 万台设备）。 历史记录的大小、 窗口持续时间和总事件负载随通过 2 个输入分区而变化。

> [!Note]
> 一个更准确的估计，自定义以适合你方案的示例。

### <a name="identifying-bottlenecks"></a>找出瓶颈问题
使用 Azure Stream Analytics 作业中度量值窗格来确定在管道中的瓶颈。 审阅**输入/输出事件**吞吐量和["水印延迟"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)或**囤积的事件**以确定是否作业使用输入速率保持。 对于事件中心指标，寻找**中止请求**并相应地调整阈值单位。 有关 Cosmos DB 指标，查看**每个分区键范围的最大使用 RU/s**统一使用吞吐量，从而确保您的分区键范围下。 对于 Azure SQL DB，监视**日志 IO**并**CPU**。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>使用 Azure Stream Analytics 中的机器学习异常情况检测

以下视频演示如何使用 Azure Stream Analytics 中的机器学习函数的实时检测异常情况。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

