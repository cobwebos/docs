---
title: Azure Maps 中的点数据聚类分析 |Microsoft Docs
description: 如何对 Web SDK 中的群集点数据
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 6dbd4461e7b8382ec3c4075b9688de59678f98f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957328"
---
# <a name="clustering-point-data"></a>聚类分析点数据

可视化代码图上的多个数据点时, 点相互重叠、 地图显示混乱和变得非常困难，可以查看并使用。 可以使用聚类分析的点数据以提高这种用户体验。 聚类分析的数据点是合并彼此邻近点数据并代表它们将 map 作为单个群集的数据点上的过程。 因为用户将缩放在映射中，群集拆分到其单个数据点。

## <a name="enabling-clustering-on-a-data-source"></a>启用数据源进行聚类分析

聚类分析可以轻松地在上启用`DataSource`通过设置类`cluster`选项为 true。 此外，像素 radius 选择邻近点组合成群集可以使用设置`clusterRadius`和缩放级别可以指定要禁用聚类分析的逻辑使用`clusterMaxZoom`选项。 下面是如何启用数据源中的聚类分析的示例。

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> 如果两个数据点在地面上相差无几，则可能永远不会破坏相隔，无论在接近程度用户缩放群集。 若要解决此问题，可以设置`clusterMaxZoom`要禁用的聚类分析逻辑，并仅显示所有内容的缩放级别在指定的数据源的选项。

`DataSource`类还具有与群集相关的以下方法：

| 方法 | 返回类型 | 描述 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | 承诺&lt;数组&lt;功能&lt;几何形状，任何&gt;\|形状&gt;&gt; | 检索给定群集上的下一步的缩放级别的子项。 这些子对象可能是形状和 subclusters 的组合。 Subclusters 将属性与匹配 ClusteredProperties 的功能。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | 计算群集将开始扩展或分离的缩放级别。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | 承诺&lt;数组&lt;功能&lt;几何形状，任何&gt;\|形状&gt;&gt; | 检索群集中的所有点。 设置`limit`以返回点的子集，并使用`offset`点翻页。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡图层显示群集

气泡图层是呈现聚集的点，因为您可以轻松地缩放 radius 和更改它们使用表达式基于群集中的点的数目的颜色的好办法。 在显示时使用气泡图层的群集，还应呈现非群集的数据点使用一个单独的层。 通常是最好还能够显示在气泡之上群集的大小。 具有文本和没有图标的符号层可用来实现此行为。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类分析的基本气泡图层" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本气泡图层的聚类分析</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>显示使用符号层的聚类

当可视化点数据使用符号层中，默认情况下将自动隐藏符号重叠以形成更清晰的体验时，但是这可能不是所需的体验如果你想要查看数据的密度在地图上点。 设置`allowOverlap`符号层的选项`iconOptions`属性设置为`true`禁用这种体验，但会导致显示的所有符号。 使用聚类分析，可创建清晰的良好用户体验时看到的所有数据的密度。 在此示例中，将使用自定义的符号来表示群集和各个数据点。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚集的符号层" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>聚集符号层</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚类分析和热映射层

热图是在地图上显示数据的密度的好办法。 此可视化效果可以处理大量的数据点，但它可以处理更多数据，如果数据点簇和群集大小将用作热度地图的权重。 设置`weight`热度地图层选项`['get', 'point_count']`来实现此目的。 如果群集半径较小，热图看起来与使用非群集的数据点热度地图几乎完全相同，但将执行得更好。 但是，越小群集 radius、 更准确热度地图将但小于性能中受益。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集实施的加权热度地图" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>群集实施的加权热度地图</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>聚类数据点上的鼠标事件

鼠标事件发生时包含聚集的数据点的图层上，将为 GeoJSON 点功能对象事件返回聚类数据点。 此点功能将具有以下属性：

| 属性名称 | Type | 描述 |
|---------------|------|-------------|
| cluster | boolean | 指示功能是否表示一个群集。 |
| cluster_id | 字符串 | 可以在数据源的群集的唯一 ID `getClusterExpansionZoom`， `getClusterChildren`，和`getClusterLeaves`方法。 |
| point_count | 数字 | 分类包含的点的数目。 |
| point_count_abbreviated | 字符串 | 一个字符串，缩写`point_count`值如果声音太长。 （例如，4,000 成为 4k） |

此示例采用气泡图层的呈现群集点并添加一个单击事件的触发时，计算，并为下一步的缩放级别，群集将中断分开使用放大地图`getClusterExpansionZoom`方法的`DataSource`类和`cluster_id`属性被单击的聚集数据点。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/moZWeV/'>群集 getClusterExpansionZoom</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-cluster-area"></a>显示群集区域 

群集表示的点数据分散到一个区域中。 在此示例中当鼠标悬停于群集中，单个数据点包含 （叶） 将用于计算一个凸包并显示在该地图以显示区域上。 可以从使用数据源中检索包含在群集中的所有点`getClusterLeaves`方法。 一个凸包是一个封装一组点的多边形和等弹性外可以使用计算`atlas.math.getConvexHull`方法。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="群集区域凸包" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>群集区域凸球面</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [数据源类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加热度地图层](map-add-heat-map-layer.md)
