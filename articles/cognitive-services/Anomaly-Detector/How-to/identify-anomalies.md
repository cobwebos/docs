---
title: 如何在时序数据中使用异常探测器 API
titleSuffix: Azure Cognitive Services
description: 了解如何以批处理或流式处理数据的方式检测数据中的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840226"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：对时序数据使用异常探测器 API  

[异常探测器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供两种异常情况检测方法。 你可以在整个时间系列中以批处理方式检测异常，或者在检测到最新数据点的异常状态时生成数据。 检测模型将返回异常结果以及每个数据点的预期值和异常检测边界的上限和下限。 您可以使用这些值来可视化普通值的范围和数据中的异常。

## <a name="anomaly-detection-modes"></a>异常情况检测模式 

异常探测器 API 提供检测模式：批处理和流式处理。

> [!NOTE]
> 以下请求 Url 必须与你的订阅的相应终结点结合。 例如： `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批量检测

若要在给定的时间范围内通过一批数据点来检测异常情况，请将以下请求 URI 用于您的时序数据： 

`/timeseries/entire/detect`。 

通过一次发送时序数据，API 将使用整个序列生成一个模型，并使用它分析每个数据点。  

### <a name="streaming-detection"></a>流检测

若要持续检测流数据的异常情况，请将以下请求 URI 用于最新的数据点： 

`/timeseries/last/detect'`。 

通过在生成新数据点的同时发送这些数据点，你可以实时监视数据。 将使用您发送的数据点生成一个模型，并且该 API 将确定时序中的最晚点是否为异常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>调整较低和较高异常情况检测边界

默认情况下，将使用 `expectedValue`、`upperMargin` 和 `lowerMargin` 来计算异常检测的上边界和下限。 如果需要不同的边界，则建议将 `marginScale` 应用于 `upperMargin` 或 `lowerMargin`。 将按如下所示计算边界：

|边缘  |计算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

以下示例显示了不同区分上的异常探测器 API 结果。

### <a name="example-with-sensitivity-at-99"></a>敏感度为99的示例

![默认敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>敏感度为95的示例

![99敏感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>敏感度为85的示例

![85敏感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>后续步骤

* [什么是异常探测器 API？](../overview.md)
* [快速入门：使用异常检测器 REST API 检测时序数据的异常](../quickstarts/detect-data-anomalies-csharp.md)
