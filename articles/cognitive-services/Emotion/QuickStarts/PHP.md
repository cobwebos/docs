---
title: 情感 API PHP 快速入门 | Microsoft Docs
description: 获取信息和示例代码，帮助用户通过认知服务中的 PHP 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 987d5a3eedaa17f1127be34e5f90ec2456fab99b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019391"
---
# <a name="emotion-api-php-quick-start"></a>情感 API PHP 快速入门

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松从视频提取见解，增强搜索结果等内容发现体验。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供了帮助用户快速开始使用 PHP 的信息和示例代码以及用于识别图片中一人或多人的情感的[情感 API 识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。 

## <a name="prerequisite"></a>先决条件
* 在[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥

## <a name="recognize-emotions-php-example-request"></a>识别情感 PHP 示例请求

更改 REST URL，以使用获取订阅密钥的位置，将正文更改为想要测试的图像的 URL，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
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

