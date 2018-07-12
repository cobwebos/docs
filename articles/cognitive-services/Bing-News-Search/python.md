---
title: 适用于 Azure 认知服务中的必应新闻搜索 API 的 Python 快速入门 | Microsoft Docs
description: 获取信息和代码示例，以帮助你快速开始使用 Azure 上的 Microsoft 认知服务中的必应新闻搜索 API。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365718"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>将必应新闻搜索 API 与 Python 配合使用快速入门
本演练演示了一个简单的示例：调用必应新闻搜索 API 并后期处理生成的 JSON 对象。 有关详细信息，请参阅[必应新闻搜索文档](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)。  

可以通过单击启动活页夹锁屏提醒，在 [MyBinder](https://mybinder.org) 上将此示例作为 Jupyter 笔记本运行： 

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>先决条件

必须拥有包含“必应搜索 API”的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。

## <a name="running-the-walkthrough"></a>运行演练
首先，将 `subscription_key` 设置为必应 API 服务的 API 密钥。


```python
subscription_key = None
assert subscription_key
```

接下来，验证 `search_url` 终结点是否正确。 在撰写本文时，必应搜索 API 仅使用一个终结点。 如果遇到授权错误，请在 Azure 仪表板中针对必应搜索终结点仔细检查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

设置 `search_term`查找有关 Microsoft 的新闻文章。


```python
search_term = "Microsoft"
```

以下程序块使用 Python 中的 `requests` 库调出必应搜索 API 并将结果作为 JSON 对象返回。 注意，我们通过 `headers` 字典传入 API 密钥，通过 `params` 字典传入搜索词。 若要查看可用于筛选搜索结果的完整选项列表，请参阅 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) 文档。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 对象包含相关新闻文章和丰富的元数据。 例如，以下代码行提取文章的说明。


```python
descriptions = [article["description"] for article in search_results["value"]]
```

然后，这些说明可以呈现为表格，其中搜索关键字用粗体突出显示。


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分页新闻](paging-news.md)
> [使用修饰标记来突出显示文本](hit-highlighting.md)

## <a name="see-also"></a>另请参阅 

 [在网上搜索新闻](search-the-web.md)  
 [试用](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
