---
title: 使用 Azure Maps 创建辅助性地图应用程序 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure Maps 构建具有辅助功能的应用程序。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 2ae84b59cd70a5b27ad3e501db6cfae110d90fbd
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209777"
---
# <a name="building-an-accessible-application"></a>构建可访问的应用程序

超过20% 的 internet 用户需要访问 web 应用程序。 因此，请务必确保应用程序的设计使任何用户都可以轻松使用它。 将其视为总体用户体验的一部分，而不是将可访问性视为一组任务。 应用程序的可访问性越多，使用该应用程序的人员越多。 

当涉及到类似地图的丰富交互式内容时，一些常见的辅助功能注意事项如下：
- 支持对查看 web 应用程序有困难的用户使用屏幕阅读器。
- 有多种方法可与 web 应用程序（如鼠标、触摸和键盘）交互和导航。
- 确保颜色对比度，使颜色不会混合在一起，并且难以区分。 

Azure Maps Web SDK 预建了许多辅助功能，例如：
- 当地图移动时以及当用户关注控件或弹出项时，屏幕阅读器的说明。
- 鼠标、触摸和键盘支持。
- 道路地图样式中可访问的颜色对比度支持。

可在[此处](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)找到所有 Microsoft 产品的完整辅助功能一致性详细信息。 搜索 "Azure Maps web"，查找专用于 Azure Maps Web SDK 的文档。 

## <a name="navigating-the-map"></a>导航地图
可以通过多种不同的方式来缩放、平移、旋转和音调地图。 下面详细介绍了导航地图的所有不同方式。

**缩放地图**
- 使用鼠标双击地图，在一个级别中进行缩放。
- 使用鼠标滚动滚轮来缩放地图。
- 使用触摸屏，用两根手指触摸地图，同时缩小，使手指缩小，使手指缩小。
- 使用触摸屏，双击地图以在一个级别中进行缩放。
- 以地图为重点，使用加号（`+`）或 * 等号（`=`）在一个级别中进行缩放。
- 以地图为重点，使用减号、连字符（`-`）或下划线（`_`）缩小一个级别。
- 使用 "缩放" 控件，用鼠标、触摸或键盘 tab/enter 键。
- 按住 "`Shift`" 按钮，然后在地图上按下鼠标左键，并拖动鼠标以绘制一个区域，将地图放大到其中。

**平移地图**
- 使用鼠标在地图上按下鼠标左键，并向任意方向拖动。
- 使用触摸屏，按下地图并向任意方向拖动。
- 在地图上，使用箭头键移动地图。

**旋转地图**
- 使用鼠标在地图上按下鼠标右键，并向左或向右拖动。 
- 使用触摸屏，用两根手指和旋转来触摸地图。
- 以地图为重点，使用 shift 键和向左键或向右键。
- 通过鼠标、触摸或键盘 tab/enter 键使用旋转控件。

**螺距地图**
- 使用鼠标在地图上按下鼠标右键，然后向上或向下拖动。 
- 使用触摸屏，用两根手指触摸地图，并将它们向上或向下拖动。
- 将地图集中在一起后，请使用 shift 键和向上键或向下键。 
- 通过鼠标、触摸或键盘 tab/enter 键使用螺距控件。

**更改地图样式**并非所有开发人员都希望在应用程序中提供所有可能的地图样式。 开发人员可以通过编程方式设置和更改地图样式。 如果开发人员显示了地图的样式选取器控件，则用户可以使用鼠标、触控或使用 tab 或 enter 键的键盘来更改地图样式。 开发人员可以在地图样式选取器控件中指定要提供的地图样式。 

## <a name="keyboard-shortcuts"></a>键盘快捷方式

地图中内置了多个键盘快捷键，可以更轻松地使用地图。 当地图有焦点时，这些键盘快捷方式将起作用。

| 密钥      | 操作                            |
|----------|-----------------------------------|
| `Tab` | 在映射中的控件和弹出窗口间导航。 |
| `ESC` | 将焦点从映射中的任何元素移到顶级地图元素。 |
| `Ctrl` + `Shift` + `D` | 切换屏幕读取器详细信息级别。  |
| 向左键 | 将地图平移左平移100像素 |
| 向右键 | 将地图向右平移100像素 |
| 下箭头键 | 将地图向下平移100像素 |
| 向上键 | 将地图向上平移100像素 |
| `Shift` + 向上键 | 将地图螺距增加10度 |
| `Shift` + 向下键 | 将地图间距减小10度 |
| `Shift` + 向右键 | 将地图顺时针旋转15度 |
| `Shift` + 向左键 | 逆时针旋转图15度 |
| 加号（`+`）或<sup>*</sup>等号（`=`） | 放大 |
| 减号、连字号（`-`）或<sup>*</sup>下划线（`_`） | 缩小 | 
| `Shift` 鼠标在地图上拖动到绘图区域 | 缩小到区域 |

