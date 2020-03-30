---
title: Azure 地图 Web SDK 中的图像模板 |微软 Azure 地图
description: 在本文中，您将学习如何在 Microsoft Azure 地图 Web SDK 中使用带有 HTML 标记和各种图层的图像模板。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198218"
---
# <a name="how-to-use-image-templates"></a>如何使用图像模板

图像可用于 Azure 地图 Web SDK 中的 HTML 标记和各种图层：

 - 符号图层可以使用图像图标渲染地图上的点。 符号也可以沿线路径呈现。
 - 可以使用填充图案图像渲染多边形图层。 
 - HTML 标记可以使用图像和其他 HTML 元素呈现点。

为了确保图层性能良好，在渲染之前，将图像加载到地图图像精灵资源中。 默认情况下，SymbolLayer 的[IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)会以少量颜色预加载几个标记图像到地图图像精灵中。 这些标记图像等可作为 SVG 模板提供。 它们可用于创建具有自定义比例的图像，或用作客户主色和次要颜色。 总共提供了 42 个图像模板：27 个符号图标和 15 个多边形填充图案。

可以使用`map.imageSprite.createFromTemplate`函数将图像模板添加到地图图像精灵资源中。 此功能允许最多传入五个参数;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

是`id`您创建的唯一标识符。 当`id`图像添加到地图图像精灵时，将分配给该图像。 在图层中使用此标识符来指定要呈现的图像资源。 指定`templateName`要使用的图像模板。 该`color`选项设置图像的主颜色，`secondaryColor`选项设置图像的次色。 在`scale`将图像模板应用于图像子画面之前，该选项将缩放图像模板。 当图像应用于图像精灵时，它将转换为 PNG。 为了确保清晰的渲染，最好在将图像模板添加到子画面之前将其放大，而不是将其放大到图层中。

此功能异步地将图像加载到图像子画面中。 因此，它返回一个承诺，您可以等待此函数完成。

以下代码演示如何从其中一个内置模板创建图像，并将其与符号图层一起使用。

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>将图像模板与符号图层一起使用

将图像模板加载到地图图像雪碧中后，可以通过引用 选项中的图像资源 ID`image`将其呈现为符号图层中的符号。 `iconOptions`

以下示例使用具有青色原色和白色次`marker-flat`色的图像模板渲染符号图层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的符号图层" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>具有内置图标模板的笔符号图层</a>。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>沿线路径使用图像模板

将图像模板加载到地图图像 sprite 中后，可以通过向数据源添加 LineString 并使用带有`lineSpacing`选项的符号图层以及引用 th`image``iconOptions`选项中图像资源 ID 来沿线路径呈现。 

以下示例在地图上渲染一条粉红色线，并使用具有道奇蓝色原色和白色`car`次要颜色的图像模板使用符号图层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带内置图标模板的线图层" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>具有内置图标模板的笔线图层</a>。
</iframe>

> [!TIP]
> 如果图像模板指向该点，如果希望`rotation`符号图层指向与线相同的方向，则将符号图层的图标选项设置为 90。

## <a name="use-an-image-template-with-a-polygon-layer"></a>将图像模板与多边形图层一起使用

将图像模板加载到地图图像子画面中后，可以通过引用图层`fillPattern`选项中的图像资源 ID 将其呈现为多边形图层中的填充图案。

以下示例使用红色原色和透明次色`dot`的图像模板渲染多边形图层。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用内置图标模板填充多边形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 映射 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>使用<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>内置图标模板查看笔填充多边形</a>。
</iframe>

> [!TIP]
> 设置填充图案的辅助颜色可以更轻松地查看基础地图仍将提供主图案。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用带有 HTML 标记的图像模板

可以使用`altas.getImageTemplate`函数检索图像模板，并将其用作 HTML 标记的内容。 模板可以传递到`htmlContent`标记的选项，然后使用`color`和`secondaryColor``text`选项进行自定义。

以下示例使用红色原`marker-arrow`色、粉红色辅助色和文本值"00"的模板。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的 HTML 标记" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 映射 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>具有内置图标模板的</a>笔 HTML 标记。
</iframe>

## <a name="create-custom-reusable-templates"></a>创建自定义可重用模板

如果应用程序使用不同的图标使用同一图标，或者正在创建添加其他图像模板的模块，则可以轻松地从 Azure 地图 Web SDK 添加和检索这些图标。 在`atlas`命名空间上使用以下静态函数。

| “属性” | 返回类型 | 描述 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 将自定义 SVG 图像模板添加到地图集命名空间。 |
|  `getImageTemplate(templateName: string, scale?: number)`| 字符串 | 按名称检索 SVG 模板。 |
| `getAllImageTemplateNames()` | string[] |  按名称检索 SVG 模板。 |

SVG 图像模板支持以下占位符值：

| 占位符 | 描述 |
|-|-|
| `{color}` | 主颜色。 | 
| `{secondaryColor}` | 辅助颜色。 | 
| `{scale}` | SVG 图像在添加到地图图像精灵时转换为 png 图像。 此占位符可用于在转换模板之前缩放模板，以确保模板呈现清晰。 | 
| `{text}` | 与 HTML 标记一起使用时要呈现文本的位置。 |

