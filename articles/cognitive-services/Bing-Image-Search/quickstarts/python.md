---
title: 调用和响应 - 适用于 Azure 认知服务中的必应图像搜索 API 的 Python 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上 Microsoft 认知服务中的必应图像搜索 API。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365716"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>调用和响应：Python 中的第一个必应图像搜索查询

必应图像搜索 API 通过允许向必应发送用户搜索查询并获取相关图像的列表，提供与 Bing.com/图像相似的体验。

本演练演示了一个简单示例：调用必应图像搜索 API 并对生成的 JSON 对象进行后续处理。 有关详细信息，请参阅[必应图像搜索文档](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)。

可以通过单击启动活页夹锁屏提醒，在 [MyBinder](https://mybinder.org) 上将此示例作为 Jupyter Notebook 运行： 

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>先决条件

必须拥有包含必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 需要使用激活免费试用版时提供的访问密钥，也可以使用 Azure 仪表板中的付费订阅密钥。

## <a name="running-the-walkthrough"></a>运行演练
若要继续本演练，请将 `subscription_key` 设置为必应 API 服务的 API 密钥。


```python
subscription_key = None
assert subscription_key
```

接下来，验证 `search_url` 终结点是否正确。 在撰写本文时，必应搜索 API 仅使用一个终结点。 如果遇到授权错误，请在 Azure 仪表板中针对必应搜索终结点仔细检查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

设置 `search_term`，搜索有关“小狗”的图像。


```python
search_term = "puppies"
```

以下程序块使用 Python 中的 `requests` 库调用必应搜索 API 并将结果作为 JSON 对象返回。 注意，我们通过 `headers` 字典传入 API 密钥，通过 `params` 字典传入搜索词。 若要查看可用于筛选搜索结果的完整选项列表，请参阅 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 文档。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 对象包含实际图像以及丰富的元数据，例如相关项目。 例如，以下代码行可以提取前 16 个结果的缩略图 URL。


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

然后，我们可以使用 `PIL` 库下载缩略图图像和 `matplotlib` 库，将其呈现在 $4 乘 4$ 网格上。


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

[必应图像搜索概述](../overview.md)  
[试试看](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[获取免费试用版访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
