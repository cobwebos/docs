---
title: 快速入门：分析远程图像 - REST、PHP
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 PHP 分析图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 83225ef3991ba7f3714ec6e18d6fd1eee3029f41
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878844"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-php-in-computer-vision"></a>快速入门：使用计算机视觉中的 REST API 和 PHP 分析远程图像

在本快速入门中，你将使用计算机视觉的 REST API 分析远程存储的图像以提取视觉特征。 使用[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)方法，可以根据图像内容提取视觉特征。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [PHP](https://secure.php.net/downloads.php)。
- 必须安装有 [Pear](https://pear.php.net)。
- 必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 安装 PHP5 [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) 包。
   1. 以管理员身份打开命令提示符窗口。
   1. 运行以下命令：

      ```console
      pear install HTTP_Request2
      ```

   1. 包安装成功后，关闭命令提示符窗口。

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `subscriptionKey` 的值替换为你的订阅密钥。
    1. 如有必要，请将 `uriBase` 的值替换为获取的订阅密钥所在的 Azure 区域中的[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)方法的终结点 URL。
    1. （可选）将 `imageUrl` 的值替换为要分析的其他图像的 URL。
    1. （可选）将 `language` 请求参数的值替换为其他语言。
1. 将代码保存为以 `.php` 为扩展名的文件。 例如，`analyze-image.php`。
1. 打开具有 PHP 支持的浏览器窗口。
1. 将文件拖放到浏览器窗口中。

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

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例网站会在浏览器窗口中分析和显示成功响应，如下例所示：

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

## <a name="clean-up-resources"></a>清理资源

不再需要文件时，请将其删除，然后卸载 PHP5 `HTTP_Request2` 包。 要卸载包，请执行以下步骤：

1. 以管理员身份打开命令提示符窗口。
2. 运行以下命令：

   ```console
   pear uninstall HTTP_Request2
   ```

3. 成功卸载包后，关闭命令提示符窗口。

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
