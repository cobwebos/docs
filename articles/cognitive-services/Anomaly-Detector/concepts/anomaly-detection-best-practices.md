---
title: 使用异常检测器 API 时的最佳做法
titleSuffix: Azure Cognitive Services
description: 了解使用异常检测器 API 检测异常时的最佳做法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721617"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用异常检测器 API 的最佳做法

异常检测器 API 是一种无状态异常检测服务。 其结果的准确性和性能可能受以下影响：

* 如何准备时间序列数据。
* 使用的异常检测器 API 参数。
* API 请求中的数据点数。 

使用本文了解使用 API 获取数据最佳结果的最佳做法。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>何时使用批次（完整）或最新（最后）点异常检测

异常检测器 API 的批处理检测终结点允许您通过整个时间系列数据检测异常。 在此检测模式下，创建单个统计模型并将其应用于数据集中的每个点。 如果时间序列具有以下特征，我们建议您在一次 API 调用中使用批处理检测来预览数据。

* 季节性时间序列，偶尔出现异常。
* 平面趋势时间序列，偶尔有峰值/下降。 

我们不建议使用批处理异常检测进行实时数据监控，或者对没有上述特征的时间序列数据使用它。 

* 批处理检测只创建并应用一个模型，每个点的检测在整个系列的上下文中完成。 如果时间序列数据在没有季节性的情况下上下移动，则模型可能会忽略某些变化点（数据中的下降和峰值）。 同样，某些更改点比数据集后期的更改点不重要，可能未计为足够重要，无法合并到模型中。

* 批处理检测比执行实时数据监视时检测最新点的异常状态要慢，因为要分析的点数。

对于实时数据监控，我们建议仅检测最新数据点的异常状态。 通过持续应用最新的点检测，可以更高效、更准确地进行流数据监控。

下面的示例描述了这些检测模式对性能的影响。 第一张图片显示了沿 28 个以前看到的数据点连续检测异常状态最新点的结果。 红色点是异常点。

![使用最新点显示异常检测的图像](../media/last.png)

下面是使用批处理异常检测的相同数据集。 为操作生成的模型忽略了几个异常，这些异常以矩形为标记。

![使用批处理方法显示异常检测的图像](../media/entire.png)

## <a name="data-preparation"></a>数据准备工作

异常检测器 API 接受格式化为 JSON 请求对象的时间序列数据。 时间序列可以是随时间顺序记录的任何数值数据。 您可以将时间序列数据的窗口发送到异常检测器 API 终结点，以提高 API 的性能。 可以发送的数据点最小数为 12，最大值为 8640 点。 [粒度](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)定义为对数据进行采样的速率。 

发送到异常检测器 API 的数据点必须具有有效的协调通用时间 （UTC） 时间戳和数值。 

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

如果数据是在非标准时间间隔内采样的，则可以通过在请求中添加`customInterval`属性来指定数据。 例如，如果每 5 分钟对系列进行采样，则可以将以下内容添加到 JSON 请求中：

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>缺少数据点

缺少数据点在均匀分布的时间序列数据集中很常见，尤其是粒度细（采样间隔小）的数据集。 例如，数据每隔几分钟采样一次。 缺少数据中预期点数的 10% 不应对检测结果产生负面影响。 请考虑根据数据的特征来填补数据的空白，例如替换早期的数据点、线性插值或移动平均线。

### <a name="aggregate-distributed-data"></a>聚合分布式数据

异常检测器 API 在均匀分布的时间序列中效果最佳。 如果数据是随机分布的，则应按时间单位（例如每分钟、每小时或每天）聚合数据。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>季节性模式数据的异常检测

如果您知道时间序列数据具有季节性模式（定期发生），则可以提高准确性和 API 响应时间。 

在构造`period`JSON 请求时指定 A 可将异常检测延迟降低多达 50%。 是`period`一个整数，它大致指定时间序列重复模式所需的数据点数。 例如，每天具有一个数据点的时间序列将`period`具有 as `7`，每小时一个点的时间序列（具有相同每周模式）将具有 。 `period` `7*24` 如果您不确定数据的模式，则不必指定此参数。

为获得最佳结果，请提供`period`4 个值的数据点，外加一个数据点。 例如，具有上述每周模式的小时数据应在请求正文 （）`7 * 24 * 4 + 1`中提供 673 个数据点。

### <a name="sampling-data-for-real-time-monitoring"></a>采样数据，用于实时监控

如果以短时间间隔（例如秒或分钟）对流数据进行采样，则发送建议的数据点数可能会超过异常检测器 API 允许的最大数量（8640 个数据点）。 如果数据显示了稳定的季节性模式，请考虑以较大的时间间隔（如小时）发送时间序列数据样本。 以这种方式对数据进行采样也可以显著提高 API 响应时间。 

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../overview.md)
* [快速入门：使用异常检测器 REST API 检测时间序列数据中的异常情况](../quickstarts/detect-data-anomalies-csharp.md)
