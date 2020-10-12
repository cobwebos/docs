---
title: 快速入门：使用 REST API 和 Python 检查拼写 - 必应拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 和 Python 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: cb07b105cedc9b017eb99212f3225c4f9d43511e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91330666"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>快速入门：使用必应拼写检查 REST API 和 Python 检查拼写

根据此快速入门中的说明对必应拼写检查 REST API 进行第一次调用。 此简单的 Python 应用程序将向 API 发送请求并返回一系列建议的更正。 

虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 上提供了此应用程序的源代码

## <a name="prerequisites"></a>先决条件

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句：

   ```python
   import requests
   import json
   ```

2. 为要进行拼写检查的文本、订阅密钥和必应拼写检查终结点创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>为请求创建参数

1. 新建以 `text` 为密钥、以文本为值的字典。

    ```python
    data = {'text': example_text}
    ```

2. 为请求添加参数： 

   1. 用 `=` 运算符将市场代码分配到 `mkt` 参数。 市场代码指示发出请求的国家/地区的代码。 

   1. 使用 `&` 运算符添加 `mode` 参数，然后分配拼写检查模式。 模式可以是 `proof`（捕获大部分拼写/语法错误）或 `spell`（捕获大部分拼写错误，但是捕获的语法错误较少）。 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. 添加 `Content-Type` 标头，并向 `Ocp-Apim-Subscription-Key` 标头添加订阅密钥。

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>发送请求并读取响应

1. 使用请求库发送 POST 请求。

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. 获取 JSON 响应，并将其打印。

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>运行应用程序

如果使用命令行，则使用以下命令运行应用程序：

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示：

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorials/spellcheck.md)

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
