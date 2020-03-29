---
title: 如何在时间序列数据上使用异常检测器 API
titleSuffix: Azure Cognitive Services
description: 了解如何检测数据中的异常，无论是批处理还是流数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840226"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：在时间序列数据上使用异常检测器 API  

[异常检测器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供了两种异常检测方法。 您可以在整个时间序列中检测异常作为批处理，也可以通过检测最新数据点的异常状态生成数据。 检测模型返回异常结果以及每个数据点的预期值以及上下异常检测边界。 您可以使用这些值来可视化正常值的范围和数据中的异常。

## <a name="anomaly-detection-modes"></a>异常检测模式 

异常检测器 API 提供检测模式：批处理和流式处理。

> [!NOTE]
> 以下请求 URL 必须与订阅的相应终结点结合使用。 例如： `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批量检测

要检测给定时间范围内一批数据点的异常，请使用以下请求 URI 与时间序列数据： 

`/timeseries/entire/detect`. 

通过一次发送时间序列数据，API 将使用整个系列生成模型，并用它来分析每个数据点。  

### <a name="streaming-detection"></a>流式检测

要持续检测流数据上的异常，请使用以下请求 URI 与最新的数据点： 

`/timeseries/last/detect'`. 

通过在生成新数据点时发送数据点，可以实时监视数据。 将生成一个模型，并生成您发送的数据点，API 将确定时间序列中的最新点是否为异常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>调整下部和上部异常检测边界

默认情况下，使用`expectedValue`和`upperMargin`计算异常检测的上边界和下边界。 `lowerMargin` 如果您需要不同的边界，我们建议对`marginScale``upperMargin`或`lowerMargin`应用 。 边界的计算方式如下：

|边界  |计算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

以下示例显示了异常检测器 API 结果，其敏感性不同。

### <a name="example-with-sensitivity-at-99"></a>灵敏度为 99 的示例

![默认敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>灵敏度为 95 的示例

![99 灵敏度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>灵敏度为 85 的示例

![85 灵敏度](../media/sensitivity_85.png)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../overview.md)
* [快速入门：使用异常检测器 REST API 检测时间序列数据中的异常情况](../quickstarts/detect-data-anomalies-csharp.md)
