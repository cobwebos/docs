---
title: 在地图上聚类点数据 | Microsoft Azure Maps
description: 了解如何对地图上的数据点进行分类。 了解如何使用 Azure Maps Web SDK 对数据进行分类、对群集鼠标事件做出反应并显示群集聚合。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 207106d8daab4d5d2a6d8d681aad5a20923a5a48
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009046"
---
# <a name="clustering-point-data"></a>聚类点数据

在地图上可视化大量的数据点时，数据点将会彼此重叠。 重叠可能会导致地图不可读且难以使用。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。 处理大量数据点时，请使用聚类过程来改善用户体验。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>对数据源启用聚类

通过将 `cluster` 选项设置为 true，在 `DataSource` 类中启用聚类。 设置 `ClusterRadius` 以选择附近的点，并将它们合并为聚类。 `ClusterRadius` 的值以像素为单位。 使用 `clusterMaxZoom` 指定禁用聚类逻辑的缩放级别。 下面的示例演示如何在数据源中启用聚类。

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
> 如果两个数据点在地面上紧挨着，则聚类可能永远不会分解，无论用户放大多少倍。 若要解决此情况，可以设置 `clusterMaxZoom` 选项以禁用聚类逻辑，只是显示所有内容。

下面是 `DataSource` 类为聚类提供的其他方法：

| 方法 | 返回类型 | 说明 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是包含与 ClusteredProperties 匹配的属性的特征。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | 计算聚类开始展开或分开的缩放级别。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡层显示聚类

气泡层是呈现聚类点的好方法。 使用表达式可基于聚类中的点数来缩放半径并更改颜色。 如果使用气泡层显示聚类，则应使用单独的层来呈现非聚类数据点。

若要在气泡顶部显示聚类的大小，请将符号层与文本一起使用，而不要使用图标。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本气泡层聚类" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本气泡层聚类</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符号层显示聚类

可视化数据点时，符号层会自动隐藏彼此重叠的符号，以确保获得更清晰的用户界面。 如果要在地图上显示数据点密度，则可能不需要此默认行为。 但是，可以更改这些设置。 若要显示所有符号，请将符号层 `iconOptions` 属性的 `allowOverlap` 选项设置为 `true`。 

使用聚类显示数据点密度，同时保持整洁的用户界面。 下面的示例演示如何使用符号层添加自定义符号并表示聚类和各个数据点。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类符号层" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>聚类符号层</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚类和热度地图层

热度地图是在地图上显示数据密度的好方法。 此可视化方法可以自行处理大量数据点。 如果数据点进行了聚类，并且聚类大小用作热度地图的权重，则热度地图可以处理更多的数据。 若要实现此选项，请将热度地图层的 `weight` 选项设置为 `['get', 'point_count']`。 当聚类半径较小时，热度地图看起来几乎与使用非聚类数据点的热度地图完全相同，但性能会更好。 但是，聚类半径越小，热度地图便越精确，不过性能优势便越少。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类加权热度地图" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>聚类加权热度地图</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>聚类数据点上的鼠标事件

当鼠标事件在包含聚类数据点的层上发生时，聚类数据点会作为 GeoJSON 点特征对象返回给事件。 此点特征具有以下属性：

| 属性名称             | 类型    | 描述   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示特征是否表示聚类。 |
| `cluster_id`              | 字符串  | 可与数据源 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法结合使用的群集唯一 ID。 |
| `point_count`             | 数字  | 聚类包含的点数。  |
| `point_count_abbreviated` | 字符串  | 用于缩写过长的 `point_count` 值的字符串。 （例如，将 4,000 缩写为 4K）  |

此示例采用一个呈现聚类点并添加单击事件的气泡层。 当单击事件触发时，代码会计算地图，并将它缩放到下一个缩放级别，聚类将在该缩放级别分解。 此功能使用 `DataSource` 类的 `getClusterExpansionZoom` 方法和所单击的聚类数据点的 `cluster_id` 属性来实现。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>聚类 getClusterExpansionZoom</a>。
</iframe>

## <a name="display-cluster-area"></a>显示聚类区域 

聚类表示的点数据会分散在某个区域。 在此示例中，当鼠标悬停在聚类上方时，会发生两种主要行为。 首先，该聚类中包含的各个数据点将用于计算凸包。 然后，凸包会显示在地图上以显示一个区域。  凸包是一种多边形，用于包装一组点（如松紧带），可以使用 `atlas.math.getConvexHull` 方法进行计算。 可以使用 `getClusterLeaves` 方法从数据源中检索包含在聚类中的所有点。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="聚类区域凸包" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>聚类区域凸包</a>。
</iframe>

## <a name="aggregating-data-in-clusters"></a>聚合聚类中的数据

通常使用符号以及聚类中的点数来表示聚类。 但有时需要使用其他指标自定义聚类样式。 使用聚类聚合，可以使用[聚合表达式](data-driven-style-expressions-web-sdk.md#aggregate-expression)计算来创建和填充自定义属性。  可以在 `DataSource` 的 `clusterProperties` 选项中定义聚类聚合。

下面的示例使用聚合表达式。 此代码基于聚类中每个数据点的实体类型属性计算计数。 当用户单击某个聚类时，将显示一个弹出窗口，其中包含有关该聚类的其他信息。

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类聚合" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>聚类聚合</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [DataSource 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math 命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加热度地图层](map-add-heat-map-layer.md)
