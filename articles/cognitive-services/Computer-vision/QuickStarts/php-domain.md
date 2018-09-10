---
title: 快速入门：使用域模型 - REST、PHP - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用认知服务中的计算机视觉和 PHP，通过域模型来识别图像中的特征点。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 5c13daa5802852916e9ae4d1e694ddcef888bbb7
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841089"
---
# <a name="quickstart-use-a-domain-model---rest-php---computer-vision"></a>快速入门：使用域模型 - REST、PHP - 计算机视觉

在本快速入门中，你将使用计算机视觉通过域模型来识别图像中的特征点或名人。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要一个订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="recognize-landmark-request"></a>识别特征点请求

使用[“识别特定于域的内容”方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200)，可以标识图像中的特定对象集。 目前可用的两个特定于域的模型为 _celebrities_ 和 _landmarks_。

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，请更改 `uriBase` 以使用获得订阅密钥的位置。
1. （可选）将 `imageUrl` 设置为要分析的图像。
1. （可选）将 `domain` 设置为 `celebrities` 以使用名人模型。
1. 使用 `.php` 扩展名保存文件。
1. 在 PHP 支持的浏览器窗口中打开该文件。

以下示例识别图像中的特征点。

此示例使用 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 包。

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
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

## <a name="recognize-landmark-response"></a>识别特征点响应

成功的响应以 JSON 格式返回，例如：

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和地标，创建缩略图以及提取打印文本和手写文本的计算机视觉 API。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
