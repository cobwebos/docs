---
title: 如何使用异常检测器 API 将时序数据
description: 了解如何在你的数据中检测异常，作为一个批，或在流式处理数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61432296"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：使用时序数据上的异常情况检测程序 API  

[异常情况检测器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供两种方法的异常情况检测。 你可以检测异常作为批处理在你的时间序列，或你的数据生成通过检测最新的数据点的异常状态。 检测模型返回异常结果以及每个数据点的预期值和上限和下限异常情况检测边界。 这些值可用于直观显示正常值和数据中的异常的范围。

## <a name="anomaly-detection-modes"></a>异常情况检测模式 

异常情况检测器 API 将提供检测模式： 批处理和流式处理。

> [!NOTE]
> 以下请求 Url 必须结合适当的终结点为你的订阅。 例如： `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批处理检测

若要检测异常在一批数据点的整个给定的时间范围内，使用时序数据使用以下请求 URI: 

`/timeseries/entire/detect`。 

通过在一次发送时序数据，API 将使用整个系列中，生成模型和分析与它的每个数据点。  

### <a name="streaming-detection"></a>流式处理检测

若要持续检测流式处理数据的异常，请使用最新的数据点使用以下请求 URI: 

`/timeseries/last/detect'`。 

通过发送新的数据点，如生成它们，您可以监视实时数据。 将使用您发送的数据点生成模型和 API 将确定是否时序中的最新点则是反常现象。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>调整下限和上限异常情况检测边界

默认情况下，使用计算异常情况检测的上限和下限边界`expectedValue`， `upperMargin`，和`lowerMargin`。 如果需要不同的边界，我们建议应用`marginScale`到`upperMargin`或`lowerMargin`。 边界将按以下方式计算：

|边界  |计算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

下面的示例在不同的区分大小显示异常检测器 API 结果。

### <a name="example-with-sensitivity-at-99"></a>使用在 99 写规则示例

![默认敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>使用在 95 写规则示例

![99 敏感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>使用在 85 写规则示例

![85 敏感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>后续步骤

* [异常情况检测程序 API 是什么？](../overview.md)
* [快速入门：使用异常情况检测程序 REST API 将时序数据中检测异常](../quickstarts/detect-data-anomalies-csharp.md)