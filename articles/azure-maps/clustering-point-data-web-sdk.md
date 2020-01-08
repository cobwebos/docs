---
title: Azure Maps 中的聚集点数据 |Microsoft Docs
description: 如何在 Web SDK 中分类点数据
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 56d9a9a629e64430c97cf392ee4381e1ad7ca906
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433037"
---
# <a name="clustering-point-data"></a>聚类数据

当在地图上可视化多个数据点时，点彼此重叠，地图看起来显得有些混乱，因而难以查看和使用。 可使用点数据的聚类分析来改善此用户的体验。 聚类数据是将相邻的点数据组合在一起并将其作为单个群集数据点表示在地图上的过程。 当用户放大到地图中时，分类将分成各自的数据点。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>对数据源启用群集功能

可以通过将 `cluster` 选项设置为 true，在 `DataSource` 类上轻松启用群集。 此外，还可以使用 `clusterRadius` 设置像素半径来选择要合并到群集中的相邻点，还可以使用 "`clusterMaxZoom`" 选项指定缩放级别，以禁用群集逻辑。 下面的示例演示如何在数据源中启用群集。

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
> 如果两个数据点在地面上彼此接近，则群集可能永远不会中断，无论用户在多长时间内进行放大。 若要解决此情况，您可以设置数据源的 `clusterMaxZoom` 选项，该选项在缩放级别指定以禁用聚集逻辑，只显示所有内容。

`DataSource` 类还具有以下与聚类分析相关的方法：

| 方法 | 返回类型 | Description |
|--------|-------------|-------------|
| getClusterChildren （clusterId： number） | 承诺&lt;数组&lt;功能&lt;几何，任何&gt; \| 形状&gt;&gt; | 在下一个缩放级别检索给定分类的子项。 这些子级可以是形状和 subclusters 的组合。 Subclusters 将是具有与 ClusteredProperties 匹配的属性的功能。 |
| getClusterExpansionZoom （clusterId： number） | 承诺&lt;号&gt; | 计算群集开始展开或分离的缩放级别。 |
| getClusterLeaves （clusterId： number，limit： number，offset： number） | 承诺&lt;数组&lt;功能&lt;几何，任何&gt; \| 形状&gt;&gt; | 检索群集中的所有点。 设置 `limit` 以返回部分点，并使用 `offset` 逐页浏览点。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡图层显示分类

气泡图层是一种呈现聚类的好方法，因为可以轻松地缩放半径，并使用表达式根据分类中的点数更改颜色。 使用气泡图层显示群集时，还应使用单独的层来呈现非群集的数据点。 还可以在气泡顶部显示群集的大小，这也是一种很好的方法。 带有文本和无图标的符号层可用于实现此行为。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本气泡层群集" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）对<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本气泡层进行聚类分析</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符号层显示群集

使用符号层可视化点数据时，默认情况下，它会自动隐藏彼此重叠的符号，以创建更清晰的体验，但如果您想要查看地图上数据点的密度，这可能不是所需的体验。 将 "符号层 `iconOptions`" 属性 `allowOverlap` 选项设置为 "`true` 禁用此体验，但将导致显示所有符号。 使用聚类分析可查看所有数据的密度，同时创建良好的干净用户体验。 在此示例中，自定义符号将用于表示群集和各个数据点。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚集符号层" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）将笔<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>聚集符号层</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚类分析和热度地图层

热度地图是在地图上显示数据密度的好方法。 此可视化效果可以自行处理大量数据点，但如果数据点是聚集的，则可以处理更多的数据，并将群集大小用作热度地图的权重。 将热度地图层的 `weight` 选项设置为 `['get', 'point_count']` 以实现此目的。 当群集的 radius 较小时，热度地图看起来几乎与使用非群集数据点的热度地图完全相同，但性能会更好。 然而，分类半径越小，热度地图就越精确，但性能优势也就越小。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集加权热度地图" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>绘制笔群集加权热度地图</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>群集数据点上的鼠标事件

当鼠标事件发生在包含群集数据点的层上时，群集数据点将作为 GeoJSON 点功能对象返回到事件。 此点功能将具有以下属性：

| 属性名称             | 类型    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示功能是否表示群集。 |
| `cluster_id`              | 字符串  | 可以与 DataSource 一起使用的群集的唯一 ID `getClusterExpansionZoom`、`getClusterChildren`和 `getClusterLeaves` 方法。 |
| `point_count`             | 数字  | 群集包含的点数。  |
| `point_count_abbreviated` | 字符串  | 一个字符串，如果 `point_count` 值过长，则该字符串缩写。 （例如，4000变为4K）  |

此示例采用一个呈现分类点的气泡图层，并添加一个单击事件，当触发、计算并缩放地图到下一个缩放级别时，将会使用 `DataSource` 类的 `getClusterExpansionZoom` 方法和已单击的群集数据点的 `cluster_id` 属性来分离该地图。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 getClusterExpansionZoom by Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的笔<a href='https://codepen.io/azuremaps/pen/moZWeV/'>群集</a>。
</iframe>

## <a name="display-cluster-area"></a>显示群集区域 

分类所代表的点数据分散在某个区域。 在此示例中，当鼠标悬停在群集上时，它包含的各个数据点（叶）将用于计算凸形，并显示在地图上以显示区域。 可以使用 `getClusterLeaves` 方法从数据源中检索包含在群集中的所有点。 凸形凸形是一种用于包装一组点（如弹性带区）并可使用 `atlas.math.getConvexHull` 方法进行计算的多边形。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="群集区域凸凸" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）进行的<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>凸</a>形凸形凸形凸形。
</iframe>

## <a name="aggregating-data-in-clusters"></a>聚合群集中的数据

通常使用带有群集内点数的符号来表示分类，但有时需要根据某个指标进一步自定义分类的样式，如群集中所有点的总收入。 使用分类聚合自定义属性可以使用[聚合表达式](data-driven-style-expressions-web-sdk.md#aggregate-expression)计算来创建和填充。  可以在 `DataSource``clusterProperties` 选项中定义群集聚合。

下面的示例使用聚合表达式来计算基于群集中每个数据点的实体类型属性的计数。

<iframe height="500" style="width: 100%;" scrolling="no" title="群集聚合" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>群集聚合</a>按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [数据源类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加热度地图层](map-add-heat-map-layer.md)
