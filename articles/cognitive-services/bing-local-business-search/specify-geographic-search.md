---
title: 使用地理边界筛选必应当地企业搜索 API 的结果 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 本文介绍如何筛选必应当地企业搜索 API 的搜索结果。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 193d0b25276b90f6047943ded83f2000dd4d3436
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775707"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>使用地理边界筛选必应本地业务搜索 API 的结果

借助必应当地企业搜索 API，可以使用 `localCircularView` 或 `localMapView` 查询参数针对要搜索的特定地理区域设置边界。 请确保只在查询中使用一个参数。 

如果搜索词包含显式的地理位置，则必应当地企业 API 将自动使用它来设置搜索结果的边界。 例如，如果搜索词为 `sailing in San Diego`，则 `San Diego` 将用作位置，并忽略查询参数或用户标头中指定的其他任何位置。 

如果在搜索词中未检测到地理位置，并且未使用查询参数指定任何地理位置，则必应当地企业搜索 API 将尝试从请求的 `X-Search-ClientIP` 或 `X-Search-Location` 标头确定位置。 如果未指定上述任一标头，该 API 将从请求的客户端 IP 或移动设备的 GPS 坐标来确定位置。

## <a name="localcircularview"></a>localCircularView

`localCircularView` 参数围绕一组纬度/经度坐标创建一个圆形地理区域，该区域按半径定义。 使用此参数时，必应当地企业搜索 API 的响应仅包含此圆内的位置；而 `localMapView` 参数则与此不同，其中可以包含略微超出搜索区域的位置。

若要指定圆形地理搜索区域，请选择充当圆心的纬度和经度，以及以米为单位的半径。 然后，可将此参数追加到查询字符串，例如：`q=Restaurants&localCircularView=47.6421,-122.13715,5000`。

完整的查询：

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` 参数指定要搜索的矩形地理区域，它使用两组坐标来指定该区域的东南和西北角。 使用此参数时，必应当地企业搜索 API 的响应可以包含指定区域内部以及靠近边界处的位置；而 `localCircularView` 参数则与此不同，其中只包含搜索区域内部的位置。

若要指定矩形搜索区域，请选择充当边界东南和西北角的两组纬度/经度坐标。 请务必先定义东南坐标，如以下示例所示：`localMapView=47.619987,-122.181671,47.6421,-122.13715`。

完整的查询：

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>后续步骤
- [当地企业搜索 Java 快速入门](quickstarts/local-search-java-quickstart.md)
- [当地企业搜索 C# 快速入门](quickstarts/local-quickstart.md)
- [当地企业搜索 Node 快速入门](quickstarts/local-search-node-quickstart.md)
- [当地企业搜索 Python 快速入门](quickstarts/local-search-python-quickstart.md)
