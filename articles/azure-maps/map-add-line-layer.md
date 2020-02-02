---
title: 向地图添加线条层 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure map Web SDK 向地图添加线条层。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933469"
---
# <a name="add-a-line-layer-to-the-map"></a>向地图添加线条层

线条层可用于将 `LineString` 和 `MultiLineString` 功能呈现为地图上的路径或路由。 线条层还可用于呈现 `Polygon` 和 `MultiPolygon` 功能的轮廓。 数据源连接到线条层以向其提供要呈现的数据。 

> [!TIP]
> 默认情况下，线条层将呈现数据源中的多边形和线条的坐标。 若要将该层限制为仅呈现 LineString 功能，请将层的 `filter` 属性设置为 `['==', ['geometry-type'], 'LineString']` 或 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` （如果还想要包括 MultiLineString 功能）。

下面的代码演示如何创建一行。 将行添加到数据源，然后使用[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)类通过线条层呈现该行。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='向地图添加线条' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>（向地图添加线条）。
</iframe>

可以使用[LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)为线条层设计样式，并[使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)。

## <a name="add-symbols-along-a-line"></a>沿行添加符号

此示例演示如何沿地图上的线条添加箭头图标。 使用符号层时，请将 "放置" 选项设置为 "line"。 此选项将沿行呈现符号并旋转图标（0度 = 右）。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="沿直线显示箭头" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "沿行 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）"<a href='https://codepen.io/azuremaps/pen/drBJwX/'>沿线条显示箭头</a>。
</iframe>

> [!TIP]
> Azure Maps web SDK 提供了几个可自定义的图像模板，你可以将其用于符号层。 有关详细信息，请参阅[如何使用映像模板](how-to-use-image-templates-web-sdk.md)文档。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>向线条添加笔划渐变

您可以将一种笔划颜色应用于线条。 您还可以使用颜色渐变填充线条，以显示从一个直线段到下一个线段的转换。 例如，线条梯度可用于表示一段时间和距离内的变化，或与连接的对象线之间的不同温度。 为了将此功能应用到行，数据源必须将 `lineMetrics` 选项设置为 true，然后可以将颜色渐变表达式传递到行的 `strokeColor` 选项。 笔划渐变表达式必须引用 `['line-progress']` 数据表达式，该表达式将计算行指标公开给表达式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有描边渐变的线条" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
通过<a href='https://codepen.io'>CodePen</a>上的 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>），查看<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>具有笔划渐变</a>的笔线。
</iframe>

## <a name="customize-a-line-layer"></a>自定义线条层

线条层具有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='线条层选项' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>线条层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用映像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)
