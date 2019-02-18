---
title: 快速入门：使用 Python 执行搜索 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Python 将请求发送到必应 Web 搜索 REST API，并接收 JSON 响应
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e191821c8e2c6e189e8d7f8befcb11009f2ed7d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172359"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>快速入门：使用 Python 调用必应 Web 搜索 API  

使用本快速入门进行你的第一次必应 Web 搜索 API 并接收 JSON 响应。 这个 Python 应用程序会向该 API 发送一个搜索请求并显示响应。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

此示例作为 Jupyter 笔记本在 [MyBinder](https://mybinder.org) 上运行。 单击“启动活页夹”提示标记：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>先决条件

* [Python 2.x 或 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>定义变量

将 `subscription_key` 值替换为来自你的 Azure 帐户的有效订阅密钥。

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

声明必应 Web 搜索 API 终结点。 如果遇到任何授权错误，请在 Azure 仪表板中针对必应搜索终结点仔细检查此值。

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

可以通过替换 `search_term` 的值随意自定义搜索查询。

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>发出请求

以下代码块使用 `requests` 库调用必应 Web 搜索 API 并将结果作为 JSON 对象返回。 API 密钥是在 `headers` 字典中传入的，搜索词和查询参数是在 `params` 字典中传入的。 有关选项和参数的完整列表，请参阅[必应 Web 搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) 文档。

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>格式化并显示响应

`search_results` 对象包括搜索结果以及元数据，例如相关查询和页面。 此代码使用 `IPython.display` 库格式化响应并在浏览器中显示响应。

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub 上的示例代码

如果希望在本地运行此代码，[GitHub 上提供了完整的示例](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应 Web 搜索单页应用教程](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
