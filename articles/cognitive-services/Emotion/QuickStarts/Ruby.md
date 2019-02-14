---
title: 快速入门：识别图像中人脸的情感 - 情感 API、Ruby
titlesuffix: Azure Cognitive Services
description: 获取信息和代码示例，以帮助你通过 Ruby 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: b1bddbffa3a58158b3a87c4681557ff8f10a6334
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232667"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>快速入门：构建应用以识别图像中人脸的情感。

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分正式发布。 

本文提供了信息和代码示例，以帮助用户用 Ruby 识别图像中一人或多人的情感，从而快速开始使用[情感 API 识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。

## <a name="prerequisite"></a>先决条件
* 单击[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥

## <a name="recognize-emotions-ruby-example-request"></a>识别情感 Ruby 示例请求

更改 REST URL，以使用获取订阅密钥的位置，将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥，并将照片 URL 添加到 `body` 变量。

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

```

## <a name="recognize-emotions-sample-response"></a>识别情感示例响应
成功的调用将返回一组按人脸矩形大小降序排列的人脸条目及其相关表情评分。 空响应指示未检测到任何人脸。 表情条目包含以下字段：
* faceRectangle - 图像中人脸的矩形位置。
* scores - 图像中每张人脸的表情得分。

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
