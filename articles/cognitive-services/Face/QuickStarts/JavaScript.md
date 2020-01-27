---
title: 快速入门：使用 REST API 和 JavaScript 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用认知服务中的人脸 API 和 JavaScript 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 1941cca7c13cb3d84b623222e11c13e793cee0f9
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169304"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>快速入门：使用 REST API 和 JavaScript 检测图像中的人脸

在本快速入门中，将使用 Azure 人脸 REST API 和 JavaScript 来检测图像中的人脸。

## <a name="prerequisites"></a>必备条件

- 人脸订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸服务并获取密钥。
- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>初始化 HTML 文件

创建一个新的 HTML 文件 (*detectFaces.html*)，并添加以下代码。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

然后在该文档的 `body` 元素中添加以下代码。 此代码将设置一个基本用户界面，其中包含 URL 字段、“分析人脸”  按钮、响应窗格和图像显示窗格。

```html
<h1>Detect Faces:</h1>
Enter the URL to an image that includes a face or faces, then click
the <strong>Analyze face</strong> button.<br><br>
Image to analyze: <input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg" />
<button onclick="processImage()">Analyze face</button><br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:<br><br>
        <textarea id="responseTextArea" class="UIInput"
            style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:<br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
```

## <a name="write-the-javascript-script"></a>编写 JavaScript 脚本

在该文档中 `h1` 元素的正上方添加以下代码。 此代码会设置调用人脸 API 的 JavaScript 代码。

```html
<script type="text/javascript">
    function processImage() {
        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";
    
        var uriBase =
            "https://<My Endpoint String>.com/face/v1.0/detect";
    
        // Request parameters.
        var params = {
            "returnFaceId": "true",
            "returnFaceLandmarks": "false",
            "returnFaceAttributes":
                "age,gender,headPose,smile,facialHair,glasses,emotion," +
                "hair,makeup,occlusion,accessories,blur,exposure,noise"
        };
    
        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;
    
        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),
    
            // Request headers.
            beforeSend: function(xhrObj){
                xhrObj.setRequestHeader("Content-Type","application/json");
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
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
            var errorString = (errorThrown === "") ?
                "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ?
                "" : (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                        jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
```

需使用订阅密钥的值更新 `subscriptionKey` 字段，并且可能需要更改 `uriBase` 字符串，使之包含正确的终结点字符串。 `returnFaceAttributes` 字段指定要检索的人脸属性；你可能希望根据自己的预期用途更改此字符串。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>运行脚本

在浏览器中打开 *detectFaces.html*。 单击“分析人脸”  按钮时，应用应显示给定 URL 中的图像并打印出人脸数据的 JSON 字符串。

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

以下文本是成功的 JSON 响应的示例。

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你编写了一个 JavaScript 脚本，该脚本调用 Azure 人脸服务检测图像中的人脸并返回其属性。 接下来，请浏览人脸 API 参考文档，以便进行详细的了解。

> [!div class="nextstepaction"]
> [人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
