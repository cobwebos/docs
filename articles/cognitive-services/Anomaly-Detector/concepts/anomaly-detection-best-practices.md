---
title: 使用异常检测器 API 时的最佳做法
titleSuffix: Azure Cognitive Services
description: 了解通过异常探测器 API 检测异常时的最佳实践。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: mbullwin
ms.openlocfilehash: 6bf00d8b5327ddd539190b6e990c7edb35c876f7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018383"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用异常探测器 API 的最佳实践

异常探测器 API 是无状态异常情况检测服务。 其结果的准确性和性能可能受以下影响：

* 如何准备时序数据。
* 使用的异常探测器 API 参数。
* API 请求中的数据点数目。 

使用本文了解有关使用 API 获取数据的最佳结果的最佳实践。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>何时使用 batch (整个) 或最新 (最后) 点异常检测

异常检测程序 API 的批处理检测终结点允许您通过整个时间序列数据来检测异常情况。 在此检测模式下，将创建一个统计模型，并将其应用于数据集中的每个点。 如果时序具有以下特征，则建议使用批处理检测在一个 API 调用中预览数据。

* 季节性时序，偶尔会出现异常。
* 平面趋势时序，偶尔出现峰值/dip。 

不建议使用批处理异常检测来进行实时数据监视，也不建议在不具有以上特征的时序数据上使用它。 

* 批处理检测仅创建和应用一个模型，每个点的检测在整个序列的上下文中完成。 如果时序数据在无季节性的情况下上下趋势，则模型可能会缺少数据) 的一些变化 (dip 和峰值。 同样，某些不太重要的更改点可能不会被视为足以合并到模型中的重要部分。

* 由于正在分析的点数，批处理检测比在进行实时数据监视时检测到最新点的异常状态更慢。

对于实时数据监视，建议只检测最新数据点的异常状态。 通过持续应用最新的点检测功能，可以更有效、更准确地进行流数据监视。

下面的示例描述了这些检测模式对性能产生的影响。 第一张图显示连续检测异常状态最近点的结果，以及以前查看的数据点的28。 红色点为异常。

![显示使用最新点的异常情况检测的图像](../media/last.png)

下面是使用批处理异常情况检测的相同数据集。 为操作生成的模型已忽略几个异常，由矩形标记。

![显示使用 batch 方法的异常情况检测的图像](../media/entire.png)

## <a name="data-preparation"></a>数据准备工作

异常探测器 API 接受格式设置为 JSON 请求对象的时序数据。 时序可以是在一段时间内按顺序进行记录的任何数值数据。 可以将时序数据的 windows 发送到异常检测程序 API 终结点，以提高 API 的性能。 可以发送的数据点数的最小数目为12，最大值为8640点。 [粒度](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) 定义为对数据进行采样的速率。 

发送到异常探测器 API 的数据点必须具有有效的协调世界时 (UTC) 时间戳和数字值。 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

如果以非标准时间间隔对数据进行采样，则可以通过 `customInterval` 在请求中添加特性来指定数据。 例如，如果序列每5分钟进行一次采样，则可将以下内容添加到 JSON 请求：

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>缺少数据点

缺少的数据点在平均分布式时序数据集中常见，尤其是精细 (小型采样间隔的数据点。 例如，每隔几分钟) 采样的数据。 如果数据中的预期点数不到10%，则不会对检测结果产生负面影响。 请考虑根据数据的特性（例如，从较早的时间段、线性内插或移动平均线中替代数据点）来填充数据中的空白。

### <a name="aggregate-distributed-data"></a>聚合分布式数据

异常探测器 API 最适用于均匀分布的时间序列。 如果数据是随机分发的，则应按时间单位（例如每分钟、每小时或每天）聚合数据。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>针对具有季节性模式的数据的异常检测

如果你知道时序数据的季节性模式 (按固定间隔发生的一种情况) ，你可以提高准确性和 API 响应时间。 

在 `period` 构造 JSON 请求时指定，可将异常检测滞后时间降低到50%。 `period`是一个整数，该整数指定时序用来重复模式的数据点的数量。 例如，每日包含一个数据点的时间序列的数据点 `period` 为 `7` ，而每小时有一个点的时序 (具有相同的周模式) 将具有 `period` 的  `7*24` 。 如果你不确定数据的模式，则无需指定此参数。

为获得最佳结果，请提供4个 `period` 值得的数据点，再加上一个额外的数据点。 例如，上述按周模式列出的每小时数据应在请求正文中提供 673 () 的数据点 `7 * 24 * 4 + 1` 。

### <a name="sampling-data-for-real-time-monitoring"></a>为实时监视采样数据

如果以较短的时间间隔（例如 (的秒数或) 分钟数）对流式处理数据进行采样，则发送建议数量的数据点可能会超过异常情况检测程序 API 的最大数目， (8640 数据点) 。 如果数据显示稳定的季节性模式，请考虑以更大的时间间隔（如小时）发送时序数据的示例。 以这种方式采样你的数据也会明显缩短 API 响应时间。 

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../overview.md)
* [快速入门：使用异常检测器 REST API 检测时序数据的异常](../quickstarts/detect-data-anomalies-csharp.md)
