---
title: 使用 Azure Maps 显示搜索结果 | Microsoft Docs
description: 如何使用 Azure Maps 执行搜索请求并在 Javascript 地图上显示结果
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 031085b3048d0ffc92dd5a35b4054903088b4858
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824330"
---
# <a name="show-search-results-on-the-map"></a>在地图上显示搜索结果

本文介绍如何搜索感兴趣的位置并在地图上显示搜索结果。

可通过两种方法来搜索感兴趣的位置。 一种方法是使用服务模块发出搜索请求。 另一种方法是通过 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 向 [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 发出搜索请求。 下面将讨论这两种方法。

## <a name="make-a-search-request-via-service-module"></a>通过服务模块发出搜索请求

<iframe height='500' scrolling='no' title='在地图上显示搜索结果（服务模块）' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>在地图上显示搜索结果（服务模块）</a>。
</iframe>

在上面的代码中，第一个代码块将构造一个地图对象并初始化客户端服务。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块使用[服务模块](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1)中的 [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) 方法。 它允许通过[模糊搜索 rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 执行自由格式文本搜索，以搜索兴趣点。 模糊搜索 API 可以处理任何模糊输入组合。 然后使用 [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) 方法将模糊搜索服务的响应解析为 GeoJSON 格式。 

第三个代码块使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建一个数据源对象并向其添加搜索结果。 某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。  随后创建一个符号层并向其中添加数据源，然后将该符号层添加到地图。

最后一个代码块使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性调整地图的照相机边界。

在地图的[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建并设置搜索请求、数据源、符号层以及照相机边界，确保在地图完全加载后显示结果。


## <a name="make-a-search-request-via-xmlhttprequest"></a>通过 XMLHttpRequest 发出搜索请求

<iframe height='500' scrolling='no' title='在地图上显示搜索结果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>（在地图上显示搜索结果）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块将 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 以搜索兴趣点。 模糊搜索 API 可以处理任意组合的模糊输入。 

第三个代码块解析搜索响应并将结果存储在数组中以计算边界。 然后返回搜索结果。

第四个代码块使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建一个数据源对象并向其添加搜索结果。 某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 随后创建一个符号层并向其中添加数据源，然后将该符号层添加到地图。

最后一个代码块使用结果数组创建一个 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 对象，然后使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 调整地图的照相机边界。 然后呈现结果针。

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
