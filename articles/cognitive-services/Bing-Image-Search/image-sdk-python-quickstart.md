---
title: 图像搜索 SDK Python 快速入门 | Microsoft Docs
description: 设置图像搜索 SDK 控制台应用程序。
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366683"
---
# <a name="image-search-sdk-python-quickstart"></a>图像搜索 SDK Python 快速入门

必应图像搜索 SDK 包含用于 Web 查询以及对结果进行分析的 REST API 功能。 

Git Hub 上提供了 [Python 必应图像搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py)。

## <a name="application-dependencies"></a>应用程序依赖项
安装 Python（若尚未安装）。 SDK 与 Python 2.7、3.3、3.4、3.5 和 3.6 兼容。

常规 Python 开发建议是使用[虚拟环境](https://docs.python.org/3/tutorial/venv.html)。 使用 [venv 模块](https://pypi.python.org/pypi/virtualenv)安装并初始化虚拟环境。 必须安装适用于 Python 2.7 的 virtualenv。
```
python -m venv mytestenv
```
安装必应图像搜索 SDK 依赖项：
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>图像搜索客户端
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 添加以下导入：
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
创建 `CognitiveServicesCredentials` 的一个实例并实例化客户端：
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
基于查询 (Yosemite) 搜索图像，针对动画 gif 和宽屏进行筛选。 验证结果数并输出第一个结果的 insightsToken、缩略图 URL 和 web URL。
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
搜索关于 (Yosemite) 的图像，针对动画 gif 和宽屏进行筛选。  验证结果数。  输出第一个结果的 `insightsToken`、`thumbnail url` 和 `web url`。
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

获取热门结果：
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>后续步骤

[认知服务 Python SDK 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)。


