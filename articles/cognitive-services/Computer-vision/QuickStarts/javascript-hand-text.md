---
title: 快速入门：计算机视觉 2.0 和 2.1 - 提取印刷体文本和手写文本 - REST、JavaScript
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 JavaScript 从图像中提取印刷体文本和手写文本。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 907b3ead8e39268bc73604599bd2c37b18ddfa21
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83676104"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-javascript"></a>快速入门：使用计算机视觉 REST API 和 JavaScript 提取印刷体文本和手写文本

本快速入门将使用计算机视觉 REST API 从图像中提取印刷体文本和/或手写文本。 使用[批量读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)和[读取操作结果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d)方法，可以检测图像中的文本，并将识别的字符提取到计算机可读的字符流中。 该 API 将确定用于每行文本的识别模型，因此它支持同时包含印刷体文本和手写文本的图像。

此功能在 v2.1 API 和 v3.0 公共预览版 API 中都可用。 与 v2.1 相比，3.0 API 有以下不同：

* 准确度改进
* 提供了字词置信度分数
* 使用附加的 `language` 参数同时支持西班牙语和英语
* 具有不同的输出格式

针对所使用的版本，选择下面的选项卡。

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

> [!IMPORTANT]
> [批量读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)方法以异步方式运行。 此方法不返回成功响应正文中的任何信息。 相反，批量读取方法返回 `Operation-Location` 响应标头字段值中的 URI。 然后就可以调用此 URI，它表示[读取操作结果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API，同时检查状态并返回批量读取方法调用的结果。

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)

> [!IMPORTANT]
> [批量读取](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005)方法以异步方式运行。 此方法不返回成功响应正文中的任何信息。 相反，批量读取方法返回 `Operation-Location` 响应标头字段值中的 URI。 然后就可以调用此 URI，它表示[读取操作结果](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) API，同时检查状态并返回批量读取方法调用的结果。

---

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

必须具有计算机视觉的订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)获取免费的试用密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。 将订阅密钥和终结点 URL 保存到临时位置。

## <a name="create-and-run-the-sample"></a>创建并运行示例

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

要创建和运行示例，请执行以下步骤：

1. 创建一个名为 _get-text.html_ 的文件，在文本编辑器中打开它，然后将以下代码复制到其中。
1. （可选）将 `inputImage` 控件的 `value` 属性的值替换为要从中提取文本的另一图像的 URL。
1. 打开一个浏览器窗口。
1. 在浏览器中，将文件拖放到浏览器窗口。
1. 当网页显示在浏览器中时，请将订阅密钥和终结点 URL 粘贴到相应的输入框中。
1.  选择“读取图像”按钮。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // Fetch your Computer Vision key and endpoint for this sample.
        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
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
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
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

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. （可选）将 `inputImage` 控件的 `value` 属性的值替换为要从中提取文本的另一图像的 URL。
1. 将代码保存为以 `.html` 为扩展名的文件。 例如，`get-text.html` 。
1. 打开一个浏览器窗口。
1. 当网页显示在浏览器中时，请填写所需的参数，并选择“读取图像”按钮  。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        let language = document.getElementById("language").value;
        
        var uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        const params = {
            language: language,
        };

        const searchParams = new URLSearchParams(params);

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase + "?" + searchParams.toString(),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
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
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>
Language: 
<input type="text" name="language" id="language"
    value="en" />
<div style="margin: 20px;">Accepted values are &quot;en&quot; and &quot;es&quot;</div>

<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
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

