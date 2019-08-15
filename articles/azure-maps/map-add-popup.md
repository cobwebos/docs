---
title: 使用 Azure Maps 添加弹出窗口 | Microsoft Docs
description: 如何将 popup 添加到 Azure Maps Web SDK。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976548"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图上的点添加弹出窗口。

## <a name="understand-the-code"></a>了解代码

下面的代码使用符号层向地图添加一个`name`点`description`功能, 该功能具有和属性。 创建了[Popup 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)的实例, 但未将其显示出来。 当鼠标悬停在符号标记上时, 鼠标事件会添加到符号层, 以触发打开和关闭弹出窗口。 当悬停标记符号时, 将用标记的`position`位置更新 popup 的属性, `content`并用`name`一些 HTML 来更新选项, 这些 HTML 用于包装要悬停`description`的点功能的和属性。 然后, 使用其`open`功能在地图上显示弹出窗口。

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='使用 Azure Maps 添加弹出窗口' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a>（使用 Azure Maps 添加弹出窗口）。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重复使用具有多个点的弹出窗口

如果有大量的点, 并且只想要一次显示一个弹出窗口, 最佳方法是创建一个弹出窗口并重复使用它, 而不是为每个点功能创建一个弹出窗口。 通过重复使用弹出式窗口, 应用程序创建的 DOM 元素数量大大降低, 从而提供更好的性能。 下面的示例创建了3个点特征。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='重复使用具有多个固定项的弹出窗口' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重复使用具有多个固定项的弹出窗口</a> Pen。
</iframe>

## <a name="customizing-a-popup"></a>自定义弹出项

默认情况下, popup 具有白色背景、底部有一个指针箭头以及右上角的 "关闭" 按钮。 下面的示例使用`fillColor` popup 的选项将背景色更改为黑色。 通过将`shoCloseButton`选项设置为 false 来删除 "关闭" 按钮。 弹出项的 HTML 内容使用从弹出项边缘填充10像素, 文本变为白色, 使其在黑色背景上正常显示。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自定义弹出窗口" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自定义" 弹出窗口</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="popup-events"></a>弹出项事件

可以打开、关闭和拖动弹出窗口。 Popup 类为帮助开发人员对这些操作做出反应的事件提供事件。 下面的示例突出显示打开、关闭或拖动弹出项时要触发的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="弹出项事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>弹出事件</a>, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下精彩文章：

> [!div class="nextstepaction"]
> [添加符号层](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)