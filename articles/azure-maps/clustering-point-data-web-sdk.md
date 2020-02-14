---
title: 在地图上聚集点数据 |Microsoft Azure 映射
description: 在本文中，你将了解如何使用 Microsoft Azure map Web SDK 来分类点数据并将其呈现在地图上。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190266"
---
# <a name="clustering-point-data"></a>聚类数据

可视化地图上的多个数据点时，数据点可能相互重叠。 重叠可能会导致映射不可读且难以使用。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。 处理大量数据点时，请使用聚类处理过程来改善用户体验。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>对数据源启用群集功能

通过将 `cluster` 选项设置为 true，在 `DataSource` 类中启用群集。 将 `ClusterRadius` 设置为选择附近的点，并将其合并到群集。 `ClusterRadius` 的值以像素为单位。 使用 `clusterMaxZoom` 指定禁用聚类分析逻辑的缩放级别。 下面的示例演示如何在数据源中启用群集。

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
> 如果两个数据点在地面上彼此接近，则群集可能永远不会中断，无论用户在多长时间内进行放大。 若要解决此情况，可以设置 `clusterMaxZoom` 选项来禁用聚类逻辑，并只显示所有内容。

下面是 `DataSource` 类为聚类提供的其他方法：

| 方法 | 返回类型 | 说明 |
|--------|-------------|-------------|
| getClusterChildren （clusterId： number） | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是包含与 ClusteredProperties 匹配的属性的特征。 |
| getClusterExpansionZoom （clusterId： number） | Promise&lt;number&gt; | 计算聚类开始展开或分开的缩放级别。 |
| getClusterLeaves （clusterId： number，limit： number，offset： number） | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡图层显示分类

气泡图层是渲染聚集点的好方法。 使用表达式可以根据群集中的点数，缩放 radius 并更改颜色。 如果使用气泡层显示群集，则应使用单独的层来呈现非群集的数据点。

若要在气泡顶部显示群集的大小，请将符号层用于文本，不要使用图标。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本气泡层群集" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）对<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本气泡层进行聚类分析</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符号层显示群集

可视化数据点时，符号层会自动隐藏彼此重叠的符号，以确保更清晰的用户界面。 如果要在地图上显示数据点密度，则可能不需要此默认行为。 但是，可以更改这些设置。 若要显示所有符号，请将符号层 `iconOptions` 属性的 `allowOverlap` 选项设置为 "`true`"。 

使用聚类分析来显示数据点密度，同时保持干净的用户界面。 下面的示例演示如何使用符号层添加自定义符号并表示群集和各个数据点。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚集符号层" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）将笔<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>聚集符号层</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚类分析和热度地图层

热度地图是在地图上显示数据密度的好方法。 此可视化方法可以自行处理大量数据点。 如果数据点已群集化，并且簇大小用作热度地图的权重，则热度地图可以处理更多的数据。 若要实现此选项，请将热度地图层的 `weight` 选项设置为 "`['get', 'point_count']`"。 当群集的 radius 较小时，热度地图看起来几乎与使用非群集数据点的热度地图完全相同，但性能会更好。 但是，更小的群集半径越小，热度地图就越精确，但性能优势就越少。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集加权热度地图" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>绘制笔群集加权热度地图</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>群集数据点上的鼠标事件

当鼠标事件发生在包含群集数据点的层上时，群集数据点将作为 GeoJSON 点功能对象返回到事件。 此点功能将具有以下属性：

| 属性名称             | 类型    | 说明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示特征是否表示聚类。 |
| `cluster_id`              | string  | 可与数据源 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法结合使用的群集唯一 ID。 |
| `point_count`             | number  | 聚类包含的点数。  |
| `point_count_abbreviated` | string  | 一个字符串，如果该 `point_count` 值过长，则该字符串缩写。 （例如，将 4,000 缩写为 4K）  |

此示例采用一个呈现分类点并添加一个 click 事件的气泡图层。 当单击事件触发时，代码将计算地图，并将其缩放到下一个缩放级别，分类将在该缩放级别中断。 此功能是使用 `DataSource` 类的 `getClusterExpansionZoom` 方法和被单击的群集数据点的 `cluster_id` 属性实现的。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 getClusterExpansionZoom by Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的笔<a href='https://codepen.io/azuremaps/pen/moZWeV/'>群集</a>。
</iframe>

## <a name="display-cluster-area"></a>显示群集区域 

分类所代表的点数据分散在某个区域。 在此示例中，当鼠标悬停在群集上时，将发生两种主要行为。 首先，群集中包含的各个数据点将用于计算凸首。 然后，凸形凸形将显示在地图上以显示一个区域。  凸形凸形是一种用于包装一组点（如弹性带区）并可使用 `atlas.math.getConvexHull` 方法进行计算的多边形。 可以使用 `getClusterLeaves` 方法从数据源中检索包含在群集中的所有点。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="群集区域凸凸" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）进行的<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>凸</a>形凸形凸形凸形。
</iframe>

## <a name="aggregating-data-in-clusters"></a>聚合群集中的数据

通常使用带有群集内点数的符号来表示分类。 但有时需要自定义具有其他指标的分类样式。 使用分类聚合，可以使用[聚合表达式](data-driven-style-expressions-web-sdk.md#aggregate-expression)计算来创建和填充自定义属性。  可以在 `DataSource``clusterProperties` 选项中定义群集聚合。

下面的示例使用聚合表达式。 此代码根据群集中每个数据点的实体类型属性计算计数。 当用户单击群集时，将显示一个弹出窗口，其中包含有关该群集的其他信息。

<iframe height="500" style="width: 100%;" scrolling="no" title="群集聚合" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>群集聚合</a>按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [DataSource 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

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
