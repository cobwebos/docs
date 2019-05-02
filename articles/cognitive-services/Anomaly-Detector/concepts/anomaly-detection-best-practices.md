---
title: 使用异常检测器 API 时的最佳做法
description: 检测异常情况检测器 API 使用的异常时，了解有关最佳做法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 766d009be3cd664d928a3c12f5fea38c26bbbdde
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692200"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用异常检测器 API 的最佳实践

异常情况检测器 API 是无状态的异常情况检测服务。 准确性和其结果的性能都可能影响：

* 如何准备时序数据。
* 使用异常检测器 API 参数。
* API 请求中的数据点数目。 

使用本文章，了解如何使用为你的数据中获得最佳结果的 API 的最佳实践。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>何时使用 batch （整个） 或最新版本 （上一次） 点异常情况检测

异常情况检测器 API batch 检测终结点可让你通过在整个时间序列数据的异常。 在此检测模式下，单个统计模型创建并应用于数据集中的每个点。 如果时间序列具有以下特征，我们建议使用批处理检测来预览一个 API 调用中的数据。

* 包含偶尔的异常情况的季节性时间序列。
* 平缓趋势时间序列，与偶发的峰值 dip。 

我们不建议使用实时数据监视，或使用不具有以上特征的时序数据的批处理的异常情况检测。 

* 批处理检测创建并应用只有一个模型，每个点检测完成整个系列的上下文中。 如果时间系列数据趋势向上和向下而无需季节性，某些点的更改 （dip 和数据中的峰值） 可能会缺失模型。 同样，没有太大关系比更高版本中数据集更改的一些点可能不计为明显，无法合并到模型。

* 批处理检测低于执行实时数据监视，因为所分析的点数量时检测最新点的异常状态。

用于监视实时数据，我们建议检测你最新数据点的异常状态。 通过连续应用最新点检测，流式处理数据监视可以进行更有效地准确地。

下面的示例介绍了这些检测模式会对性能产生的影响。 第一张图显示的持续检测异常情况状态沿 28 先前已见到的数据点的最新点的结果。 红色的点是异常。

![显示使用最新点的异常情况检测的图像](../media/last.png)

下面是相同的数据集使用 batch 异常情况检测。 为操作生成的模型已忽略由矩形标记的多个异常。

![显示使用批处理方法的异常情况检测的图像](../media/entire.png)

## <a name="data-preparation"></a>数据准备工作

异常情况检测器 API 接受时间序列数据格式化为 JSON 请求对象。 时间序列可以是一段时间按先后顺序记录任何数值数据。 可以将时序数据的窗口发送到异常情况检测器 API 终结点，以提高 API 的性能。 可以发送的数据点的最小数目为 12，并最大值是 8640 点。 

发送到异常情况检测程序 API 的数据点必须具有有效的协调世界时 (UTC) 时间戳和数字值。 

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

### <a name="missing-data-points"></a>缺失的数据点

缺失的数据点是常用于平均分布的时间系列数据集，尤其是具有细粒度 （较小的采样间隔。 例如，对数据采样每隔几分钟)。 缺少少于 10%的数据中的点的预期数目不应在检测结果产生负面影响。 请考虑填充根据其特性，如替换从更早的期间，线性内插或移动平均线的数据点对数据中的缺口。

### <a name="aggregate-distributed-data"></a>聚合的分布式的数据

平均分布的时序异常情况检测器 API 效果最佳。 如果随机分布数据，您应它按聚合的时间，例如每分钟，每小时或每日单位为例。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>对数据进行季节性模式的异常情况检测

如果您知道时序数据的季节性模式 （一个定期出现），则可以提高准确性和 API 响应时间。 

指定`period`JSON 请求在构造时可以减少 50%的异常情况检测延迟。 `period`是一个整数，指定大致多少数据点的时间系列重复所需模式。 例如，具有包含每天创建一个数据点的时间序列`period`作为`7`，并且必须包含每个小时 （使用相同的每周模式） 的一个点的时间序列`period`的`7*24`。 如果您不确定的数据的模式，您无需指定此参数。

为获得最佳结果，提供 4`period`的积累的数据点，以及另一个。 例如，每小时数据每周模式与上文所述应提供 673 请求正文中的数据点 (`7 * 24 * 4 + 1`)。

### <a name="sampling-data-for-real-time-monitoring"></a>实时监视的采样数据

如果流式处理数据进行采样在短暂的间隔 （例如秒或分钟），发送的建议的数据点数目可能超过异常情况检测程序 API 的最大数字允许 （8640 数据点）。 如果你的数据显示了稳定的季节性模式，请考虑在更大的时间间隔，如小时发送的时序数据的示例。 采样数据以这种方式可以还显著提高 API 响应时间。 

## <a name="next-steps"></a>后续步骤

* [异常情况检测程序 API 是什么？](../overview.md)
* [快速入门：使用异常情况检测程序 REST API 将时序数据中检测异常](../quickstarts/detect-data-anomalies-csharp.md)
