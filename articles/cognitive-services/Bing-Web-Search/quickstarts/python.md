---
title: 调用和响应 - 适用于 Azure 认知服务中的必应 Web 搜索 API 的 Python 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助你快速开始使用 Azure 上 Microsoft 认知服务中的必应 Web 搜索 API。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365746"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>调用和响应：Python 中的第一个必应 Web 搜索查询

必应 Web 搜索 API 通过返回必应确定与用户查询相关的搜索结果，提供与 Bing.com/搜索相似的体验。 结果可能包括网页、图像、视频、新闻和实体，以及相关的搜索查询、拼写更正、时区、单位换算、翻译和计算。 所获得的结果类型取决于它们的相关性以及订阅的必应搜索 API 的层级。

有关 API 的技术详细信息，请参阅 [API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)。

可以通过单击启动活页夹锁屏提醒，在 [MyBinder](https://mybinder.org) 上将此示例作为 Jupyter 笔记本运行： 

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>先决条件
必须拥有包含**必应搜索 API** 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。

## <a name="running-the-walkthrough"></a>运行演练

将 `subscription_key` 设置为必应 API 服务的 API 密钥。


```python
subscription_key = None
assert subscription_key
```

接下来，验证 `search_url` 终结点是否正确。 在撰写本文时，必应搜索 API 仅使用一个终结点。 如果遇到授权错误，请在 Azure 仪表板中针对必应搜索终结点仔细检查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

设置 `search_term` 以在必应中查询 Microsoft 认知服务。


```python
search_term = "Microsoft Cognitive Services"
```

以下程序块使用 Python 中的 `requests` 库调用必应搜索 API 并将结果作为 JSON 对象返回。 注意，我们通过 `headers` 字典传入 API 密钥，通过 `params` 字典传入搜索词。 若要查看可用于筛选搜索结果的完整选项列表，请参阅 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) 文档。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 对象包含搜索结果以及丰富的元数据，例如相关查询和页面。 以下代码行会对查询返回的最匹配页面进行格式化。


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应 Web 搜索单页应用教程](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

[必应 Web 搜索概述](../overview.md)  
[试用](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[获取免费试用版访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[必应 Web 搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
