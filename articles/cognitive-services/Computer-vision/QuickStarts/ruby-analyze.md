---
title: 快速入门：分析远程图像 - REST、Ruby
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 Ruby 分析图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8ae2cd5f3a866b2d026ad4f3babc89bc9af68dc4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904484"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-ruby-in-computer-vision"></a>快速入门：使用计算机视觉中的 REST API 和 Ruby 分析远程图像

在本快速入门中，你将使用计算机视觉的 REST API 分析远程存储的图像以提取视觉特征。 使用[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)方法，可以根据图像内容提取视觉特征。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [Ruby](https://www.ruby-lang.org/en/downloads/) 2.4.x 或更高版本。
- 必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `<Subscription Key>` 替换为订阅密钥。
    1. 如果需要，将 `https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze` 替换为你在其中获取了订阅密钥的 Azure 区域中的[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 方法的终结点 URL。
    1. （可选）将 `language` 请求参数的值替换为其他语言。 
    1. （可选）将 `http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\` 替换为要分析的其他图像的 URL。
1. 将代码保存为以 `.rb` 为扩展名的文件。 例如，`analyze-image.rb`。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `ruby` 命令运行示例。 例如，`ruby analyze-image.rb`。

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze')
uri.query = URI.encode_www_form({
    # Request parameters
    'visualFeatures' => 'Categories, Description',
    'details' => 'Landmarks',
    'language' => 'en'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "categories": [
    {
      "name": "abstract_",
      "score": 0.00390625
    },
    {
      "name": "people_",
      "score": 0.83984375,
      "detail": {
        "celebrities": [
          {
            "name": "Satya Nadella",
            "faceRectangle": {
              "left": 597,
              "top": 162,
              "width": 248,
              "height": 248
            },
            "confidence": 0.999028444
          }
        ]
      }
    }
  ],
  "adult": {
    "isAdultContent": false,
    "isRacyContent": false,
    "adultScore": 0.0934349000453949,
    "racyScore": 0.068613491952419281
  },
  "tags": [
    {
      "name": "person",
      "confidence": 0.98979085683822632
    },
    {
      "name": "man",
      "confidence": 0.94493889808654785
    },
    {
      "name": "outdoor",
      "confidence": 0.938492476940155
    },
    {
      "name": "window",
      "confidence": 0.89513939619064331
    }
  ],
  "description": {
    "tags": [
      "person",
      "man",
      "outdoor",
      "window",
      "glasses"
    ],
    "captions": [
      {
        "text": "Satya Nadella sitting on a bench",
        "confidence": 0.48293603002174407
      }
    ]
  },
  "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
  "metadata": {
    "width": 1500,
    "height": 1000,
    "format": "Jpeg"
  },
  "faces": [
    {
      "age": 44,
      "gender": "Male",
      "faceRectangle": {
        "left": 593,
        "top": 160,
        "width": 250,
        "height": 250
      }
    }
  ],
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown",
      "Black"
    ],
    "accentColor": "873B59",
    "isBWImg": false
  },
  "imageType": {
    "clipArtType": 0,
    "lineDrawingType": 0
  }
}

```

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
