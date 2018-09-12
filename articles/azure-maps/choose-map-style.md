---
title: Azure Maps 中的地图样式功能 | Microsoft Docs
description: 了解 Azure Maps 样式的相关功能。
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666865"
---
# <a name="choose-a-map-style-in-azure-maps"></a>在 Azure Maps 中选择地图样式
Azure Maps 有四种不同的地图样式可供选择。 有关地图样式的详细信息，请参阅 [Azure Maps 中支持的地图样式](./supported-map-styles.md)。 本文介绍如何使用样式的相关功能设置地图加载的样式、设置新的样式和使用样式选取器控件。

## <a name="setting-style-on-map-load"></a>设置地图加载的样式

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

上面的代码创建一个样式设置为灰度的地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

## <a name="updating-the-style"></a>更新样式

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

上述代码中的第一个代码块可在无需预先设置样式的情况下创建地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用地图的 [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) 方法将地图样式设置为卫星。

## <a name="adding-the-style-picker"></a>添加样式选取器

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

上述代码中的第一个代码块可在无需预先设置样式的情况下创建地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用 atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) 构造函数来构造样式选取器。

样式选取器支持为地图选择样式。 第三个代码块使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法向地图添加样式选取器。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
有关可向地图添加的更多代码示例，请参阅以下文章：
* [添加地图控件](./map-add-controls.md)
* [添加图钉](./map-add-pin.md)
