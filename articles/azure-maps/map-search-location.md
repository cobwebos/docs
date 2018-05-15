---
title: 使用 Azure Maps 显示搜索结果 | Microsoft Docs
description: 如何使用 Azure Maps 执行搜索请求并在 Javascript 地图上显示结果
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>在地图上显示搜索结果

本文展示了如何发出搜索请求并在地图上显示搜索结果。 

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='在地图上显示搜索结果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>（在地图上显示搜索结果）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一层搜索图钉并将其添加到地图。 有关说明，可以参阅[在地图上添加图钉](./map-add-pin.md)。

第三个代码块将一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)。

最后一个代码块分析传入的响应。 对于成功的响应，它收集返回的每个位置的纬度和经度信息。 它将所有位置点作为图钉添加到地图上并调整地图边界以呈现所有图钉。


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 

* [Azure Maps 模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
