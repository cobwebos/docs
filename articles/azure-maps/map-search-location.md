---
title: 使用 Azure Maps 显示搜索结果 | Microsoft Docs
description: 如何使用 Azure Maps 执行搜索请求并在 Javascript 地图上显示结果
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7d4eb5f9be4a6bcefe4b544d3f97a9b9391c0d81
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665771"
---
# <a name="show-search-results-on-the-map"></a>在地图上显示搜索结果

本文介绍如何搜索感兴趣的位置并在地图上显示搜索结果。 

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='在地图上显示搜索结果（服务模块）' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>在地图上显示搜索结果（服务模块）</a>。
</iframe>

在上面的代码中，第一个代码块构造地图对象并实例化客户端服务。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块使用模糊搜索 [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 来搜索兴趣点。 模糊搜索 API 可以处理任何模糊输入组合。 然后使用 [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) 方法将模糊搜索服务的响应解析为 GeoJSON 格式。 然后将图钉添加到地图以在地图上显示兴趣点。

最后一个代码块使用地图的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 属性为地图添加照相机边界。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 

* [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
有关可向地图添加的更多代码示例，请参阅以下文章： 
* [从坐标获取信息](./map-get-information-from-coordinate.md)
* [显示从 A 到 B 的路线](./map-route.md)
