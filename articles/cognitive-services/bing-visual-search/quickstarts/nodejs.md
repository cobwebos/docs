---
title: 快速入门：使用必应视觉搜索 REST API 和 Node.js 获取图像见解
titleSuffix: Azure Cognitive Services
description: 了解如何将图像上传到必应视觉搜索 API 并获取其相关见解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 9414bac220d928618b403aa2f7df7748772e0e9a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047562"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>快速入门：使用必应视觉搜索 REST API 和 Node.js 获取图像见解

使用本快速入门首次调用必应视觉搜索 API 并查看搜索结果。 此简单 JavaScript 应用程序会将一个图像上传到该 API，并显示返回的相关信息。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

上传本地图像时，表单数据必须包含 `Content-Disposition` 标头。 必须将其 `name` 参数设置为“image”，并且可以将 `filename` 参数设置为任何字符串。 表单内容包括图像的二进制数据。 可以上传的最大图像大小为 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/download/)
* JavaScript 请求模块。 可以使用 `npm install request` 命令安装该模块。
* 表单数据模块。 可以使用 `npm install form-data` 命令安装该模块。 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建 JavaScript 文件，并设置以下要求：

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. 为 API 终结点、订阅密钥和图像路径创建变量：

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. 创建一个名为 `requestCallback()` 的函数来输出 API 的响应：

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>构造并发送搜索请求

1. 使用 `FormData()` 创建新的 **FormData** 对象，并使用 `fs.createReadStream()` 将图像路径追加​​到该对象后面：
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. 使用请求库上传图像，并调用 `requestCallback()` 输出响应。 请务必将订阅密钥添加到请求标头中：

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建视觉搜索单页 Web 应用](../tutorial-bing-visual-search-single-page-app.md)
