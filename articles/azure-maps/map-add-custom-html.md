---
title: 在 Azure Maps 中添加自定义 html | Microsoft Docs
description: 如何向 Javascript 地图添加自定义 html
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600215"
---
# <a name="add-custom-html-to-the-map"></a>向地图添加自定义 HTML

本文展示了如何向地图添加自定义 HTML，例如图像文件。 

## <a name="understand-the-code"></a>了解代码

<iframe height='466' scrolling='no' title='向地图添加自定义 html - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add custom html to a map - png</a>（向地图添加自定义 html - png）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块基于一个图像创建 HTML 元素。

最后一个代码块使用 map 类的 [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) 函数将图像添加到地图的指定位置。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
有关可向地图添加的更多代码示例，请参阅以下文章： 
* [显示搜索结果](./map-search-location.md)
* [从坐标获取信息](./map-get-information-from-coordinate.md)

