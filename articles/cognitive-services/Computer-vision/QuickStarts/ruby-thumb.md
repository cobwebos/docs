---
title: 计算机视觉 API Ruby 快速入门 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用计算机视觉和 Ruby 从图像生成缩略图。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a5f6c345b3e1f5e4dab923d43dd239344c66aab1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769291"
---
# <a name="quickstart-generate-a-thumbnail---rest-ruby"></a>快速入门：生成缩略图 - REST、Ruby

本快速入门使用计算机视觉从图像生成缩略图。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="get-thumbnail-request"></a>Get Thumbnail 请求

使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)，可以生成图像的缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `uri` 值更改为你获得订阅密钥的位置。
1. （可选）更改要分析的图像 (`{\"url\":\"...`)。
1. 使用 `.rb` 扩展名保存文件。
1. 打开 Ruby 命令提示符并运行该文件，例如：`ruby myfile.rb`。

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="get-thumbnail-response"></a>Get Thumbnail 响应

成功的响应包含缩略图二进制文件。 如果请求失败，则响应包含错误代码和消息，以帮助确定出错的地方。

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和地标，创建缩略图以及提取打印文本和手写文本的计算机视觉 API。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
