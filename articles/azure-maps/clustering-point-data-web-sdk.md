---
title: 在地图上聚类点数据 |微软 Azure 地图
description: 在本文中，您将学习如何使用 Microsoft Azure 地图 Web SDK 对点数据进行群集并在地图上呈现数据。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804565"
---
# <a name="clustering-point-data"></a>聚类点数据

在地图上可视化许多数据点时，数据点可能会相互重叠。 重叠可能导致地图变得不可读且难以使用。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。 处理大量数据点时，请使用群集过程来改善用户体验。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>在数据源上启用群集

通过将选项设置为 true，`DataSource``cluster`在类中启用群集。 设置为`ClusterRadius`选择附近的点并将它们合并到群集中。 的值`ClusterRadius`以像素为单位。 用于`clusterMaxZoom`指定用于禁用聚类逻辑的缩放级别。 下面是如何在数据源中启用群集的示例。

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
> 如果两个数据点在地面上接近，则无论用户放大有多近，群集都可能永远不会分崩离析。 为了解决这个问题，`clusterMaxZoom`您可以设置禁用群集逻辑的选项，并仅显示所有内容。

下面是`DataSource`类为群集提供的其他方法：

| 方法 | 返回类型 | 说明 |
|--------|-------------|-------------|
| 获取群集子级（群集 ID：编号） | 承诺&lt;数组&lt;要素&lt;几何，任何&gt;\|形状&gt;&gt; | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是包含与 ClusteredProperties 匹配的属性的特征。 |
| 获取群集扩展（群集 Id：编号） | Promise&lt;number&gt; | 计算聚类开始展开或分开的缩放级别。 |
| 获取群集叶（群集 Id：数量、限制：数字、偏移量：数字） | 承诺&lt;数组&lt;要素&lt;几何，任何&gt;\|形状&gt;&gt; | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡图层显示群集

气泡图层是渲染聚类点的好方法。 使用表达式缩放半径并根据群集中的点数更改颜色。 如果使用气泡图层显示群集，则应使用单独的图层来呈现未聚类的数据点。

要在气泡顶部显示群集的大小，请使用带有文本的符号图层，并且不要使用图标。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本气泡层聚类" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 群集的笔<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本气泡图层</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符号图层显示群集

可视化数据点时，符号图层会自动隐藏彼此重叠的符号，以确保用户界面更简洁。 如果要在地图上显示数据点密度，则此默认行为可能是不可取的。 但是，可以更改这些设置。 要显示所有符号，将`allowOverlap`"符号"图层`iconOptions`属性的选项设置为`true`。 

使用聚类显示数据点密度，同时保持干净的用户界面。 下面的示例演示如何添加自定义符号，并使用符号图层表示聚类和单个数据点。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类符号图层" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>群集符号图层</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚类和热图图层

热图是显示地图上数据密度的好方法。 此可视化方法可以自行处理大量数据点。 如果数据点是聚类的，并且聚类大小用作热图的重量，则热图可以处理更多的数据。 要实现此选项，将`weight`热图图层的选项设置为`['get', 'point_count']`。 当聚类半径较小时，热图看起来与使用未聚类数据点的热图几乎相同，但它的性能会好得多。 但是，群集半径越小，热图就越准确，但性能优势却越小。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚类加权热图" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>群集加权热图</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>群集数据点上的鼠标事件

当鼠标事件发生在包含群集数据点的图层上时，群集数据点将作为 GeoJSON 点要素对象返回到事件。 此点功能将具有以下属性：

| 属性名称             | 类型    | 说明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示特征是否表示聚类。 |
| `cluster_id`              | 字符串  | 可与数据源 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法结合使用的群集唯一 ID。 |
| `point_count`             | 数字  | 聚类包含的点数。  |
| `point_count_abbreviated` | 字符串  | 如果值长，则缩写该`point_count`值的字符串。 （例如，将 4,000 缩写为 4K）  |

本示例采用一个气泡图层，该气泡图层呈现聚类点并添加单击事件。 单击事件触发时，代码将计算地图并将其缩放到下一个缩放级别，群集将断开。 此功能使用`getClusterExpansionZoom``DataSource`类的方法和单击的群集数据点`cluster_id`的属性实现。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集获取群集扩展缩放" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅在<a href='https://codepen.io'>CodePen</a>上通过 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/moZWeV/'>获取群集扩展的笔群集扩展</a>。
</iframe>

## <a name="display-cluster-area"></a>显示群集区域 

群集表示的点数据分布在区域上。 在此示例中，当鼠标悬停在群集上时，会发生两种主要行为。 首先，群集中包含的单个数据点将用于计算凸包。 然后，凸壳将显示在地图上以显示区域。  凸包是一个多边形，它包裹一组点，如弹性带，可以使用 方法`atlas.math.getConvexHull`计算。 可以使用`getClusterLeaves`方法从数据源检索群集中包含的所有点。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="群集区域凸壳" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 凸包的笔<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>群集区域凸包</a>。
</iframe>

## <a name="aggregating-data-in-clusters"></a>聚合群集中的数据

通常，使用具有群集内点数的符号表示群集。 但是，有时最好使用其他指标自定义群集样式。 使用群集聚合，可以使用[聚合表达式](data-driven-style-expressions-web-sdk.md#aggregate-expression)计算创建和填充自定义属性。  群集聚合可以在 的选项`clusterProperties``DataSource`中定义。

以下示例使用聚合表达式。 代码根据群集中每个数据点的实体类型属性计算计数。 当用户单击群集时，将显示一个弹出窗口，其中包含有关群集的其他信息。

<iframe height="500" style="width: 100%;" scrolling="no" title="群集聚合" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看笔<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>群集聚合</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [数据源类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [数据源选项对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯.数学命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [添加气泡图层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加符号图层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加热度地图层](map-add-heat-map-layer.md)
