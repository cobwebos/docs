---
title: 将 HTML 标记添加到地图 | Microsoft Azure Maps
description: 本文介绍如何使用 Microsoft Azure Maps Web SDK 向地图添加 HTML 标记。
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 30d9cf9848a8d99505bf9f9c97bef5aaa31065ee
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119284"
---
# <a name="add-html-markers-to-the-map"></a>向地图添加 HTML 标记

本文展示了如何向地图添加自定义 HTML（例如图像文件）作为 HTML 标记。

> [!NOTE]
> HTML 标记不连接到数据源。 相反，位置信息将被直接添加到标记中，标记将被添加到地图 `markers` 属性中，即 [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)。

> [!IMPORTANT]
> 与 Azure Maps Web 控件中使用 WebGL 进行呈现的大多数层不同，HTML 标记使用传统的 DOM 元素进行呈现。 因此，添加到页面的 HTML 标记越多，DOM 元素就越多。 添加几百个 HTML 标记后，可能会降低性能。 对于较大的数据集，可以考虑对数据进行聚类分析，或者使用符号或气泡层。

## <a name="add-an-html-marker"></a>添加 HTML 标记

[HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) 类具有默认样式。 可以通过设置标记的颜色和文本选项自定义标记。 HTML 标记类的默认样式是具有 `{color}` 和 `{text}` 占位符的 SVG 模板。 在 HTML 标记选项中设置颜色和文本属性，以便进行快速自定义。 

下面的代码创建一个 HTML 标记，并将 color 属性设置为“DodgerBlue”，将 text 属性设置为“10”。 一个弹出窗口将附加到该标记，`click` 事件用于切换弹出窗口的可见性。

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='向地图添加 HTML 标记' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>向地图添加 HTML 标记</a>。
</iframe>

## <a name="create-svg-templated-html-marker"></a>创建 SVG 模板化的 HTML 标记

Html 标记的默认 `htmlContent` 是 SVG 模板，其中包含文件夹 `{color}` 和 `{text}`。 可以创建自定义 SVG 字符串并将这些相同的占位符添加到 SVG 中，以便设置标记的 `color` 和 `text` 选项来更新 SVG 中的这些占位符。

<br/>

<iframe height='500' scrolling='no' title='使用自定义 SVG 模板的 HTML 标记' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>使用自定义 SVG 模板的 HTML 标记</a>。
</iframe>

> [!TIP]
> Azure Maps Web SDK 提供了多个可与 HTML 标记一起使用的 SVG 图像模板。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="add-a-css-styled-html-marker"></a>添加 CSS 样式化的 HTML 标记

HTML 标记的优点之一是，可以使用 CSS 来实现许多有用的自定义项。 在此示例中，HtmlMarker 内容由 HTML 和 CSS 组成，它们创建一个动画图钉，使其落在适当的位置并产生脉冲。

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

## <a name="draggable-html-markers"></a>可拖动的 HTML 标记

此示例演示如何使 HTML 标记可拖动。 HTML 标记支持 `drag`、`dragstart` 和 `dragend` 事件。

<br/>

<iframe height='500' scrolling='no' title='可拖动的 HTML 标记' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>可拖动的 HTML 标记</a>。
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>将鼠标事件添加到 HTML 标记

这些示例展示如何将鼠标和拖动事件添加到 HTML 标记中。

<br/>

<iframe height='500' scrolling='no' title='将鼠标事件添加到 HTML 标记' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>将鼠标事件添加到 HTML 标记</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡层](./map-add-bubble-layer.md)
