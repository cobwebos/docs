---
title: 快速入门：使用必应视觉搜索 REST API 和 Python 获取图像见解
titleSuffix: Azure Cognitive Services
description: 了解如何将图像上传到必应视觉搜索 API 并获取其相关见解。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 37fef6100d78b46d0fb52e486f1788eb78ea2578
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193754"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>快速入门：使用 Python 编写的第一个必应视觉搜索查询

使用本快速入门首次调用必应视觉搜索 API 并查看搜索结果。 此简单 JavaScript 应用程序会将一个图像上传到该 API，并显示返回的相关信息。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

上传本地图像时，POST 表单数据必须包含 Content-Disposition 标头。 它的 `name` 参数必须设置为“image”，`filename` 参数可以设置为任何字符串。 窗体内容是图像的二进制文件。 可以上传的最大图像大小为 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>先决条件

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化应用程序

1. 在偏好的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句。

    ```python
    import requests, json
    ```

2. 为订阅密钥、终结点以及要上传的图像的路径创建变量。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. 创建一个字典对象用于保存请求的标头信息。 将订阅密钥绑定到字符串 `Ocp-Apim-Subscription-Key`，如下所示。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. 创建另一个字典用于包含图像，发送请求时将打开并上传该图像。 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>分析 JSON 响应

1. 创建名为 `print_json()` 的方法，用于接收 API 响应并列显 JSON。

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>发送请求

1. 使用 `requests.post()` 向必应视觉搜索 API 发送请求。 请包含终结点、标头和文件信息的字符串。 使用 `print_json()` 列显 `response.json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](../tutorial-bing-visual-search-single-page-app.md)
