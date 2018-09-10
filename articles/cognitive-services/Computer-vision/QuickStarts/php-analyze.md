---
title: 快速入门：分析远程图像 - REST、PHP - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 本快速入门将在认知服务中使用计算机视觉和 PHP 分析图像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4865943fb5b903695f9d79b13624cbe8a94b8cd7
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841006"
---
# <a name="quickstart-analyze-a-remote-image---rest-php---computer-vision"></a>快速入门：分析远程图像 - REST、PHP - 计算机视觉

本快速入门将使用计算机视觉分析图像以提取视觉特征。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="analyze-image-request"></a>Analyze Image 请求

使用 [Analyze Image 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，可以根据图像内容提取视觉特征。 可以上传图像或指定图像 URL 并选择要返回的特征，包括：

* 与图像内容相关的标记的详细列表。
* 用完整句子描述图像内容。
* 图像中包含的任何人脸的坐标、性别和年龄。
* 图像类型（剪贴画或线条图）。
* 主色、主题色或图像是否为黑白。
* 此[分类](../Category-Taxonomy.md)中定义的类别。
* 图像是否包含成人内容或性暗示内容？

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，请更改 `uriBase` 以使用获得订阅密钥的位置。
1. （可选）将 `imageUrl` 设置为要分析的图像。
1. （可选）更改响应语言 (`'language' => 'en'`)。
1. 使用 `.php` 扩展名保存文件。
1. 在 PHP 支持的浏览器窗口中打开该文件。

此示例使用 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 包。

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="analyze-image-response"></a>Analyze Image 响应

JSON 中返回了成功的响应，例如：

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和特征点，创建缩略图以及提取打印文本和手写文本的计算机视觉 API。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
