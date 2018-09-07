---
title: 计算机视觉 API JavaScript 快速入门 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用计算机视觉和 JavaScript 分析图像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 5ae39743a812bca9716e8022c192d6a0d06b6fd4
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769312"
---
# <a name="quickstart-analyze-a-remote-image---rest-javascript"></a>快速入门：分析远程图像 - REST、JavaScript

本快速入门使用计算机视觉分析图像。

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

1. 复制以下内容并将其保存到文件，如 `analyze.html`。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `uriBase` 值更改为你获得订阅密钥的位置。
1. 将文件拖放到浏览器中。
1. 单击“`Analyze image`”按钮。

本示例使用 jQuery 1.9.0。 对于使用不带 jQuery 的 JavaScript 的示例，请参阅[智能生成缩略图](javascript-thumb.md)。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Analyze Sample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to get your
        // subscription keys. For example, if you got your subscription keys from
        // westus, replace "westcentralus" in the URI below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        // Request parameters.
        var params = {
            "visualFeatures": "Categories,Description,Color",
            "details": "",
            "language": "en",
        };

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Make the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),

            // Request headers.
            beforeSend: function(xhrObj){
                xhrObj.setRequestHeader("Content-Type","application/json");
                xhrObj.setRequestHeader(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                jQuery.parseJSON(jqXHR.responseText).message;
            alert(errorString);
        });
    };
</script>

<h1>Analyze image:</h1>
Enter the URL to an image, then click the <strong>Analyze image</strong> button.
<br><br>
Image to analyze:
<input type="text" name="inputImage" id="inputImage"
    value="http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg" />
<button onclick="processImage()">Analyze image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
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
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "73ef10ce-a4ea-43c6-aee7-70325777e4b3",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>后续步骤

浏览一款 JavaScript 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [计算机视觉 API JavaScript 教程](../Tutorials/javascript-tutorial.md)
