---
title: Azure Maps Web SDK 中的图像模板 |Microsoft Docs
description: 如何在 Azure Maps Web SDK 中使用映像模板。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: b9b1543ca37c636f4a82ff9ada3dfe212fa9b8d0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976670"
---
# <a name="how-to-use-image-templates"></a>如何使用图像模板

可以在 Azure Maps web SDK 中使用 HTML 标记和各种层:

 - 符号层可使用图像图标呈现地图上的点。 还可以沿着行路径呈现符号。
 - 可以使用填充模式图像呈现多边形层。 
 - HTML 标记可以使用图像和其他 HTML 元素呈现点。

为了确保层具有良好的性能, 在呈现之前, 需要将这些图像加载到地图图像 sprite 资源中。 默认情况下, [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)会将几个颜色的标记图像预加载到地图图像 sprite。 这些相同的标记图像和其他图像作为 SVG 模板提供, 可用于创建具有自定义比例的图像, 以及客户的主要颜色和辅助颜色。 总共提供了42个映像模板;27个符号图标和15个多边形填充模式。

可以使用`map.imageSprite.createFromTemplate`函数将图像模板添加到地图图像 sprite 资源。 此函数允许传入多达五个参数;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

其中, `id`是你创建的唯一标识符, 在将其添加到地图图像动画层时, 会将其分配给映像。 在层中使用此标识符来指定要呈现的图像资源。 `templateName`指定要使用的图像模板。 选项设置图像的主要颜色`secondaryColor` , 选项设置图像的辅助颜色。 `color` `scale`选项会在将图像模板应用于图像子画面之前对其进行缩放。 当图像应用于图像子画面时, 它将转换为 PNG。 若要确保清晰渲染, 最好在将图像模板添加到子画面之前先对其进行缩放, 然后再将其添加到子画面。

此函数以异步方式将图像加载到图像 sprite, 因而返回可等待此函数完成的承诺。

下面的代码演示如何创建一个内置模板的图像, 并将其用于符号层。

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>使用带有符号层的图像模板

将图像模板加载到地图图像 sprite 后, 可通过在的`image`选项`iconOptions`中引用图像资源 ID, 将其呈现为符号层中的符号。

下面的示例使用带有蓝绿色主色`marker-flat`和白色辅助颜色的图像模板呈现符号层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的符号层" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>带有内置图标的笔符号层</a>"<a href='https://codepen.io/azuremaps'>@azuremaps</a>模板 Azure Maps ()。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>使用沿行路径的图像模板

将图像模板加载到地图图像 sprite 后, 可以通过将 LineString 添加到数据源, 并使用带`lineSpacing`选项的符号层并`image`在选项 o 中引用图像资源的 ID, 将其沿行的路径呈现第`iconOptions`f 个。 

下面的示例在地图上呈现粉红色的线条, 并使用带有宝蓝蓝原色`car`和白色辅助颜色的图像模板的符号层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的线条层" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>带有内置图标模板</a>的笔线层<a href='https://codepen.io/azuremaps'>@azuremaps</a>" Azure Maps ()。
</iframe>

