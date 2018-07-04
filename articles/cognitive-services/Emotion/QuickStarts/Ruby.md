---
title: 情感 API Ruby 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助用户通过认知服务中的 Ruby 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 733127bb3656d86a7f3f57cd26c72909900f4899
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021043"
---
# <a name="emotion-api-ruby-quick-start"></a>情感 API Ruby 快速入门

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松从视频提取见解，增强搜索结果等内容发现体验。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供了信息和代码示例，以帮助用户用 Ruby 识别图像中一人或多人的情感，从而快速开始使用[情感 API 识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。

## <a name="prerequisite"></a>先决条件
* 在[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥

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
