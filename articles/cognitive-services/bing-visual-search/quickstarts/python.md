---
title: 快速入门：使用 REST API 和 Python 获取图像见解 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 了解如何将图像上传到必应视觉搜索 API 并获取其相关见解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: 7c76ec8600ade26098206a5d18559e795db53178
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853069"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>快速入门：使用必应视觉搜索 REST API 和 Python 获取图像见解

根据此快速入门中的说明对必应视觉搜索 API 进行第一次调用。 此 Python 应用程序会将一个图像上传到该 API，并显示它返回的信息。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建一个新的 Python 文件，并添加以下 `import` 语句：

    ```python
    import requests, json
    ```

2. 为订阅密钥、终结点以及要上传的图像的路径创建变量。 对于 `BASE_URI` 值，可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. 上传本地图像时，表单数据必须包含 `Content-Disposition` 标头。 将其 `name` 参数设置为“image”，并且将 `filename` 参数设置为映像的文件名。 表单内容包括图像的二进制数据。 可以上传的最大图像大小为 1 MB。
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. 创建一个字典对象用于保存请求的标头信息。 将订阅密钥绑定到字符串 `Ocp-Apim-Subscription-Key`。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. 创建另一个字典用于包含图像，发送请求时将打开并上传该图像。

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>分析 JSON 响应

创建名为 `print_json()` 的方法，用于接受 API 响应并输出 JSON。

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>发送请求

使用 `requests.post()` 向必应视觉搜索 API 发送请求。 请包含终结点、标头和文件信息的字符串。 使用 `print_json()` 输出 `response.json()`。

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
> [创建视觉搜索单页 Web 应用](../tutorial-bing-visual-search-single-page-app.md)
