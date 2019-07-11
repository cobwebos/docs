---
title: 快速入门：使用墨迹识别器 REST API 和 Node.js 识别数字墨迹
titleSuffix: Azure Cognitive Services
description: 使用墨迹识别器 API 来识别数字墨迹笔划。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 7e158b0ae27780eeecb1ee7948087bf59b1502e1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721270"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>快速入门：使用墨迹识别器 REST API 和 JavaScript 识别数字墨迹

根据本快速入门的说明，开始对数字墨迹笔划使用墨迹识别器 API。 本 JavaScript 应用程序发送包含 JSON 格式墨迹笔划数据的 API 请求，并显示响应。

虽然此应用程序采用 JavaScript 编写且在 Web 浏览器中运行，但 API 是一种 RESTful Web 服务，可与大多数编程语言兼容。

通常会从数字墨迹应用调用此 API。 本快速入门为 JSON 文件中的以下手写示例发送墨迹笔划数据。

![手写文本图像](../media/handwriting-sample.jpg)

可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905) 上找到此快速入门的源代码。

## <a name="prerequisites"></a>先决条件

- Web 浏览器
- 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json) 上找到此快速入门的示例墨迹笔划数据。


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在最喜爱的 IDE 或编辑器中创建一个新的 `.html` 文件。 然后，向该文件添加基本的 HTML（适用于我们稍后要添加的代码）。
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. 在 `<body>` 标记中添加以下 html：
    1. 两个文本区域，用于显示 JSON 请求和响应。
    2. 一个按钮，用于调用稍后将创建的 `recognizeInk()` 函数。
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>加载示例 JSON 数据

1. 在 `<script>` 标记中，为 sampleJson 创建一个变量。 然后创建一个名为 `openFile()` 的 JavaScript 函数，用于打开文件资源管理器，这样你就可以选择 JSON 文件。 单击 `Recognize ink` 按钮时，该按钮会调用此函数并开始读取该文件。
2. 使用 `FileReader` 对象的 `onload()` 函数以异步方式处理文件。 
    1. 将文件中的任何 `\n` 或 `\r` 字符替换为空字符串。 
    2. 使用 `JSON.parse()` 将文本转换为有效的 JSON
    3. 更新应用程序中的 `request` 文本框。 使用 `JSON.stringify()` 设置 JSON 字符串格式。 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>将请求发送到墨迹识别器 API

1. 在 `<script>` 标记中，创建名为 `recognizeInk()` 的函数。 该函数稍后会调用 API 并使用响应来更新页面。 将以下步骤中的代码添加到此函数内。 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. 为终结点 URL、订阅密钥和示例 JSON 创建变量。 然后创建 `XMLHttpRequest` 对象，用于发送 API 请求。 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. 为 `XMLHttpRequest` 对象创建返回函数。 此函数会分析来自成功请求的 API 响应，并将其显示在应用程序中。 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 为请求对象创建错误函数。 此函数将错误记录到控制台。 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 为请求对象的 `onreadystatechange` 属性创建函数。 当请求对象的就绪状态更改时，将会应用上述返回函数和错误函数。
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. 发送 API 请求。 向 `Ocp-Apim-Subscription-Key` 标头添加订阅密钥，并将 `content-type` 设置为 `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>运行应用程序并查看响应

此应用程序可在 Web 浏览器中运行。 成功的响应以 JSON 格式返回。 也可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json) 上找到 JSON 响应：

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://go.microsoft.com/fwlink/?linkid=2089907)

若要了解墨迹识别 API 在数字墨迹应用中的工作原理，请查看 GitHub 上的以下示例应用程序：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript Web 浏览器应用](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 移动应用](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 移动应用](https://go.microsoft.com/fwlink/?linkid=2089805)
