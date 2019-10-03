---
title: 使用 Azure Maps 显示搜索结果 | Microsoft Docs
description: 如何使用 Azure Maps 执行搜索请求, 然后在 Azure Maps Web SDK 上显示结果。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975964"
---
# <a name="show-search-results-on-the-map"></a>在地图上显示搜索结果

本文介绍如何搜索感兴趣的位置并在地图上显示搜索结果。

可通过两种方法来搜索感兴趣的位置。 一种方法是使用服务模块发出搜索请求。 另一种方法是发出搜索请求, 通过[提取 api](https://fetch.spec.whatwg.org/) [AZURE MAPS 模糊搜索 api](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 下面将讨论这两种方法。

## <a name="make-a-search-request-via-service-module"></a>通过服务模块发出搜索请求

<iframe height='500' scrolling='no' title='在地图上显示搜索结果（服务模块）' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>在地图上显示搜索结果（服务模块）</a>。
</iframe>

在上面的代码中, 第一个代码块构造一个 map 对象, 并设置用于利用访问令牌的身份验证机制。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建`TokenCredential` , 使用访问令牌对要 Azure Maps 的 HTTP 请求进行身份验证。 然后, 它将`TokenCredential`传递`atlas.service.MapsURL.newPipeline()`给并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)实例。 `searchURL` 表示 Azure Maps [搜索](https://docs.microsoft.com/rest/api/maps/search)操作的 URL。

第三个代码块使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建一个数据源对象并向其添加搜索结果。 某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。  随后创建一个符号层并向其中添加数据源，然后将该符号层添加到地图。

第四个代码块使用[服务模块](how-to-use-services-module.md)中的[SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)方法。 它允许您通过 "[获取搜索模糊 REST API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) " 执行自由格式的文本搜索, 以搜索兴趣点。 Get Search 模糊 API 可以处理模糊输入的任意组合。 然后使用 `geojson.getFeatures()` 方法提取响应中的 GeoJSON 特征集合，并将其添加到数据源，这将自动通过符号层在地图上呈现数据。

最后一个代码块使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性调整地图的照相机边界。

搜索请求、数据源、符号层和相机边界在地图的就绪[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内创建并设置, 以确保在地图完全加载后显示结果。


## <a name="make-a-search-request-via-fetch-api"></a>通过提取 API 发出搜索请求

<iframe height='500' scrolling='no' title='在地图上显示搜索结果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>（在地图上显示搜索结果）。
</iframe>

在上面的代码中, 第一个代码块构造一个 map 对象, 并设置用于利用访问令牌的身份验证机制。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建要向其发出搜索请求的 URL。 它还会创建两个数组, 用于存储搜索结果的边界和 pin。

第三个代码块使用[FETCH api](https://fetch.spec.whatwg.org/)发出[AZURE MAPS 模糊搜索 api](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)的请求, 以搜索兴趣点。 模糊搜索 API 可以处理任意组合的模糊输入。 然后, 它将处理并分析搜索响应, 并将结果 pin 添加到 searchPins 数组。

第四个代码块使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建一个数据源对象并向其添加搜索结果。 某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 随后创建一个符号层并向其中添加数据源，然后将该符号层添加到地图。

最后一个代码块使用结果数组创建一个 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 对象，然后使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 调整地图的照相机边界。 然后, 它呈现结果针脚。

在地图的[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中设置搜索请求、数据源、符号层以及照相机边界，确保在地图完全加载后显示结果。

## <a name="next-steps"></a>后续步骤

详细了解**模糊搜索**：

> [!div class="nextstepaction"]
> [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [从坐标获取信息](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](./map-route.md)
