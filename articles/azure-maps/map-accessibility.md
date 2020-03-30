---
title: 使用 Azure 地图创建可访问的地图应用程序 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 映射构建具有辅助功能的应用程序。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473313"
---
# <a name="building-an-accessible-application"></a>构建可访问的应用程序

超过 20% 的互联网用户需要可访问的 Web 应用程序。 因此，请务必确保应用程序的设计使任何用户都可以轻松使用它。 与其将辅助功能视为要完成的任务集，不如将其视为整体用户体验的一部分。 应用程序越可访问，可以使用该应用程序的人就越多。 

当涉及到丰富的交互式内容（如地图）时，一些常见的辅助功能注意事项包括：
- 为难以查看 Web 应用程序的用户支持屏幕阅读器。
- 具有多种方法与 Web 应用程序（如鼠标、触摸和键盘）进行交互和导航。
- 确保颜色对比度使颜色不会混合在一起，并且变得难以区分。 

Azure 地图 Web SDK 预构建了许多辅助功能，例如：
- 当地图移动以及用户将焦点放在控件或弹出窗口上时，屏幕阅读器说明。
- 鼠标、触摸和键盘支持。
- 路线图样式中可访问的颜色对比度支持。

所有 Microsoft 产品的完整辅助功能符合性详细信息可[在此处](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)找到。 搜索"Azure 地图 Web"以查找专门针对 Azure 地图 Web SDK 的文档。 

## <a name="navigating-the-map"></a>导航地图

地图可以缩放、平移、旋转和倾斜几种不同的方式。 下面详细介绍了导航地图的所有不同方法。

**缩放地图**

- 使用鼠标，双击地图以放大一个级别。
- 使用鼠标滚动滚轮以缩放地图。
- 使用触摸屏，用两根手指触摸地图，然后捏在一起缩小或将手指分开以放大。
- 使用触摸屏，双击地图可放大一个级别。
- 将地图聚焦后，使用 Plus 符号`+`（ ） 或`=`等于符号 （ ） 放大一个级别。
- 将地图聚焦后，使用减号、连字符 （）`-`或下`_`划线 （ ） 缩小一个级别。
- 使用带有鼠标、触摸或键盘选项卡/输入键的缩放控件。
- 按住按钮`Shift`，按下地图上的鼠标左键，然后拖动以绘制区域以放大地图。

**平移地图**

- 使用鼠标，按下地图上的鼠标左键，然后朝任意方向拖动。
- 使用触摸屏，触摸地图并朝任意方向拖动。
- 将地图聚焦后，使用箭头键移动地图。

**旋转地图**

- 使用鼠标，按下地图上的鼠标右键，向左或向右拖动。 
- 使用触摸屏，用两根手指触摸地图并旋转。
- 将地图聚焦时，请使用移位键和左箭头或右箭头键。
- 使用带有鼠标、触摸或键盘选项卡/输入键的旋转控件。

**间距地图**

- 使用鼠标，按下地图上的鼠标右键，然后向上或向下拖动。 
- 使用触摸屏，用两根手指触摸地图，然后一起向上或向下拖动。
- 将地图聚焦时，请使用移位键加上向上或向下箭头键。 
- 使用带有鼠标、触摸或键盘选项卡/输入键的间距控件。

## <a name="change-the-map-style"></a>更改地图样式

并非所有开发人员都希望所有可能的地图样式在其应用程序中可用。 如果开发人员显示地图的样式选取器控件，则用户可以使用鼠标、触摸或带有选项卡的键盘更改地图样式或输入键。 开发人员可以指定他们希望在地图样式选取器控件中提供的地图样式。 此外，开发人员可以以编程方式设置和更改地图样式。

**使用高对比度**

- 加载地图控件时，它会检查是否启用了高对比度，并且浏览器支持它。
- 地图控件不监视设备的高对比度模式。 如果设备模式发生更改，则映射不会更改。 因此，用户需要通过刷新页面来重新加载地图。
- 检测到高对比度时，地图样式将自动切换到高对比度，并且所有内置控件都将使用高对比度样式。 例如，缩放控制、间距控制、指南针控制、样式控制和其他内置控件将使用高对比度样式。
- 有两种高对比度，即光和暗。 如果地图控件可以检测到高对比度类型，则地图的行为将相应地调整。 如果光线，则将加载grayscale_light地图样式。 如果无法检测到类型或为深色，则将加载high_contrast_dark样式。
- 如果创建自定义控件，则了解内置控件是否使用高对比度样式非常有用。 开发人员可以在地图容器 div 上添加 css 类来检查。 要添加的 css 类是`high-contrast-dark``high-contrast-light`和 。 要检查使用 JavaScript，请使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

或者，使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>键盘快捷方式

地图内置了许多键盘快捷键，便于使用地图。 当地图具有焦点时，这些键盘快捷键工作。

| 键      | 操作                            |
|----------|-----------------------------------|
| `Tab` | 在地图中的控件和弹出窗口中导航。 |
| `ESC` | 将焦点从地图中的任何元素移动到顶级地图元素。 |
| `Ctrl` + `Shift` + `D` | 切换屏幕阅读器详细信息级别。  |
| 向左键 | 平移地图左侧 100 像素 |
| 向右键 | 向右平移地图 100 像素 |
| 下箭头键 | 将地图向下平移 100 像素 |
| 向上键 | 将地图向上平移 100 像素 |
| `Shift`• 向上箭头 | 将地图间距提高 10 度 |
| `Shift`• 向下箭头 | 将地图间距降低 10 度 |
| `Shift`• 右箭头 | 顺时针旋转地图 15 度 |
| `Shift`• 左箭头 | 逆时针旋转地图 15 度 |
| 加号`+`（ <sup>*</sup>） 或等于`=`符号 （ ） | 放大 |
| 减号、连字符`-`（ <sup>*</sup>）`_`或下划线 （ ） | 缩小 | 
| `Shift`• 鼠标在地图上拖动以绘制区域 | 放大缩小字体功能 放大缩小字体功能 |

