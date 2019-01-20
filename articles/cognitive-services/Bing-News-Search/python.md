---
title: 快速入门：使用 Python 执行新闻搜索 - 必应新闻搜索 REST API
titlesuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Python 将请求发送到必应新闻搜索 REST API，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6d5f21bd2ddcc08296551f061f02d792a5a545a7
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260663"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>快速入门：使用 Python 和必应新闻搜索 REST API 执行新闻搜索

使用本快速入门对必应新闻搜索 API 进行你的第一次调用并接收 JSON 响应。 这个简单的 JavaScript 应用程序会向 API 发送一个搜索查询并处理结果。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

可以通过单击启动活页夹锁屏提醒，在 [MyBinder](https://mybinder.org) 上将此代码示例作为 Jupyter Notebook 运行： 

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

该示例的源代码还可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py) 上获得。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你最喜欢使用的 IDE 或编辑器中新建一个 Python 文件，并导入请求模块。 为你的订阅密钥、终结点和搜索词创建变量。 可以在 Azure 仪表板中找到你的终结点。

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>为请求创建参数

1. 将你的订阅密钥添加到一个新字典中，使用 `"Ocp-Apim-Subscription-Key"` 作为键。 为搜索参数执行相同的操作。

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>发送请求并获取响应

1. 使用请求库以及你的订阅密钥和在上一步骤中创建的字典对象调用必应视觉搜索 API。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` 将来自 API 的响应包含为 JSON 对象。 访问响应中包含的对象的说明。
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>显示结果

然后，这些说明可以呈现为表格，其中搜索关键字用粗体突出显示。

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
[创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)