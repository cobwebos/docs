---
title: 将弹出窗口添加到地图上的点 |微软 Azure 地图
description: 在本文中，您将学习如何使用 Microsoft Azure 地图 Web SDK 将弹出窗口添加到某个点。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055951"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图上的点添加弹出窗口。

## <a name="understand-the-code"></a>了解代码

以下代码使用符号图层向地图添加具有`name`和`description`属性的点要素。 将创建[弹出类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)的实例，但不会显示。 鼠标事件将添加到符号图层中，以触发弹出窗口的打开和关闭。 当标记`position`符号被悬停时，弹出窗口的属性会随着标记的位置进行更新，并且`content`该选项将更新为一些 HTML，该 HTML 将要悬停`name`的`description`点要素和属性进行包装。 然后，弹出窗口将利用其`open`功能显示在地图上。

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

在某些情况下，最好的方法是创建一个弹出窗口并重用它。 例如，您可能具有大量点，并且希望一次只显示一个弹出窗口。 通过重用弹出窗口，应用程序创建的 DOM 元素的数量大大减少，从而提供更好的性能。 以下示例创建三点要素。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='重复使用具有多个固定项的弹出窗口' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重复使用具有多个固定项的弹出窗口</a> Pen。
</iframe>

## <a name="customizing-a-popup"></a>自定义弹出窗口

默认情况下，弹出窗口的背景为白色，底部有指针箭头，右上角有一个关闭按钮。 以下示例使用弹出窗口`fillColor`的选项将背景颜色更改为黑色。 通过将`CloseButton`选项设置为 false，将删除关闭按钮。 弹出窗口的 HTML 内容使用从弹出窗口边缘填充的 10 像素。 文本是白色的，所以它显示在黑色背景上。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自定义弹出窗口" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 自定义的笔<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自定义弹出窗口</a>。
</iframe>

## <a name="add-popup-templates-to-the-map"></a>将弹出模板添加到地图

弹出式模板便于为弹出窗口创建数据驱动的布局。 以下各节演示了使用各种弹出式模板使用功能属性生成格式化内容。

> [!NOTE]
> 默认情况下，使用弹出式模板呈现的所有内容都将在 iframe 内部作为安全功能进行沙盒处理。 但是，存在限制：
>
> - 禁用所有脚本、窗体、指针锁定和顶部导航功能。 单击时，允许在新选项卡中打开链接。 
> - 不支持 iframe 上`srcdoc`参数的旧浏览器将仅限于呈现少量内容。
> 
> 如果您信任加载到弹出窗口中的数据，并且可能希望加载到弹出窗口中的这些脚本能够访问您的应用程序，则可以通过将弹出窗口模板`sandboxContent`选项设置为 false 来禁用此功能。 

### <a name="string-template"></a>字符串模板

字符串模板使用要素属性的值替换占位符。 不必为要素的属性分配 String 类型的值。 例如，`value1`保存整数。 然后，这些值将传递给 的内容`popupTemplate`属性。 

该`numberFormat`选项指定要显示的数字的格式。 如果未指定`numberFormat`， 则代码将使用弹出模板日期格式。 该`numberFormat`选项使用["数字.到本地字符串"](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)功能设置数字格式。 要设置大数字的格式，请考虑`numberFormat`将 选项与[NumberFormat.format.格式](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)中的函数一起使用。 例如，下面的代码段用于`maximumFractionDigits`将分数位数限制为两个。

> [!Note]
> 字符串模板只有一种方式可以呈现图像。 首先，字符串模板需要有一个图像标记。 传递给图像标记的值应为图像的 URL。 然后，字符串模板需要在 中`isImage``HyperLinkFormatOptions`设置为 true。 该`isImage`选项指定超链接用于图像，超链接将加载到图像标记中。 单击超链接时，图像将打开。

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

### <a name="propertyinfo-template"></a>属性信息模板

属性信息模板显示该功能的可用属性。 该`label`选项指定要向用户显示的文本。 如果未`label`指定，则将显示超链接。 而且，如果超链接是图像，将显示分配给"alt"标记的值。 指定`dateFormat`日期的格式，如果未指定日期格式，则日期将呈现为字符串。 该`hyperlinkFormat`选项呈现可单击的链接，同样，`email`该选项可用于呈现可单击的电子邮件地址。

在属性信息模板向最终用户显示属性之前，它会递归地检查这些属性确实已为该功能定义。 它还忽略显示样式和标题属性。 例如，`color`它不会显示`size`、、`anchor``strokeOpacity`和`visibility`。 因此，一旦属性路径检查在后端完成，属性信息模板将以表格式显示内容。

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

功能还可以使用字符串模板和属性信息模板的组合显示内容。 在这种情况下，字符串模板在白色背景上呈现占位符值。  而且，属性信息模板在表格中呈现全宽度图像。 此示例中的属性与我们在前面的示例中解释的属性类似。

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

### <a name="points-without-a-defined-template"></a>没有已定义模板的点

当弹出窗口模板未定义为字符串模板、属性信息模板或两者的组合时，它使用默认设置。 当`title`和`description`是唯一分配的属性时，弹出模板将显示一个白色背景，右上角的关闭按钮。 在中小型屏幕上，它显示了底部的箭头。 默认设置显示在 表内，用于 和 以外的`title`所有属性。 `description` 即使回到默认设置，弹出窗口模板仍可以通过编程方式进行操作。 例如，用户可以关闭超链接检测，默认设置仍应用于其他属性。

单击 CodePen 中地图上的点。 地图上有以下每个弹出模板的点：字符串模板、属性信息模板和多个内容模板。 还有三点可以显示模板如何使用默认设置呈现。

<br/>

<iframe height='500' scrolling='no' title='弹出模板' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/dyovrzL/'>弹出窗口模板</a>。
</iframe>

## <a name="reuse-popup-template"></a>重复使用弹出窗口模板

与重复使用弹出窗口类似，您可以重复使用弹出窗口模板。 当您只想一次显示多个点的弹出模板时，此方法非常有用。 通过重用弹出窗口模板，应用程序创建的 DOM 元素的数量将减少，从而提高应用程序的性能。 以下示例对三个点使用相同的弹出窗口模板。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='重用弹出模板' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的笔<a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>重用弹出模板</a>。
</iframe>

## <a name="popup-events"></a>弹出事件

弹出窗口可以打开、关闭和拖动。 弹出类提供事件来帮助开发人员对这些事件做出反应。 以下示例突出显示当用户打开、关闭或拖动弹出窗口时触发的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="弹出事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看笔<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>弹出窗口事件</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [弹出项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [弹出模板](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

有关完整代码示例，请参阅以下精彩文章：

> [!div class="nextstepaction"]
> [添加符号图层](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)