---

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例网页会在浏览器窗口中分析和显示成功响应，如下例所示：

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T20:56:33Z",
  "lastUpdatedDateTime": "2020-02-11T20:56:34Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8206,
        "width": 800,
        "height": 154,
        "unit": "pixel",
        "lines": [
          {
            "language": "en",
            "boundingBox": [
              6,
              4,
              774,
              14,
              773,
              61,
              5,
              49
            ],
            "text": "The quick brown fox jumps over the lazy",
            "words": [
              {
                "boundingBox": [
                  14,
                  5,
                  76,
                  6,
                  74,
                  49,
                  12,
                  48
                ],
                "text": "The",
                "confidence": 0.83
              },
              {
                "boundingBox": [
                  84,
                  6,
                  182,
                  7,
                  180,
                  51,
                  82,
                  49
                ],
                "text": "quick",
                "confidence": 0.762
              },
              {
                "boundingBox": [
                  191,
                  7,
                  312,
                  9,
                  309,
                  54,
                  189,
                  51
                ],
                "text": "brown",
                "confidence": 0.67
              },
              {
                "boundingBox": [
                  320,
                  9,
                  382,
                  10,
                  379,
                  55,
                  317,
                  54
                ],
                "text": "fox",
                "confidence": 0.849
              },
              {
                "boundingBox": [
                  390,
                  10,
                  497,
                  11,
                  493,
                  57,
                  387,
                  55
                ],
                "text": "jumps",
                "confidence": 0.703
              },
              {
                "boundingBox": [
                  506,
                  11,
                  596,
                  12,
                  591,
                  59,
                  502,
                  57
                ],
                "text": "over",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  604,
                  12,
                  666,
                  13,
                  661,
                  60,
                  600,
                  59
                ],
                "text": "the",
                "confidence": 0.923
              },
              {
                "boundingBox": [
                  674,
                  13,
                  773,
                  14,
                  768,
                  62,
                  670,
                  60
                ],
                "text": "lazy",
                "confidence": 0.863
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              5,
              53,
              79,
              56,
              77,
              95,
              4,
              92
            ],
            "text": "dog",
            "words": [
              {
                "boundingBox": [
                  6,
                  53,
                  74,
                  56,
                  72,
                  95,
                  5,
                  92
                ],
                "text": "dog",
                "confidence": 0.418
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              0,
              90,
              787,
              95,
              787,
              145,
              0,
              136
            ],
            "text": "Pack my box with five dozen liquor jugs",
            "words": [
              {
                "boundingBox": [
                  1,
                  96,
                  79,
                  93,
                  79,
                  135,
                  0,
                  136
                ],
                "text": "Pack",
                "confidence": 0.835
              },
              {
                "boundingBox": [
                  87,
                  93,
                  151,
                  92,
                  151,
                  135,
                  87,
                  135
                ],
                "text": "my",
                "confidence": 0.88
              },
              {
                "boundingBox": [
                  162,
                  92,
                  226,
                  91,
                  225,
                  135,
                  161,
                  135
                ],
                "text": "box",
                "confidence": 0.301
              },
              {
                "boundingBox": [
                  234,
                  91,
                  335,
                  90,
                  335,
                  135,
                  233,
                  135
                ],
                "text": "with",
                "confidence": 0.959
              },
              {
                "boundingBox": [
                  346,
                  91,
                  418,
                  91,
                  417,
                  136,
                  345,
                  135
                ],
                "text": "five",
                "confidence": 0.489
              },
              {
                "boundingBox": [
                  426,
                  91,
                  527,
                  93,
                  527,
                  138,
                  425,
                  136
                ],
                "text": "dozen",
                "confidence": 0.727
              },
              {
                "boundingBox": [
                  554,
                  94,
                  687,
                  98,
                  687,
                  143,
                  553,
                  139
                ],
                "text": "liquor",
                "confidence": 0.377
              },
              {
                "boundingBox": [
                  701,
                  99,
                  787,
                  103,
                  787,
                  146,
                  700,
                  143
                ],
                "text": "jugs",
                "confidence": 0.693
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="next-steps"></a>后续步骤

了解一款使用计算机视觉执行光学字符识别 (OCR) 功能的 JavaScript 应用程序；创建智能裁剪缩略图；对图像中的视觉特征进行检测、分类、标记和说明。 

> [!div class="nextstepaction"]
> [计算机视觉 API JavaScript 教程](../Tutorials/javascript-tutorial.md)

* 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。
