---
title: 快速入门：使用适用于 Python 的 SDK 执行新闻搜索 - 必应新闻搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，可以使用适用于 Python 的必应新闻搜索 SDK 搜索新闻，并处理响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b84b5ee8682007191953bef34579973c7c24ca45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448512"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>快速入门：使用适用于 Python 的必应新闻搜索 SDK 执行新闻搜索

使用本快速入门开始使用适用于 Python 的必应新闻搜索 SDK 搜索新闻。 虽然必应新闻搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) 上找到此示例的源代码。

## <a name="prerequisites"></a>必备条件

* [Python](https://www.python.org/) 2.x 或 3.x

建议使用[虚拟环境](https://docs.python.org/3/tutorial/venv.html)进行 python 开发。 可以使用 [venv 模块](https://pypi.python.org/pypi/virtualenv)安装并初始化虚拟环境。 必须安装适用于 Python 2.7 的 virtualenv。 可以使用以下命令创建虚拟环境：

```console
python -m venv mytestenv
```

可以使用以下命令安装必应新闻搜索 SDK 依赖项：
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你最喜欢使用的 IDE 或编辑器中新建一个 Python 文件，并导入以下库。 为订阅密钥和搜索词创建变量。

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>初始化客户端并发送请求

1. 创建 `CognitiveServicesCredentials` 的实例。 对客户端进行实例化：
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. 向新闻搜索 API 发送搜索查询并存储响应。

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>分析响应

如果找到任何搜索结果，则输出第一个网页结果：

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)
