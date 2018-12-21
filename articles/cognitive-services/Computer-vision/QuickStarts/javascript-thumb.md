---
title: 快速入门：生成缩略图 - REST、JavaScript - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 JavaScript 基于图像生成缩略图。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 9d66e6cf7e858a28d3f78398f83228a7ccf7e5a7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970996"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-javascript-in-computer-vision"></a>快速入门：在计算机视觉中使用 REST API 和 JavaScript 生成缩略图

在本快速入门中，你将使用计算机视觉的 REST API 基于图像生成缩略图。 使用[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法，可以生成图像的缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `subscriptionKey` 的值替换为你的订阅密钥。
    1. 如有必要，请将 `uriBase` 的值替换为获取的订阅密钥所在的 Azure 区域中的[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法的终结点 URL。
    1. （可选）将 `inputImage` 控件的 `value` 属性的值替换为要分析的其他图像的 URL。
1. 将代码保存为以 `.html` 为扩展名的文件。 例如，`get-thumbnail.html`。
1. 打开一个浏览器窗口。
1. 在浏览器中，将文件拖放到浏览器窗口。
1. 在浏览器中显示网页时，选择“生成缩略图”按钮。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
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
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>检查响应

成功的响应将以二进制数据形式返回，这些数据表示缩略图的图像数据。 如果请求成功，会根据响应中的二进制数据生成缩略图，并在浏览器窗口中显示该缩略图。 如果请求失败，则响应将显示在控制台窗口中。 失败请求的响应包含错误代码和消息，用以帮助确定出错的地方。

## <a name="clean-up-resources"></a>清理资源

不再需要该文件时，请将其删除。

## <a name="next-steps"></a>后续步骤

浏览一款 JavaScript 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [计算机视觉 API JavaScript 教程](../Tutorials/javascript-tutorial.md)
