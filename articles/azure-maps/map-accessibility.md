---
title: 使用 Azure Maps 创建易访问的地图应用程序 | Microsoft Azure Maps
description: 了解 Azure Maps 中的辅助功能注意事项。 查看可访问地图应用程序的功能，并查看辅助功能提示。
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88006207"
---
# <a name="building-an-accessible-application"></a>生成可访问的应用程序

超过 20% 的 Internet 用户需要访问 Web 应用程序。 因此，请务必确保应用程序的设计能使任何用户都可以轻松使用它。 不要将辅助功能视为要完成的一组任务，而要将其视为总体用户体验的一部分。 应用程序越容易访问，使用它的人就越多。 

涉及到地图等丰富交互式内容时，一些常见的辅助功能考虑因素如下：
- 对难以查看 Web 应用程序的用户提供屏幕阅读器支持。
- 提供多种方法与 Web 应用程序交互以及在其中导航，例如，支持鼠标、触屏和键盘。
- 确保颜色对比度，使颜色不会混合在一起，并且难以区分。 

Azure Maps Web SDK 中随附了许多预生成的辅助功能，例如：
- 当地图移动以及当用户聚焦于某个控件或弹出窗口时显示屏幕阅读器说明。
- 鼠标、触屏和键盘支持。
- 以路线图样式提供易访问的颜色对比度支持。
- 高对比度支持。

可在[此处](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)找到所有 Microsoft 产品的完整辅助功能遵从性详细信息。 搜索“Azure Maps web”，找到 Azure Maps Web SDK 的专门文档。 

## <a name="navigating-the-map"></a>在地图中导航

可通过多种不同的方式缩放、平移、旋转地图及调整其俯仰角。 下面详细说明了在地图中导航的所有不同方式。

**缩放地图**

- 使用鼠标双击地图可放大一个级别。
- 滚动鼠标滚轮可以缩放地图。
- 在触摸屏上使用双指触摸地图，收拢双指可以缩小，分开双指可以放大。
- 在触摸屏上双击地图可以放大一个级别。
- 聚焦到地图后，使用加号 (`+`) 或等号 (`=`) 可以放大一个级别。
- 聚焦到地图后，使用减号、连字符 (`-`) 或下划线 (`_`) 可以缩小一个级别。
- 配合鼠标、触屏或键盘 Tab/Enter 键使用缩放控件。
- 在按住 `Shift` 按钮的同时，在地图上按下鼠标左键并拖动可以绘制要放大的地图区域。
- 使用一些多点触摸板，向上拖动两个手指以缩小或缩小以放大。

**平移地图**

- 在地图上按下鼠标左键并朝任意方向拖动。
- 在触摸屏上触摸地图并朝任意方向拖动。
- 聚焦到地图后，使用箭头键移动地图。

**旋转地图**

- 在地图上按下鼠标右键并向左或向右拖动。 
- 在触摸屏上使用双指触摸地图并旋转。
- 聚焦到地图，使用 Shift 键和向左或向右箭头键。
- 配合鼠标、触屏或键盘 Tab/Enter 键使用旋转控件。

**调整地图俯仰角**

- 在地图上按下鼠标右键并向上或向下拖动。 
- 在触摸屏上使用双指触摸地图，并同时向上或向下拖动双指。
- 聚焦到地图后，使用 Shift 键加向上或向下箭头键。 
- 配合鼠标、触屏或键盘 Tab/Enter 键使用俯仰角调整控件。

## <a name="change-the-map-style"></a>更改地图样式

并非所有开发人员都希望在其应用程序中提供所有可能的地图样式。 如果开发人员显示了地图的样式选取器控件，则用户可以使用鼠标、触屏或键盘 Tab 或 Enter 键来更改地图样式。 开发人员可以指定他们想要在地图样式选取器控件中提供的地图样式。 此外，开发人员能够以编程方式设置和更改地图样式。

**使用高对比度**

- 加载地图控件后，它会检查是否已启用高对比度，以及浏览器是否支持高对比度。
- 地图控件不会监视设备的高对比度模式。 地图不会随着设备模式的更改而更改。 因此，用户需要通过刷新页面来重新加载地图。
- 检测到高对比度时，地图样式会自动切换到高对比度，所有内置控件将使用高对比度样式。 例如，ZoomControl、PitchControl、CompassControl、StyleControl 和其他内置控件将使用高对比度样式。
- 有两种类型的高对比度：浅色和深色。 如果地图控件可以检测到高对比度类型，则地图的行为将会相应地调整。 如果是浅色类型，则加载 grayscale_light 地图样式。 如果无法检测到该类型或该类型为深色，则将加载 high_contrast_dark 样式。
- 创建自定义控件有助于了解内置控件是否使用高对比度样式。 开发人员可以在地图容器 div 中添加一个用于检查的 css 类。 要添加的 css 类为 `high-contrast-dark` 和 `high-contrast-light`。 若要使用 JavaScript 进行检查，请使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

或者使用：

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>键盘快捷键

地图中内置了多个键盘快捷键，可以方便你使用地图。 聚焦到地图时，可以使用这些键盘快捷键。