<sup>*</sup>这些键快捷键通常在键盘上共享同一个键。 添加了这些快捷方式是为了改善用户体验。 用户是否对这些快捷方式使用移位键也无关紧要。

## <a name="screen-reader-support"></a>屏幕阅读器支持

用户可以使用键盘导航地图。 如果屏幕阅读器正在运行，地图会将其状态的更改通知用户。 例如，在平移或缩放地图时，系统将地图的更改告知用户。 默认情况下，地图提供排除地图中心的缩放级别和坐标的简化说明。 `Ctrl` + `Shift` + `D`用户可以使用键盘短截头切换这些描述的细节级别。

基本地图上的任何附加信息应具有适用于屏幕阅读器用户的相应文本信息。 请务必在适当的情况下添加[可访问的富 Internet 应用程序 （ARIA）](https://www.w3.org/WAI/standards-guidelines/aria/)、alt 和标题属性。 

## <a name="make-popups-keyboard-accessible"></a>使弹出窗口键盘可访问

标记或符号通常用于表示地图上的位置。 当用户与标记交互时，有关该位置的其他信息通常显示在弹出窗口中。 在大多数应用程序中，当用户单击或点击标记时会出现弹出窗口。 但是，单击和点击需要用户分别使用鼠标和触摸屏。 一个好的做法是使弹出窗口在使用键盘时可访问。 此功能可以通过为每个数据点创建一个弹出窗口并将其添加到地图来实现。 

下面的示例使用符号图层加载地图上的兴趣点，并为每个兴趣点的地图添加一个弹出窗口。 每个弹出窗口的引用存储在每个数据点的属性中。 也可以检索标记，例如单击标记时。 聚焦在地图上时，按下选项卡键将允许用户单步执行地图上的每个弹出窗口。

<br/>

<iframe height='500' scrolling='no' title='构建可访问的应用程序' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>构建可访问的应用程序</a>。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他辅助功能提示

下面是一些额外的提示，使 Web 映射应用程序更易于访问。

- 如果在地图上显示许多交互式点数据，请考虑减少杂乱并使用聚类。 
- 确保文本/符号和背景颜色之间的颜色对比度为 4.5：1 或更多。
- 使屏幕阅读器（ARIA、alt 和标题属性）消息保持简短、描述性和有意义性。 避免不必要的行话和首字母缩略词。
- 尝试优化发送给屏幕阅读器的邮件，以提供用户易于消化的短而有意义的信息。 例如，如果要以高频率更新屏幕阅读器（例如，当地图移动时），请考虑执行以下几点：
    - 等待地图完成移动以更新屏幕阅读器。
    - 将更新限制到每几秒钟一次。 
    - 以逻辑方式将消息组合在一起。 
- 避免使用颜色作为传达信息的唯一方法。 使用文本、图标或图案来补充或替换颜色。 一些注意事项：
    - 如果使用气泡图层来显示数据点之间的相对值，请考虑缩放每个气泡的半径、为气泡着色或两者着色。 
    - 请考虑对不同的指标类别（如三角形、星形和正方形）使用具有不同图标的符号图层。 符号图层还支持缩放图标的大小。 也可以显示文本标签。
    - 如果显示线数据，则宽度可用于表示权重或大小。 短划线数组模式可用于表示不同类别的行。 符号图层可与线结合使用，以覆盖沿线的图标。 使用箭头图标可用于显示线条的流或方向。
    - 如果显示多边形数据，则模式（如条纹）可用作颜色的替代方法。 
- 某些可视化效果（如热图、切片图层和图像图层）对于视力障碍的用户是无法访问的。 一些注意事项：
    - 让屏幕阅读器描述图层添加到地图时显示的内容。 例如，如果显示天气雷达切片图层，则让屏幕阅读器说"天气雷达数据叠加在地图上"。
- 限制需要鼠标悬停的功能量。 使用键盘或触摸设备与应用程序交互的用户将无法访问这些功能。 请注意，对于交互式内容（如可单击的图标、链接和按钮），最好采用悬停样式。
- 请尝试使用键盘导航应用程序。 确保选项卡排序是合乎逻辑的。
- 如果创建键盘快捷键，请尝试将其限制为两个或更少的键。 

## <a name="next-steps"></a>后续步骤

了解 Web SDK 模块中的可访问性。

> [!div class="nextstepaction"]
> [绘图工具可访问性](drawing-tools-interactions-keyboard-shortcuts.md)

了解如何使用 Microsoft 学习开发可访问的应用程序：

> [!div class="nextstepaction"]
> [操作中的辅助功能 数字徽章学习路径](https://ready.azurewebsites.net/learning/track/2940)

查看这些有用的辅助功能工具：
> [!div class="nextstepaction"]
> [开发可访问的应用程序](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 概述](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 辅助功能评估工具 （WAVE）](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 颜色对比检查器](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [无咖啡视觉模拟器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