<sup>*</sup>这些键快捷方式通常在键盘上共享相同的键。 添加这些快捷方式是为了改进用户体验。 如果用户对这些快捷方式使用 shift 键，这也无关紧要。

## <a name="screen-reader-support"></a>屏幕阅读器支持

用户可以使用键盘导航地图。 如果屏幕阅读器正在运行，地图会将其状态的更改通知用户。 例如，在平移或缩放地图时，系统将地图的更改告知用户。 默认情况下，该地图提供简化的说明，排除了地图中心的缩放级别和坐标。 用户可以使用键盘简短剪切 `Ctrl` + `Shift` + `D`来切换这些说明的详细级别。

基本地图上的任何附加信息应具有适用于屏幕阅读器用户的相应文本信息。 请确保在适当的位置添加[可访问的丰富 Internet 应用程序（ARIA）](https://www.w3.org/WAI/standards-guidelines/aria/)、alt 和 title 特性。 

## <a name="make-popups-keyboard-accessible"></a>使弹出键盘可访问

标记或符号通常用于表示地图上的位置。 用户与标记进行交互时，有关该位置的其他信息通常显示在弹出窗口中。 在大多数应用程序中，当用户单击或点击标记时，将显示弹出窗口。 然而，单击和点击要求用户分别使用鼠标和触摸屏。 一种很好的做法是在使用键盘时使弹出窗口可访问。 可以通过为每个数据点创建一个弹出窗口并将其添加到地图来实现此功能。 

下面的示例使用符号层加载地图上的兴趣点，并为每个相关点向地图添加一个弹出框。 每个弹出项的引用都存储在每个数据点的属性中。 还可以为标记（如单击标记时）检索它。 当焦点位于地图上时，按 tab 键可允许用户单步执行地图上的每个弹出窗口。

<br/>

<iframe height='500' scrolling='no' title='构建可访问的应用程序' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io'>构建可访问的应用程序</a>。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他辅助功能提示

下面是一些其他提示，使 web 映射应用程序更易于访问。

- 如果在地图上显示多个交互点数据，请考虑减少混乱并使用聚类分析。 
- 确保文本/符号和背景色之间的颜色对比度为4.5：1或更大。
- 使屏幕阅读器（ARIA、alt 和 title 特性）消息简短、描述性和有意义。 避免不必要的术语和缩写。
- 尝试优化发送到屏幕阅读器的消息，为用户提供简单的简短信息，使用户能够轻松地进行摘要。 例如，如果要以高频率（例如，地图移动时）来更新屏幕阅读器，请考虑执行以下几点：
    - 请等待映射完成后再更新屏幕阅读器。
    - 将更新限制为每隔几秒钟一次。 
    - 以逻辑方式将消息组合在一起。 
- 避免使用颜色作为传达信息的唯一方法。 使用文本、图标或模式来补充或替换颜色。 一些注意事项：
    - 如果使用气泡图层来显示数据点之间的相对值，请考虑缩放每个气泡的半径，为气泡着色，或同时为两者着色。 
    - 对于不同的指标类别（如三角形、星形和正方形），请考虑使用带有不同图标的符号层。 符号层还支持缩放图标大小。 还可以显示文本标签。
    - 如果显示行数据，则可使用宽度来表示权重或大小。 短线数组模式可用于表示不同类别的行。 可以结合使用符号层和线条来叠加线条上的图标。 使用箭头图标对于显示直线的流或方向非常有用。
    - 如果显示多边形数据，则可以使用一种模式（如条纹）作为颜色的替代方法。 
- 对于视力障碍的用户，某些可视化对象（如热图、图块层和图像层）无法访问。 一些注意事项：
    - 让屏幕阅读器说明该层添加到地图时所显示的内容。 例如，如果显示 "天气雷达" 图块层，则让屏幕阅读器显示 "天气雷达图数据在地图上重叠"。
- 限制需要鼠标悬停的功能量。 使用键盘或触摸设备与应用程序进行交互的用户将无法访问这些功能。 请注意，对于交互式内容（如可单击图标、链接和按钮），可以使用悬停样式。
- 尝试使用键盘浏览应用程序。 请确保 tab 键顺序为逻辑。
- 如果创建键盘快捷方式，请尝试将其限制为两个或更少的键。 

## <a name="next-steps"></a>后续步骤

了解 Web SDK 模块中的可访问性。

> [!div class="nextstepaction"]
> [绘图工具辅助功能](drawing-tools-interactions-keyboard-shortcuts.md)

了解如何通过 Microsoft Learn 开发辅助性应用程序：

> [!div class="nextstepaction"]
> [操作中的可访问性数字徽章学习路径](https://ready.azurewebsites.net/learning/track/2940)

请看一下这些有用的辅助功能工具：
> [!div class="nextstepaction"]
> [开发辅助性应用](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 概述](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 辅助功能评估工具（WAVE）](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 色对比度检查器](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [无咖啡愿景模拟器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
