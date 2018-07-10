---
title: 新闻搜索 SDK Python 快速入门 | Microsoft Docs
description: 设置新闻搜索 SDK 控制台应用程序。
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366691"
---
# <a name="news-search-sdk-python-quickstart"></a>新闻搜索 SDK Python 快速入门

新闻搜索 SDK 包含用于 Web 查询以及对结果进行分析的 REST API 功能。 

Git Hub 上提供了 [Python 必应新闻搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)。

## <a name="application-dependencies"></a>应用程序依赖项
安装 Python（若尚未安装）。 SDK 与 Python 2.7、3.3、3.4、3.5 和 3.6 兼容。

常规 Python 开发建议是使用[虚拟环境](https://docs.python.org/3/tutorial/venv.html)。 使用 [venv 模块](https://pypi.python.org/pypi/virtualenv)安装并初始化虚拟环境。 必须安装适用于 Python 2.7 的 virtualenv。
```
python -m venv mytestenv
```
安装必应新闻搜索 SDK 依赖项：
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>新闻搜索客户端
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 添加以下导入：
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
创建 `CognitiveServicesCredentials` 的实例。 对客户端进行实例化：
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
搜索结果，并输出第一个网页结果：
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
使用筛选器以及 `freshness` 和 `sortBy` 参数搜索关于“Artificial Intelligence”的最新新闻。 验证结果数，并输出第一个新闻项结果的 `totalEstimatedMatches`、`name`、`url`、`description`、`published time` 和 `name of provider`。
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
使用安全搜索搜索关于电影和电视娱乐的分类新闻。 验证结果数，并输出第一个新闻项结果的 `category`、`name`、`url`、`description`、`published time` 和 `name of provider`。
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
在必应中搜索新闻热门主题。  验证结果数，并输出第一个新闻结果的 `name`、`text of query`、`webSearchUrl`、`newsSearchUrl` 和 `image Url`。
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>后续步骤

[认知服务 Python SDK 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)。