下面的示例演示如何采用 SVG 模板，并将其作为可重用图标模板添加到 Azure 地图 Web SDK 中。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="将自定义图标模板添加到地图集命名空间" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅在 CodePen 上按 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a><a href='https://codepen.io/azuremaps/pen/NQyvEX/'>向地图集添加自定义图标模板</a><a href='https://codepen.io'>的笔</a>。
</iframe>

## <a name="list-of-image-templates"></a>图像模板列表

此表列出了 Azure 地图 Web SDK 中当前提供的所有映像模板。 模板名称在每个图像上方。 默认情况下，主色为蓝色，辅助颜色为白色。 为了使次要颜色更容易在白色背景上看到，以下图像将次色设置为黑色。

**符号图标模板**

|||||
|:-:|:-:|:-:|:-:|
| marker | 标记厚 | 标记圆 | 标记平面 |
|![标记图标](./media/image-templates/marker.png)|![标记厚图标](./media/image-templates/marker-thick.png)|![标记圆图标](./media/image-templates/marker-circle.png)|![标记平面图标](./media/image-templates/marker-flat.png)|
||||
| 标记方 | 标记-方形群集 | 标记箭头 | 标记-球针 | 
|![标记方形图标](./media/image-templates/marker-square.png)|![标记方形群集图标](./media/image-templates/marker-square-cluster.png)|![标记箭头图标](./media/image-templates/marker-arrow.png)|![标记-球针图标](./media/image-templates/marker-ball-pin.png)|
||||
| 标记-平方圆舍五入 | 标记-平方圆-聚类 | 标志 | 标志三角形 |
| ![标记方形圆角图标](./media/image-templates/marker-square-rounded.png) | ![标记-方形圆角-群集图标](./media/image-templates/marker-square-rounded-cluster.png) | ![标志图标](./media/image-templates/flag.png) | ![标志三角形图标](./media/image-templates/flag-triangle.png) |
||||
| 三角形 | 三角形厚 | 三角形箭头向上 | 三角形箭头-左侧 |
| ![三角形图标](./media/image-templates/triangle.png) | ![三角形厚的图标](./media/image-templates/triangle-thick.png) | ![三角形箭头图标](./media/image-templates/triangle-arrow-up.png) | ![三角形箭头左侧图标](./media/image-templates/triangle-arrow-left.png) |
||||
| 六 角 | 六边形厚 | 六边形圆润 | 六边形-圆形厚 |
| ![六边形图标](./media/image-templates/hexagon.png) | ![六边形厚图标](./media/image-templates/hexagon-thick.png) | ![六边形圆角图标](./media/image-templates/hexagon-rounded.png) | ![六边形四舍五入厚图标](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | 针圆 | 圆方形 | 圆方形厚 |
| ![“固定”图标](./media/image-templates/pin.png) | ![针圆图标](./media/image-templates/pin-round.png) | ![圆形方形图标](./media/image-templates/rounded-square.png) | ![圆形方形厚图标](./media/image-templates/rounded-square-thick.png) |
||||
| 箭头 | 箭头向上薄 | 汽车 ||
| ![箭头图标](./media/image-templates/arrow-up.png) | ![箭头向上精简图标](./media/image-templates/arrow-up-thin.png) | ![汽车图标](./media/image-templates/car.png) | |

**多边形填充图案模板**

|||||
|:-:|:-:|:-:|:-:|
| 检查 | 跳棋旋转 | 圆形 | 圆间距 |
| ![检查器图标](./media/image-templates/checker.png) | ![跳棋旋转图标](./media/image-templates/checker-rotated.png) | ![圆圈图标](./media/image-templates/circles.png) | ![圆间距图标](./media/image-templates/circles-spaced.png) |
|||||
| 对角线 | 对角线向下 | 对角条纹向上 | 对角条纹向下 |
| ![对角线排列图标](./media/image-templates/diagonal-lines-up.png) | ![对角线向下图标](./media/image-templates/diagonal-lines-down.png) | ![对角条纹向上图标](./media/image-templates/diagonal-stripes-up.png) | ![对角条纹向下图标](./media/image-templates/diagonal-stripes-down.png) |
|||||
| 网格线 | 旋转网格线 | 旋转网格条纹 | x 填充 |
| ![网格线图标](./media/image-templates/grid-lines.png) | ![旋转网格线图标](./media/image-templates/rotated-grid-lines.png) | ![旋转网格条纹图标](./media/image-templates/rotated-grid-stripes.png) | ![x 填充图标](./media/image-templates/x-fill.png) |
|||||
| 锯齿形 | 锯齿形垂直 | 点 |  |
| ![锯齿形图标](./media/image-templates/zig-zag.png) | ![锯齿形垂直图标](./media/image-templates/zig-zag-vertical.png) | ![点图标](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>立即试用工具

使用以下工具，您可以通过多种方式呈现不同的内置图像模板，并自定义主颜色和次要颜色和比例。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="图标模板选项" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看笔<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>图标模板选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [图像 Sprite 管理器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [地图集命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

有关可以使用图像模板的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号图层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
