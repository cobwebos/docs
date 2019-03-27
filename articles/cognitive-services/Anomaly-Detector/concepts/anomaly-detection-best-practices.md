---
title: 使用异常检测器 API 的最佳实践
description: 检测异常情况检测器 API 使用的异常时，了解有关最佳做法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484026"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用异常检测器 API 的最佳实践

异常情况检测器 API 是无状态的异常情况检测服务。 准确性和其结果的性能都可能影响：

* 如何准备时序数据。
* 使用异常检测器 API 参数。
* API 请求中的数据点数目。 

使用本文章，了解如何使用为你的数据中获得最佳结果的 API 的最佳实践。 

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
