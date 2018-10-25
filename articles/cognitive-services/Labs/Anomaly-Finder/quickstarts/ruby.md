---
title: 如何将异常查找器 API 与 Ruby 配合使用 - Microsoft 认知服务 | Microsoft Docs
description: 获取信息和示例代码，帮助用户快速开始使用 Ruby 和认知服务中的异常查找器 API。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 97b02bfc2e388757306ae6180cf66f6870437218
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457623"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>将异常查找器 API 与 Ruby 配合使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

本文提供了相关信息和示例代码，帮助用户快速开始将异常查找器 API 与 Ruby 配合使用，从而获取时序数据的异常情况检测结果。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>通过使用 Ruby 的异常查找器 API 获取异常点 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>时序数据示例
时序数据点示例如下，

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>分析数据，并获取异常点 Ruby 示例

使用该示例的步骤如下。

1. 通过运行“gem install rest-client”安装 [rest-client](https://github.com/rest-client/rest-client)。
2. 将下面的代码保存为 .rb 文件。
3. 将 `[YOUR_SUBSCRIPTION_KEY]` 值替换为有效订阅密钥。
4. 将 `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` 替换为示例或你自己的数据点。
5. 执行并检查响应。

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 示例响应如下。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