| 密钥      | 操作                            |
|----------|-----------------------------------|
| `Tab` | 在地图中的控件和弹出窗口之间导航。 |
| `ESC` | 将焦点从地图中的任一元素转移到顶级地图元素。 |
| `Ctrl` + `Shift` + `D` | 切换屏幕阅读器详细程度。  |
| 向左键 | 将地图向左平移 100 像素 |
| 向右键 | 将地图向右平移 100 像素 |
| 下箭头键 | 将地图向下平移 100 像素 |
| 向上键 | 将地图向上平移 100 像素 |
| `Shift` + 向上箭头 | 将地图俯仰角增大 10 度 |
| `Shift` + 向下箭头 | 将地图俯仰角减小 10 度 |
| `Shift` + 向右箭头 | 将地图顺时针旋转 15 度 |
| `Shift` + 向左箭头 | 将地图逆时针旋转 15 度 |
| 加号 (`+`) 或 <sup>*</sup>等号 (`=`) | 放大 |
| 减号、连字号 (`-`) 或 <sup>*</sup>下划线 (`_`) | 缩小 | 
| `Shift` + 在地图上拖动鼠标以绘制区域 | 放大区域 |

<sup>*</sup> 这些快捷键通常共享键盘上的同一键。 添加这些快捷键的目的是改进用户体验。 如果用户对这些快捷方式使用 shift 键，这也无关紧要。

## <a name="screen-reader-support"></a>屏幕阅读器支持

用户可以使用键盘导航地图。 如果屏幕阅读器正在运行，地图会将其状态的更改通知用户。 例如，在平移或缩放地图时，系统将地图的更改告知用户。 默认情况下，地图提供简化的说明，其中不包括缩放级别以及地图中心坐标。 用户可以使用键盘快捷键 `Ctrl` + `Shift` + `D` 来切换这些说明的详细程度。

基本地图上的任何附加信息应具有适用于屏幕阅读器用户的相应文本信息。 在适用的情况下，请务必添加[易访问的丰富 Internet 应用程序 (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/)、alt 和 title 特性。 

## <a name="make-popups-keyboard-accessible"></a>使弹出窗口可通过键盘访问

通常使用标记或符号来表示地图上的某个位置。 当用户与标记交互时，通常会在一个弹出窗口中显示有关该位置的附加信息。 在大多数应用程序中，当用户单击或点击标记时会显示弹出窗口。 但是，单击和点击操作分别需要用户使用鼠标和触摸屏。 好的做法是使弹出窗口可通过键盘访问。 可以通过为每个数据点创建一个弹出窗口并将此弹出窗口添加到地图中，来实现此功能。 

以下示例使用符号层在地图上加载兴趣点，并在地图中为每个兴趣点添加一个弹出窗口。 对每个弹出窗口的引用存储在每个数据点的属性中。 还可以检索对标记的引用，例如，在单击某个标记时这样做。 聚焦到地图后，用户可以按 Tab 键切换地图上的每个弹出窗口。

<br/>

<iframe height='500' scrolling='no' title='构建可访问的应用程序' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>构建可访问的应用程序</a>。 </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>其他辅助功能提示

下面是使 Web 地图应用程序更易于访问的其他一些提示。

- 如果要在地图上显示许多的交互点数据，请考虑减少混杂情况并使用聚类。 
- 确保文本/符号与背景色之间的颜色对比度为 4.5:1 或更高。
- 使屏幕阅读器（ARIA、alt 和 title 特性）阅读的消息简短、具描述性且有意义。 避免不必要的行话和缩写。
- 尝试优化发送到屏幕阅读器的消息，以提供可让用户容易领会的简短且有意义的信息。 例如，若要以较高频率更新屏幕阅读器（例如，在移动地图时这样做），请考虑以下要点：
    - 等到地图完成移动后再更新屏幕阅读器。
    - 将更新限制为每隔几秒钟一次。 
    - 以逻辑方式将消息组合在一起。 
- 避免使用颜色作为传达信息的唯一方式。 使用文本、图标或模式来补充或取代颜色。 一些注意事项：
    - 如果使用气泡图层来显示数据点之间的相对值，请考虑缩放每个气泡的半径以及/或者为气泡编上颜色。 
    - 对于不同的指标类别，请考虑使用带有不同图标的符号层，例如三角形、星形和正方形。 符号层还支持缩放图标的大小。 还可以显示文本标签。
    - 如果显示线条数据，可以使用宽度来表示重量或大小。 可以使用虚线数组模式表示不同的线条类别。 可以结合使用符号层和线条来覆盖线条上的图标。 箭头图标可用于显示线条的走向或方向。
    - 如果显示多边形数据，可以使用某种模式（例如条纹）来替代颜色。 
- 存在视力障碍的用户无法访问某些可视化效果（例如热度地图、图块层和图像层）。 一些注意事项：
    - 让屏幕阅读器描述图层要显示添加到地图中的哪些内容。 例如，如果显示气象雷达图块层，则让屏幕阅读器讲出“已在地图上叠加天气雷达数据”。
- 限制需要鼠标悬停的功能数。 使用键盘或触屏设备与应用程序交互的用户无法访问这些功能。 请注意，对可单击的图标、链接和按钮等交互内容使用悬停样式仍然是好的做法。
- 尝试使用键盘在应用程序中导航。 确保 Tab 键顺序具有逻辑性。
- 如果创建键盘快捷键，请尝试将其限制为两个键或更少的键。 

## <a name="next-steps"></a>后续步骤

了解 Web SDK 模块中的辅助功能。

> [!div class="nextstepaction"]
> [绘制工具辅助功能](drawing-tools-interactions-keyboard-shortcuts.md)

在 Microsoft Learn 中了解如何开发易访问的应用：

> [!div class="nextstepaction"]
> [Accessibility in Action 数字徽章学习路径](https://ready.azurewebsites.net/learning/track/2940)

大致了解这些有用的辅助功能工具：
> [!div class="nextstepaction"]
> [开发易访问的应用](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA 概述](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web 辅助功能评估工具 (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim 颜色对比度检查程序](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [NoCoffee 视觉模拟器](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
