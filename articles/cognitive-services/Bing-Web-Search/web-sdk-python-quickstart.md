---
title: 快速入门：使用用于 Python 的必应 Web 搜索 SDK
titleSuffix: Azure Cognitive Services
description: 可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 Python 应用程序中。 本快速入门介绍了如何发送请求、接收 JSON 响应以及筛选和分析结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 64ef052478bed627c896cd0e0b5301961a8b6f21
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562861"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>快速入门：使用用于 Python 的必应 Web 搜索 SDK

可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 Python 应用程序中。 本快速入门介绍了如何发送请求、接收 JSON 响应以及筛选和分析结果。

想要马上查看代码？ GitHub 上提供了[适用于 Python 示例的必应 Web 搜索 SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)。

## <a name="prerequisites"></a>必备条件
必应 Web 搜索 SDK 与 Python 2.7、3.3、3.4、3.5 和 3.6 兼容。 建议在本快速入门中使用虚拟环境。

* Python 2.7、3.3、3.4、3.5 或 3.6
* 适用于 Python 2.7 的 [virtualenv](https://docs.python.org/3/tutorial/venv.html)
* 适用于 Python 3.x 的 [venv](https://pypi.python.org/pypi/virtualenv)

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>创建并配置虚拟环境

Python 2.x 和 Python 3.x 的设置和配置虚拟环境的说明是不同的。 请按以下步骤创建和初始化虚拟环境。

### <a name="python-2x"></a>Python 2.x

使用 `virtualenv` 为 Python 2.7 创建虚拟环境：

```console
virtualenv mytestenv
```

激活环境：

```console
cd mytestenv
source bin/activate
```

安装必应 Web 搜索 SDK 依赖项：

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

使用 `venv` 为 Python 3.x 创建虚拟环境：

```console
python -m venv mytestenv
```

激活环境：

```console
mytestenv\Scripts\activate.bat
```

安装必应 Web 搜索 SDK 依赖项：

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>创建客户端并输出初始结果

设置虚拟环境并安装依赖项之后，就可以创建客户端了。 客户端会处理针对必应 Web 搜索 API 的请求以及来自该 API 的响应。

如果响应包含网页、图像、新闻或视频，则会输出各自的初始结果。

1. 使用最喜欢的 IDE 或编辑器创建新的 Python 项目。

1. 将此示例代码复制到项目中。 `endpoint` 可以是下面的全局终结点，也可以是资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key), base_url = "YOUR_ENDPOINT")

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. 将 `SUBSCRIPTION_KEY` 替换为有效订阅密钥。

1. 将 `YOUR_ENDPOINT` 替换为门户中你的终结点 URL。

1. 运行该程序。 例如：`python your_program.py`。

## <a name="define-functions-and-filter-results"></a>定义函数并筛选结果

现在你已经第一次调用了必应 Web 搜索 API，让我们看看几个函数。 以下部分重点介绍用于优化查询和筛选结果的 SDK 功能。 每个函数都可以添加到在上一部分创建的 Python 程序中。

### <a name="limit-the-number-of-results-returned-by-bing"></a>限制必应返回的结果数

此示例使用 `count` 和 `offset` 参数限制通过 SDK 的 [`search` 方法](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)返回的结果数。 将会输出初始结果的 `name` 和 `url`。

1. 将以下代码添加到 Python 项目：

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. 运行该程序。

### <a name="filter-for-news-and-freshness"></a>新闻和新鲜度的筛选器

此示例使用 `response_filter` 和 `freshness` 参数筛选通过 SDK 的 [`search` 方法](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)获得的搜索结果。 返回的搜索结果仅限必应发现的过去 24 小时的新闻文章和网页。 将会输出初始结果的 `name` 和 `url`。

1. 将以下代码添加到 Python 项目：

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. 运行该程序。

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>使用安全搜索、答案计数和提升筛选器

此示例使用 `answer_count`、`promote` 和 `safe_search` 参数筛选通过 SDK 的 [`search` 方法](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)获得的搜索结果。 将会显示初始结果的 `name` 和 `url`。

1. 将以下代码添加到 Python 项目：

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. 运行该程序。

## <a name="clean-up-resources"></a>清理资源

完成本项目以后，请务必从程序代码中删除订阅密钥并停用虚拟环境。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务 Python SDK 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)。

## <a name="see-also"></a>另请参阅

* [Azure Python SDK 参考](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
