---
title: 快速入门：使用必应视觉搜索 REST API 和 Node.js 获取图像见解
titleSuffix: Azure Cognitive Services
description: 了解如何将图像上传到必应视觉搜索 API 并获取其相关见解。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 5fca4e960b449988a0e77b2ecc2d0a9c8ca1988f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741465"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>快速入门：使用必应视觉搜索 REST API 和 Node.js 获取图像见解

使用本快速入门首次调用必应视觉搜索 API 并查看搜索结果。 此简单 JavaScript 应用程序会将一个图像上传到该 API，并显示返回的相关信息。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

上传本地图像时，表单数据必须包含 Content-Disposition 标头。 它的 `name` 参数必须设置为“image”，`filename` 参数可以设置为任何字符串。 窗体内容是图像的二进制文件。 可以上传的最大图像大小为 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/download/)
* JavaScript 请求模块
    * 可以使用 `npm install request` 安装此模块
* 表单数据模块
    * 可以使用 `npm install form-data` 安装此模块


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 JavaScript 文件，然后设置以下要求：

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. 为 API 终结点、订阅密钥和图像路径创建变量。

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. 创建一个名为 `requestCallback()` 的函数来输出 API 的响应。

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>构造并发送搜索请求

1. 使用 `FormData()` 创建新的表单数据，并使用 `fs.createReadStream()` 将图像路径附加至窗体。
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. 使用请求库上传图像，调用 `requestCallback()` 输出响应。 请务必将订阅密钥添加到请求标头中。 

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
> [生成自定义搜索 Web 应用](../tutorial-bing-visual-search-single-page-app.md)