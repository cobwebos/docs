---
title: 快速入门：生成缩略图（REST、Ruby）计算机视觉
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 Ruby 基于图像生成缩略图。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 2ddd2417a1e2a7548be641eca8e72b7967ace685
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958605"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-ruby-in-computer-vision"></a>快速入门：在计算机视觉中使用 REST API 和 Ruby 生成缩略图

在本快速入门中，你将使用计算机视觉的 REST API 基于图像生成缩略图。 使用[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法，可以生成图像的缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [Ruby](https://www.ruby-lang.org/en/downloads/) 2.4.x 或更高版本。
- 必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `<Subscription Key>` 替换为订阅密钥。
    1. 必要时，请将 `https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze` 替换为你在其中获取了订阅密钥的 Azure 区域中的[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法的终结点 URL。
    1. （可选）将 `https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\` 替换为你要为其生成缩略图的另一图像的 URL。
1. 将代码保存为以 `.rb` 为扩展名的文件。 例如，`get-thumbnail.rb`。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `ruby` 命令运行示例。 例如，`ruby get-thumbnail.rb`。

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

## <a name="examine-the-response"></a>检查响应

成功的响应将以二进制数据形式返回，这些数据表示缩略图的图像数据。 如果请求失败，则响应将显示在控制台窗口中。 失败请求的响应包含错误代码和消息，用以帮助确定出错的地方。

## <a name="clean-up-resources"></a>清理资源

不再需要该文件时，请将其删除。

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
