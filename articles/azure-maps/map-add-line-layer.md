---
title: 向地图添加线图层 |微软 Azure 地图
description: 在本文中，您将学习如何使用 Microsoft Azure 地图 Web SDK 将线图层添加到地图中。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933469"
---
# <a name="add-a-line-layer-to-the-map"></a>向地图添加线图层

线图层可用于在地图上渲染`LineString`和`MultiLineString`要素作为路径或路径。 线图层还可用于呈现 和`Polygon``MultiPolygon`要素的轮廓。 数据源连接到线图层，以提供要呈现的数据。 

> [!TIP]
> 默认情况下，线图层将呈现数据源中的多边形和行的坐标。 要限制图层，使其仅渲染 LineString 要素，`filter`请将图层的属性设置为`['==', ['geometry-type'], 'LineString']``['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`或是否还希望也包含多LineString要素。

以下代码演示如何创建行。 将线添加到数据源，然后使用[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)类使用线图层呈现它。

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

可以使用[LineLayer 选项](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)[并使用数据驱动的样式表达式](data-driven-style-expressions-web-sdk.md)对线图层进行样式设置。

## <a name="add-symbols-along-a-line"></a>沿线添加符号

此示例演示如何沿地图上的线条添加箭头图标。 使用符号图层时，将"放置"选项设置为"行"。 此选项将沿直线渲染符号并旋转图标（0 度 = 右）。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="沿线显示箭头" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 沿行查看"笔<a href='https://codepen.io/azuremaps/pen/drBJwX/'>显示"箭头</a>。
</iframe>

> [!TIP]
> Azure 地图 Web SDK 提供了多个可自定义的图像模板，可用于符号图层。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>向线条添加描边渐变

您可以将单个描边颜色应用于线条。 您还可以用颜色渐变填充线条，以显示从一个线段到下一个线段的过渡。 例如，线渐变可用于表示随时间和距离的变化，或跨连接的对象线的不同温度。 为了将此功能应用于行，数据源必须将`lineMetrics`选项设置为 true，然后可以将颜色渐变表达式传递给行`strokeColor`的选项。 描边渐变表达式必须引用向`['line-progress']`表达式公开计算的行指标的数据表达式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带描边渐变的线条" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 显示<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>具有描边渐变的笔线</a>。
</iframe>

## <a name="customize-a-line-layer"></a>自定义线条层

Line 图层具有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='线条层选项' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>线条层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [线层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [线图层选项](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)
