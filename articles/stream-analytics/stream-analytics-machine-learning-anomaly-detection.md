---
title: Azure 流分析中的异常情况检测
description: 本文介绍如何将 Azure 流分析与 Azure 机器学习一起使用以检测异常。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: fdf98a0c0c40010bb55955b54dc7b04db8e199f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66493262"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 流分析中的异常情况检测

Azure 流分析可在云和 Azure IoT Edge 中使用，它提供内置的机器学习异常情况检测功能，此功能可用于监视两种最常见的异常情况：暂时性异常和永久性异常。 使用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函数可以直接在流分析作业中执行异常情况检测。

机器学习模型采用统一采样的时序。 如果时序不统一，你可以在调用异常情况检测之前使用翻转窗口插入一个聚合步骤。

机器学习操作不支持季节性趋势或多变体关联。

## <a name="model-accuracy-and-performance"></a>模型准确度和性能

一般情况下，模型的准确度会随着滑动窗口中数据的增多而提升。 指定的滑动窗口中的数据被视为该时间范围内其正态值范围的一部分。 检查当前事件是否为异常事件时，模型只考虑滑动窗口中的事件历史记录。 当滑动窗口移动时，将从模型的训练中逐出旧值。

函数的工作方式是根据它们到目前为止所观测到的值建立特定的法线。 通过在置信度级别内根据建立的法线进行比较来识别离群值。 窗口大小应该基于训练正常行为模型所需的最小事件数，这样，在发生异常时，该模型才能识别它。

请注意，模型的响应时间随着历史记录的大小增大而延长，因为它需要对更多的以往事件进行比较。 建议仅包含所需数量的事件，以提高性能。

时序中的间隙可能是模型在特定的时间点未接收事件而造成的。 流分析将使用插补来处理这种情况。 历史记录大小以及同一滑动窗口的持续时间用于计算事件预期抵达的平均速率。

## <a name="spike-and-dip"></a>高峰和低谷

时序事件流中的暂时性异常称为高峰和低谷。 可以使用基于机器学习的运算符 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 来监视高峰和低谷。

![高峰和低谷异常示例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在同一滑动窗口中，如果第二个高峰小于第一个高峰，则相比于在指定的置信度级别内为第一个高峰计算的评分，较小高峰的计算评分可能不够明显。 可以尝试减小模型的置信度级别设置来捕获此类异常。 但是，如果开始收到过多的警报，则可以使用更高的置信度间隔。

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

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>使用 Azure Stream Analytics 中的机器学习异常情况检测

以下视频演示如何使用 Azure Stream Analytics 中的机器学习函数的实时检测异常情况。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

