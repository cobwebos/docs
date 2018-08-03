---
title: 情感 API JavaScript 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助用户通过认知服务中的 JavaScript 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072086"
---
# <a name="emotion-api-javascript-quick-start"></a>情感 API JavaScript 快速入门

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用全新的[视频索引器 API（预览版）](https://azure.microsoft.com/services/cognitive-services/video-indexer/)。它可以检测口语、人脸、字符和情感，不仅便于从视频中轻松提取见解，还能增强内容发现体验（如搜索结果）。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供了信息和代码示例，以帮助用户用 JavaScript 识别图像中一人或多人的情感，从而快速开始使用[情感 API 识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。

## <a name="prerequisite"></a>先决条件
* 在[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥，如果你有 Azure 订阅，则创建情感 API 资源并在其中获取订阅密钥和终结点。

![创建情感 API 资源](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>识别情感 JavaScript 示例请求

复制以下内容并将其保存到文件，如 `test.html`。 更改请求 `url`，以使用获取订阅密钥的位置，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。 可以分别在 Azure 门户的情感 API 资源的“概述”和“密钥”部分找到这些信息。 

![API 终结点](../Images/api-url.png)

![API 订阅密钥](../Images/keys.png)

将请求正文更改为要使用的图像的位置。 要运行示例，将文件拖放到浏览器中。

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
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
