---
title: 如何将异常查找器 API 与 cURL 配合使用 - Microsoft 认知服务 | Microsoft Docs
description: 获取信息帮助用户快速开始使用 cURL 和认知服务中的异常查找器。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366039"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>将异常查找器 API 与 cURL 配合使用

本文提供了相关信息和示例代码，帮助用户快速开始将异常查找器 API 与 cURL 配合使用，从而获取时序数据异常结果。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>通过使用 cURL 的异常查找器 API 获取异常点 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>时序数据示例

时序数据点示例如下。

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>分析数据，并获取异常点 cURL 示例

该示例的使用步骤如下。

1. 将 `[YOUR_SUBSCRIPTION_KEY]` 值替换为有效订阅密钥。
2. 将 `[YOUR_REGION]` 替换为获取订阅密钥时使用的位置。
3. 将 `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` 替换为示例数据点或自己的数据点。
4. 执行并检查响应。

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>示例响应
JSON 中返回成功响应。 示例响应如下：[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