> [!TIP]
> 如果图像模板指向上方, 则将符号`rotation`层的图标选项设置为 90 (如果你想要将其指向与直线相同的方向。

## <a name="use-an-image-template-with-a-polygon-layer"></a>使用带有多边形层的图像模板

将图像模板加载到地图图像下边缘后, 可通过在该图层的`fillPattern`选项中引用图像资源 ID, 将其呈现为多边形层中的填充模式。

下面的示例使用带有红色主颜色和`dot`透明辅助颜色的图像模板呈现多边形层。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="用内置图标模板填充多边形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>使用内置图标模板的笔填充多边形</a>Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>()。
</iframe>

> [!TIP]
> 通过设置填充模式的辅助颜色, 可以更轻松地看到基础地图仍将提供主模式。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用带有 HTML 标记的图像模板

可以使用`altas.getImageTemplate`函数检索图像模板, 并将其用作 HTML 标记的内容。 模板`htmlContent`可传递到标记的选项, 然后`color`使用、 `secondaryColor`和`text`选项自定义。

下面的示例使用具有`marker-arrow`红色主色、粉红色辅助颜色和文本值 "00" 的模板。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的 HTML 标记" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>使用内置图标模板的笔 HTML 标记</a>Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>()。
</iframe>

## <a name="create-custom-reusable-templates"></a>创建自定义的可重用模板

如果你的应用程序使用具有不同图标的相同图标, 或者如果你要创建添加其他图像模板的模块, 则可以使用以下静态函数在`atlas` Azure Maps web SDK 中轻松添加和检索这些图标:名称.

| 姓名 | 返回类型 | 描述 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 向塔命名空间添加自定义 SVG 图像模板。 |
|  `getImageTemplate(templateName: string, scale?: number)`| string | 按名称检索 SVG 模板。 |
| `getAllImageTemplateNames()` | string[] |  按名称检索 SVG 模板。 |

SVG 图像模板支持以下占位符值:

| 占位符 | 描述 |
|-|-|
| `{color}` | 主要颜色。 | 
| `{secondaryColor}` | 辅助颜色。 | 
| `{scale}` | 将 SVG 图像添加到地图图像动画层后, 会将其转换为 png 图像。 此占位符可用于在转换模板之前对其进行缩放, 确保它清晰呈现。 | 
| `{text}` | 与 HTML 标记一起使用时呈现文本的位置。 |

下面的示例演示如何获取 SVG 模板并将其作为可重用的图标模板添加到 Azure Maps web SDK。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="将自定义图标模板添加到阿特拉斯命名空间" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>将自定义图标模板添加到阿特拉斯命名空间</a>"。
</iframe>

## <a name="list-of-image-templates"></a>映像模板列表

下表列出了 Azure Maps web SDK 中当前可用的所有映像模板, 每个图像上方有模板名称。 默认情况下, 主要颜色为蓝色, 辅助颜色为白色。 为了使辅助颜色在白色背景上更易于查看, 以下图像将辅助颜色设置为黑色。

**符号图标模板**

|||||
|:-:|:-:|:-:|:-:|
| ' | 标记-厚 | 标记-圆圈 | 标记-平 |
|![标记图标](./media/image-templates/marker.png)|![标记-厚图标](./media/image-templates/marker-thick.png)|![标记-圆圈图标](./media/image-templates/marker-circle.png)|![标记-平面图标](./media/image-templates/marker-flat.png)|
||||
| 标记-正方形 | 标记-方形-分类 | 标记-箭头 | 标记球-pin | 
|![标记-正方形图标](./media/image-templates/marker-square.png)|![标记-方形-群集图标](./media/image-templates/marker-square-cluster.png)|![标记-箭头图标](./media/image-templates/marker-arrow.png)|![标记球图标](./media/image-templates/marker-ball-pin.png)|
||||
| 标记-圆形 | 标记-方形舍入-分类 | 标志 | 标记-三角形 |
| ![标记-圆形图标](./media/image-templates/marker-square-rounded.png) | ![标记-方形舍入-分类图标](./media/image-templates/marker-square-rounded-cluster.png) | ![标志图标](./media/image-templates/flag.png) | ![标记-三角形图标](./media/image-templates/flag-triangle.png) |
||||
| 三角 | 三角形-粗 | 三角形-向上箭头 | 三角形-向左箭头 |
| ![三角形图标](./media/image-templates/triangle.png) | ![三角形-厚图标](./media/image-templates/triangle-thick.png) | ![三角形-向上箭头图标](./media/image-templates/triangle-arrow-up.png) | ![三角形-向左箭头图标](./media/image-templates/triangle-arrow-left.png) |
||||
| 六边形 | 六边形-粗 | 六边形-圆形 | 六边形-粗 |
| ![六边形图标](./media/image-templates/hexagon.png) | ![六边形-厚图标](./media/image-templates/hexagon-thick.png) | ![六边形圆形图标](./media/image-templates/hexagon-rounded.png) | ![六边形-粗图标](./media/image-templates/hexagon-rounded-thick.png) |
||||
| 固定 | 固定 | 圆角正方形 | 圆方形-粗 |
| ![固定图标](./media/image-templates/pin.png) | ![固定线图标](./media/image-templates/pin-round.png) | ![圆角图标](./media/image-templates/rounded-square.png) | ![圆角方形-粗图标](./media/image-templates/rounded-square-thick.png) |
||||
| 向上箭头 | 向上箭头-细 | 汽车 ||
| ![向上箭头图标](./media/image-templates/arrow-up.png) | ![向上箭头-细图标](./media/image-templates/arrow-up-thin.png) | ![汽车图标](./media/image-templates/car.png) | |

**多边形填充图案模板**

|||||
|:-:|:-:|:-:|:-:|
| 检查 | 检查器-旋转 | 小号 | 圆圈-间距 |
| ![检查图标](./media/image-templates/checker.png) | ![检查器-旋转图标](./media/image-templates/checker-rotated.png) | ![圆圈图标](./media/image-templates/circles.png) | ![圆圈-间距图标](./media/image-templates/circles-spaced.png) |
|||||
| 斜上框线 | 对角线-向下移动 | 对角-条向上 | 对角线-向下移动 |
| ![斜上框线图标](./media/image-templates/diagonal-lines-up.png) | ![斜向下箭头图标](./media/image-templates/diagonal-lines-down.png) | ![对角-条带图标](./media/image-templates/diagonal-stripes-up.png) | ![对角线-向下移动图标](./media/image-templates/diagonal-stripes-down.png) |
|||||
| 网格线 | 旋转网格线 | 旋转网格-条纹 | x 填充 |
| ![网格线图标](./media/image-templates/grid-lines.png) | ![旋转网格线图标](./media/image-templates/rotated-grid-lines.png) | ![旋转网格线图标](./media/image-templates/rotated-grid-stripes.png) | ![x 填充图标](./media/image-templates/x-fill.png) |
|||||
| 之-字形 | 之-字形-垂直 | 点数 |  |
| ![之-字形图标](./media/image-templates/zig-zag.png) | ![之-字形图标](./media/image-templates/zig-zag-vertical.png) | ![点图标](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>立即试用工具

通过以下工具, 你可以通过多种方式呈现不同的内置图像模板, 并自定义主要和次要的颜色和缩放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="图标模板选项" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 查看笔<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>图标模板选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [阿特拉斯命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

请参阅以下文章, 了解可以使用图像模板的更多代码示例:

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
