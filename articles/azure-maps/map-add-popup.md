---
title: 向地图上的点添加弹出项 |Microsoft Azure 映射
description: 在本文中，你将了解如何使用 Microsoft Azure Map Web SDK 将 popup 添加到某个点。
author: Philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d7ca2f07e4568257baf72f2f298631b86f776c2a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119256"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图上的点添加弹出窗口。

## <a name="understand-the-code"></a>了解代码

下面的代码使用符号层向地图添加一个点功能，该功能具有 `name` 和 `description` 属性。 创建了[Popup 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)的实例，但未将其显示出来。 鼠标事件会添加到符号层，以触发打开和关闭弹出窗口。 悬停标记符号时，将 `position` 用标记的位置更新 popup 的属性，并 `content` 用一些 HTML 来更新选项，这些 HTML 用于包装 `name` `description` 要悬停的点功能的和属性。 然后，使用其功能在地图上显示弹出窗口 `open` 。

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

<iframe height='500' scrolling='no' title='使用 Azure Maps 添加弹出窗口' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a>（使用 Azure Maps 添加弹出窗口）。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重复使用具有多个点的弹出窗口

在某些情况下，最好的方法是创建一个弹出窗口并重复使用它。 例如，你可能有大量点，并且希望一次只显示一个弹出窗口。 通过重复使用弹出式窗口，应用程序创建的 DOM 元素数量大大减少，从而提供更好的性能。 下面的示例创建了3个点特征。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='重复使用具有多个固定项的弹出窗口' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重复使用具有多个固定项的弹出窗口</a> Pen。
</iframe>

## <a name="customizing-a-popup"></a>自定义弹出项

默认情况下，popup 具有白色背景、底部有一个指针箭头以及右上角的 "关闭" 按钮。 下面的示例使用 popup 的选项将背景色更改为黑色 `fillColor` 。 通过将选项设置为 false 来删除 "关闭" 按钮 `CloseButton` 。 弹出项的 HTML 内容使用从弹出项边缘填充10像素。 文本变为白色，因此显示在黑色背景上。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自定义弹出窗口" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自定义" 弹出窗口</a>Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>）。
</iframe>

## <a name="add-popup-templates-to-the-map"></a>向映射添加弹出项模板

Popup 模板使你可以轻松地为弹出窗口创建数据驱动布局。 以下各节演示了如何使用各种弹出式模板来使用功能的属性生成格式化内容。

> [!NOTE]
> 默认情况下，呈现的所有内容都使用 popup 模板作为一项安全功能在 iframe 内进行沙盒处理。 但是，存在一些限制：
>
> - 所有脚本、窗体、指针锁和顶部导航功能都处于禁用状态。 单击时，允许链接在新选项卡中打开。 
> - 不支持 iframe 上的参数的较早的浏览器 `srcdoc` 将被限制为呈现少量内容。
> 
> 如果信任加载到弹出窗口中的数据，并且可能希望将这些脚本加载到弹出窗口中才能访问应用程序，则可以通过将 popup 模板选项设置为 false 来禁用此设置 `sandboxContent` 。 

### <a name="string-template"></a>字符串模板

字符串模板会将占位符替换为功能属性的值。 此功能的属性不必赋给 String 类型的值。 例如， `value1` 保存一个整数。 然后，将这些值传递给的 content 属性 `popupTemplate` 。 

`numberFormat`选项指定要显示的数字的格式。 如果 `numberFormat` 未指定，则代码将使用 popup 模板日期格式。 `numberFormat`选项使用[toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函数对数字进行格式设置。 若要格式化大数字，请考虑将 `numberFormat` 选项用于[intl.numberformat](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)中的函数。 例如，下面的代码片段使用 `maximumFractionDigits` 将小数位位数限制为两个数字。

> [!Note]
> 只有一种方法，字符串模板可以呈现图像。 首先，字符串模板中需要有一个图像标记。 传递给图像标记的值应为指向图像的 URL。 然后，在中，字符串模板需要 `isImage` 设置为 true `HyperLinkFormatOptions` 。 `isImage`选项指定超链接用于图像，超链接将加载到图像标记中。 单击超链接时，将打开该图像。

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo 模板

PropertyInfo 模板显示功能的可用属性。 `label`选项指定要向用户显示的文本。 如果 `label` 未指定，则将显示超链接。 如果超链接为图像，则将显示分配给 "alt" 标记的值。 `dateFormat`指定日期的格式，如果未指定日期格式，则日期将呈现为字符串。 `hyperlinkFormat`选项可呈现可单击的链接，同样， `email` 选项可用于呈现可单击的电子邮件地址。

在 PropertyInfo 模板向最终用户显示这些属性之前，它会以递归方式检查是否确实为该功能定义了属性。 它还会忽略显示样式和标题属性。 例如，它不显示 `color` 、、 `size` `anchor` 、 `strokeOpacity` 和 `visibility` 。 因此，在后端中完成属性路径检查后，PropertyInfo 模板以表格式显示内容。

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>多个内容模板

功能还可以结合使用字符串模板和 PropertyInfo 模板来显示内容。 在这种情况下，字符串模板在白色背景上呈现占位符值。  而且，PropertyInfo 模板将在表中呈现宽度的宽度图像。 此示例中的属性与我们在前面的示例中介绍的属性类似。

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>没有定义的模板的点

如果 Popup 模板未定义为字符串模板、PropertyInfo 模板或二者的组合，则将使用默认设置。 当 `title` 和 `description` 是唯一分配的属性时，popup 模板会显示一个白色背景，即右上角的 "关闭" 按钮。 在中小型屏幕上，它会在底部显示一个箭头。 除和之外的所有属性在表中显示默认设置 `title` `description` 。 即使回退到默认设置，也仍可通过编程方式操作 popup 模板。 例如，用户可以关闭超链接检测，默认设置仍适用于其他属性。

在 CodePen 中单击地图上的点。 以下每个弹出式模板的地图上都有一个点：字符串模板、PropertyInfo 模板和多内容模板。 还可以使用三个点来显示模板如何使用默认设置进行呈现。

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的<a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> ）。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="reuse-popup-template"></a>重复使用弹出项模板

与重复使用 popup 类似，你可以重复使用弹出式模板。 当你只想要为多个点同时显示一个弹出模板时，此方法非常有用。 通过重复使用 popup 模板，应用程序创建的 DOM 元素的数目会减少，从而提高应用程序的性能。 下面的示例对三个点使用同一个弹出模板。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的<a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> ）。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="popup-events"></a>弹出项事件

可以打开、关闭和拖动弹出窗口。 Popup 类提供事件以帮助开发人员响应这些事件。 下面的示例重点介绍当用户打开、关闭或拖动弹出项时触发的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="弹出项事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的笔<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>弹出事件</a>，Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> ）。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [弹出项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

有关完整代码示例，请参阅以下精彩文章：

> [!div class="nextstepaction"]
> [添加符号层](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)
