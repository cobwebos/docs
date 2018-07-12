---
title: 适用于 Azure 认知服务中的必应视频搜索 API 的 Python 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上的 Microsoft 认知服务中的必应视频搜索 API。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365714"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>将必应视频搜索 API 与 Python 配合使用的快速入门

本演练展示了如何使用 Azure 上的 Microsoft 认知服务中包含的必应视频搜索 API。 有关 API 的技术详细信息，请参阅 [API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)。

可以通过单击启动活页夹锁屏提醒，在 [MyBinder](https://mybinder.org) 上将此示例作为 Jupyter Notebook 运行： 

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>先决条件
必须拥有包含必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 需要使用激活免费试用版时提供的访问密钥，也可以使用 Azure 仪表板中的付费订阅密钥。

## <a name="running-the-walkthrough"></a>运行演练

首先，将 `subscription_key` 设置为必应 API 服务的 API 密钥。


```python
subscription_key = None
assert subscription_key
```

接下来，验证 `search_url` 终结点是否正确。 在撰写本文时，必应搜索 API 仅使用一个终结点。 如果遇到授权错误，请在 Azure 仪表板中针对必应搜索终结点仔细检查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

设置 `search_term`，搜索有关“小猫”的视频


```python
search_term = "kittens"
```

以下程序块使用 Python 中的 `requests` 库调用必应搜索 API 并将结果作为 JSON 对象返回。 注意，我们通过 `headers` 字典传入 API 密钥，通过 `params` 字典传入搜索词。 若要查看可用于筛选搜索结果的完整选项列表，请参阅 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 文档。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 对象包含相关视频和丰富的元数据。 若要查看其中的视频，请使用其 `embedHtml` 属性并将其插入 `IFrame`。


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对视频进行分页](paging-videos.md)
> [对图像进行大小调整和裁剪](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>另请参阅 

 [在网上搜索视频](search-the-web.md) [试试看](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
