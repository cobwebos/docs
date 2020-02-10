---
title: 教程：从 Google Maps 迁移 Web 应用 | Microsoft Azure Maps
description: 如何将 Web 应用从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989106"
---
# <a name="migrate-a-web-app-from-google-maps"></a>从 Google Maps 迁移 Web 应用

使用 Google Maps 的大多数 Web 应用都是使用 Google Maps V3 JavaScript SDK。 Azure Maps Web SDK 是适合用于迁移目标的基于 Azure 的 SDK。 Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图。 你可以在 Web 或移动应用程序中运行自己的应用。 此控件使用 WebGL，因此可以渲染大型数据集，同时保持很高的性能。 使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

如果迁移现有的 Web 应用程序，请检查该应用程序是否使用开源地图控件库。 开源地图控件库的示例包括：Cesium、Leaflet 和 OpenLayers。 如果是，但你不想要使用 Azure Maps Web SDK，则迁移应用程序的另一种做法是继续使用开源地图控件，并将其连接到 Azure Maps 图块服务（[道路图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)）。 下面指出了有关如何在某些常用开源地图控件库中使用 Azure Maps 的详细信息。

- Cesium - 适用于 Web 的 3D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [文档](https://cesiumjs.org/)
- Leaflet - 适用于 Web 的轻型 2D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [文档](https://leafletjs.com/)
- OpenLayers - 支持投影的适用于 Web 的 2D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [文档](https://openlayers.org/)

## <a name="key-features-support"></a>重要功能支持

下表列出了 Google Maps V3 JavaScript SDK 中的重要 API 功能，以及 Azure Maps Web SDK 中支持的 API 功能。

| Google Maps 功能     | Azure Maps Web SDK 支持 |
|-------------------------|:--------------------------:|
| 标记                 | ✓                          |
| 标记聚类       | ✓                          |
| 折线和多边形    | ✓                          |
| 数据层             | ✓                          |
| 地面叠层         | ✓                          |
| 热度地图               | ✓                          |
| 图块层             | ✓                          |
| KML 层               | ✓                          |
| 绘图工具           | ✓                          |
| 地理编码器服务        | ✓                          |
| 方向服务      | ✓                          |
| 距离矩阵服务 | ✓                          |
| 高度服务       | 已计划                    |

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK 中的显著差异

下面是 Google Maps 与 Azure Maps Web SDK 之间需要注意的一些重要区别：

- 除了提供托管终结点用于访问 Azure Maps Web SDK 以外，还可以根据偏好使用某个 NPM 包将 Web SDK 嵌入应用。 有关详细信息，请参阅[此文档](how-to-use-map-control.md)。 此程序包还包括了 TypeScript 定义。
- 在 Azure Maps 中创建 Map 类的实例后，代码应等待激发地图 `ready` 或 `load` 事件，然后与地图交互。 此顺序可以确保加载所有地图资源并准备好访问这些资源。
- 这两个平台为基础地图使用类似的图块系统，但 Google Maps 中图块的尺寸为 256 像素，而 Azure Maps 中图块的尺寸为 512 像素。 因此，若要在 Azure Maps 中获得与 Google Maps 相同的地图视图，在 Google Maps 中使用的缩放级别在 Azure Maps 中需要减 1。
- Google Maps 中的坐标以“纬度, 经度”表示，Azure Maps 则使用“经度, 纬度”。 Azure Maps 格式符合大多数 GIS 平台所遵循的标准 `[x, y]`。
- Azure Maps Web SDK 中的形状基于 GeoJSON 架构。 帮助器类通过 [*atlas.data* 命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)公开。 还有 [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 类。 此类可用于包装 GeoJSON 对象，以便能够以数据可绑定的方式轻松更新和维护这些对象。
- Azure Maps 中的坐标定义为 Position 对象。 可将坐标指定为采用 `[longitude, latitude]` 格式的数字数组，或使用新的 atlas.data.Position(longitude, latitude) 指定坐标。
    > [!TIP]
    > Position 类包含静态帮助器方法用于导入“纬度, 经度”格式的坐标。 往往可以将 [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) 方法替换为 Google Maps 代码中的 `new google.maps.LatLng` 方法。
- Azure Maps 将样式与数据相区分，而不是在添加到地图的每个形状中指定样式信息。 数据存储在数据源中，并连接到呈现层。 Azure Maps 代码使用数据源来呈现数据。 此方法提供增强的性能优势。 此外，很多层支持数据驱动的样式，其中，业务逻辑可添加到层样式选项。 此项支持更改了单个形状根据形状中定义的属性在层中的呈现方式。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 对比示例

以下集合包含每个平台的代码示例，其中每个示例涵盖了常见用例。 此集合旨在帮助你将 Web 应用程序从 Google Maps V3 JavaScript SDK 迁移到 Azure Maps Web SDK。 与 Web 应用程序相关的代码示例以 JavaScript 提供。 但是，Azure Maps 还通过 [NPM 模块](how-to-use-map-control.md)提供 TypeScript 定义作为附加选项。

### <a name="load-a-map"></a>加载地图

在这两个 SDK 中加载地图需遵循相同的一组步骤：

- 添加对 Map SDK 的引用。
- 将充当地图占位符的 `div` 标记添加到页面的正文。
- 创建加载页面时要调用的 JavaScript 函数。
- 创建相应 map 类的实例。

**一些重要差别**

- Google Maps 要求在 API 的脚本引用中指定帐户密钥。 Azure Maps 的身份验证凭据指定为 map 类的选项。 此凭据可以是订阅密钥或 Azure Active Directory 信息。
- Google Maps 在 API 的脚本引用中采用一个回调函数，该函数用于调用初始化函数来加载地图。 使用 Azure Maps 时，应使用页面的 onload 事件。
- 引用要在其中呈现地图的 `div` 元素时，Azure Maps 中的 `Map` 类只需要 `id` 值，而 Google Maps 需要 `HTMLElement` 对象。
- Azure Maps 中的坐标定义为 Position 对象，可将这些对象指定为采用 `[longitude, latitude]` 格式的简单数字数组。
- Azure Maps 中的缩放级别比 Google Maps 中的缩放级别要低一级。 之所以存在这种差异，是因为这两个平台之间的图块系统大小有差别。
- Azure Maps 不会将任何导航控件添加到地图画布。 因此，默认情况下，地图中不提供缩放按钮和地图样式按钮。 但是，有一些控件可用于添加地图样式选取器、缩放按钮、罗盘或旋转控件以及俯仰角控件。
- Azure Maps 中添加了一个事件处理程序用于监视地图实例的 `ready` 事件。 在地图加载完 WebGL 上下文以及所需的所有资源后，将激发此事件。 可将地图完成加载后你要运行的任何代码添加到此事件处理程序。

以下示例演示如何加载基本地图，使该地图在 Google Maps 中的中心点位于纽约，坐标为（经度：-73.985，纬度：40.747），缩放级别为 12。

**前者：Google Maps**

以下代码示例演示如何显示在某个位置居中并缩放的 Google 地图。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在浏览器中运行此代码会显示如下图所示的地图：

<center>

![简单 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**后者：Azure Maps**

以下代码演示如何加载具有 Azure Maps 中的相同视图以及地图样式控件和缩放按钮的地图。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在浏览器中运行此代码会显示如下图所示的地图：

<center>

![简单 Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

在[此处](how-to-use-map-control.md)可以找到有关如何在 Web 应用中设置和使用 Azure Maps 地图控件的详细文档。

> [!NOTE]
> 与 Google Maps 不同，Azure Maps 在加载地图时不需要指定初始中心点和缩放级别。 如果在加载地图时未提供此信息，地图将尝试确定用户所在的城市，然后在该位置居中并缩放地图。

**其他资源：**

- Azure Maps 还提供了导航控件，用于按[此文](map-add-controls.md)所述旋转地图视图及调整其俯仰角。

### <a name="localizing-the-map"></a>本地化地图

如果你的受众遍布多个国家/地区或讲不同的语言，则本地化就非常重要。

**前者：Google Maps**

若要本地化 Google Maps，请将语言和区域参数添加到

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

下面是将语言设置为“fr-FR”的 Google Maps 示例。

<center>

![Google Maps 本地化](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**后者：Azure Maps**

Azure Maps 提供两种不同的方式来设置地图的语言和区域视图。 第一种做法是将此信息添加到全局 *atlas* 命名空间，使应用中的所有地图控件实例默认采用这些设置。 以下示例将语言设置为法语（“fr-FR”），将区域视图设置为“auto”：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二种做法是在加载地图时将此信息传入地图选项，如下所示：

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> 使用 Azure Maps 时，可以在同一页面上加载使用不同语言和区域设置的多个地图实例。 此外，还可以在加载地图后在其中更新这些设置。 在[此处](supported-languages.md)可以找到 Azure Maps 支持的语言的详细列表。

下面是将语言设置为“fr”、将用户区域设置为“fr-FR”的 Azure Maps 示例。

<center>

![Azure Maps 本地化](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>设置地图视图

可以编程方式将 Azure Maps 和 Google Maps 中的动态地图移到新的地理位置。 为此，请调用 JavaScript 中的相应函数。 这些示例演示如何在 Google Maps 中使地图显示卫星航拍图像，将地图中心点置于某个位置，并将缩放级别更改为 15。 使用以下位置坐标：经度：-111.0225，纬度：35.0272。

> [!NOTE]
> Google Maps 使用尺寸为 256 像素的图块，而 Azure Maps 则使用更大的 512 像素图块。 在加载相同的地图区域时，与 Google Maps 相比，这可以减少 Azure Maps 所需的网络请求数。 但是，由于地图控件中图块金字塔的工作方式，Azure Maps 中更大的图块意味着在，在使用 Azure Maps 时，若要实现与 Google Maps 中地图相同的可视区域，需要将 Google Maps 中使用的缩放级别减 1。

**前者：Google Maps**

可以使用 `setOptions` 方法以编程方式移动 Google Maps 地图控件。 此方法允许指定地图的中心点和缩放级别。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps - 设置视图](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，可以使用地图的 `setCamera` 方法以编程方式更改地图位置，可以使用 `setStyle` 方法更改地图样式。 Azure Maps 中的坐标采用“经度, 纬度”格式，缩放级别值需减 1。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps - 设置视图](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**其他资源：**

- [选择地图样式](choose-map-style.md)
- [支持的地图样式](supported-map-styles.md)

### <a name="adding-a-marker"></a>添加标记

在 Azure Maps 中，可通过多种方式在地图上呈现点数据；

- **HTML 标记** – 使用传统的 DOM 元素呈现点。 HTML 标记支持拖动。
- **符号层** – 在 WebGL 上下文中使用图标和/或文本呈现点。
- **气泡层** – 在地图上以圆的形式呈现点。 可根据数据中的属性缩放圆的半径。

符号层和气泡层都在 WebGL 上下文中呈现。 这两个层能够在地图上呈现极大的点集。 这些层要求将数据存储在数据源中。 激发 `ready` 事件后，应将数据源和呈现层添加到地图中。 HTML 标记在页面中呈现为 DOM 元素，且不使用数据源。 页面中的 DOM 元素越多，页面加载速度越慢。 若要在地图上呈现好几百个点，则我们建议改用某个呈现层。

以下示例将一个标记添加到地图上的（经度：-0.2，纬度：51.5）位置，并使用叠加的数字 10 作为标签。

**前者：Google Maps**

使用 Google Maps 时，可以使用 `google.maps.Marker` 类并将地图指定为选项之一，将标记添加到地图。

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps 标记](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**后者：使用 HTML 标记的 Azure Maps**

在 Azure Maps 中，HTML 标记可用于在地图上显示某个点。 对于只需在地图上显示少量点的简单应用，建议使用 HTML 标记。 若要使用 HTML 标记，请创建 `atlas.HtmlMarker` 类的实例，设置文本和位置选项，然后使用 `map.markers.add` 方法将标记添加到地图。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML 标记](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**后者：使用符号层的 Azure Maps**

使用符号层时，必须将数据添加到数据源，并将数据源附加到层。 此外，在 `ready` 事件激发后，应将数据源和层添加到地图。 若要在符号上方呈现唯一的文本值，需将文本信息存储为数据点的属性，并且必须在层的 `textField` 选项中引用该属性。 这种做法比使用 HTML 标记要略微繁琐一些，但具有性能优势。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 符号层](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**其他资源：**

- [创建数据源](create-data-source-web-sdk.md)
- [添加符号层](map-add-pin.md)
- [添加气泡层](map-add-bubble-layer.md)
- [聚类点数据](clustering-point-data-web-sdk.md)
- [添加 HTML 标记](map-add-custom-html.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)
- [符号层图标选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [符号层文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 标记类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 标记选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>添加自定义标记

可使用自定义图像来表示地图上的点。 以下地图图像使用自定义图像在地图上显示一个点。 该点的显示位置为纬度：51.5，经度：-0.2。 定位点将标记位置偏移，使图钉图标的点与地图上的正确位置对齐。

<center>

![黄色图钉图像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**前者：Google Maps**

在 Google Maps 中，可通过指定 `Icon` 对象来创建自定义标记，该对象包含图像的 `url`、与图钉图像点对齐的 `anchor` 点，以及地图上的坐标。 Google Maps 中的定位点值相对于图像的左上角。

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps 自定义标记](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**后者：使用 HTML 标记的 Azure Maps**

若要在 Azure Maps 中自定义 HTML 标记，可将 HTML `string` 或 `HTMLElement` 传递给该标记的 `htmlContent` 选项。 在 Azure Maps 中，`anchor` 选项用于通过九个已定义的参照点之一，来指定标记相对于位置坐标的位置。 定义的参照点为：“center”、“top”、“bottom”、“left”、“right”、“top-left”、“top-right”、“bottom-left”、“bottom-right”。 默认情况下，内容将定位到 html 内容的中下方。 若要更轻松地从 Google Maps 迁移代码，请将 `anchor` 设置为“top-left”，然后将 `pixelOffset` 选项与 Google Maps 中使用的相同偏移量结合使用。 Azure Maps 中的偏移方向与 Google Maps 中相反，因此需将偏移量乘以 -1。

> [!TIP]
> 在显示不需要的图标的 Microsoft Edge 中，添加 `pointer-events:none` 作为 html 内容的样式可以禁用默认拖放行为。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps 自定义 HTML 标记](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**后者：使用符号层的 Azure Maps**

Azure Maps 中的符号层也支持自定义图像，但需要先将图像载入地图资源，并为其分配唯一的 ID。 然后，符号层可以引用此 ID。 可以使用图标 `offset` 选项来偏移符号，使之与图像上的正确点对齐。 在 Azure Maps 中，`anchor` 选项用于通过九个已定义的参照点之一，来指定符号相对于位置坐标的位置。 定义的位置坐标为：“center”、“top”、“bottom”、“left”、“right”、“top-left”、“top-right”、“bottom-left”、“bottom-right”。 默认情况下，内容将定位到 html 内容的中下方。 若要更轻松地从 Google Maps 迁移代码，请将 `anchor` 设置为“top-left”，然后将 `offset` 选项与 Google Maps 中使用的相同偏移量结合使用。 Azure Maps 中的偏移方向与 Google Maps 中相反，因此需将偏移量乘以 -1。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 自定义图标符号层](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> 若要创建点的高级自定义呈现，请结合使用多个呈现层。 例如，假设你要使用多个图钉，而这些图钉在不同的彩色圆上使用相同的图标。 不需要针对每个颜色叠加层创建大量的图像，而可以在气泡层的顶部添加一个符号层，并使图钉引用同一个数据源。 这种做法比创建并维护大量不同的图像更为高效。

**其他资源：**

- [创建数据源](create-data-source-web-sdk.md)
- [添加符号层](map-add-pin.md)
- [添加 HTML 标记](map-add-custom-html.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)
- [符号层图标选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [符号层文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 标记类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 标记选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>添加折线

折线用于表示地图上的线条或路径。 以下示例演示如何在地图上创建虚线折线。

**前者：Google Maps**

在 Google Maps 中，Polyline 类采用一组选项。 将坐标数组传入折线的 `path` 选项。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps 折线](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，折线称为 `LineString` 或 `MultiLineString` 对象。 可将这些对象添加到数据源，并使用线条层来呈现。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps 折线](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**其他资源：**

- [将线条添加到地图](map-add-line-layer.md)
- [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>添加多边形

多边形用于表示地图上的某个区域。 Azure Maps 和 Google Maps 为多边形提供类似的支持。 以下示例演示如何创建一个多边形，该多边形基于地图的中点坐标构成一个三角形。

**前者：Google Maps**

在 Google Maps 中，Polygon 类采用一组选项。 将坐标数组传入多边形的 `paths` 选项。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps 多边形](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，可将 `Polygon` 和 `MultiPolygon` 对象添加到数据源，并使用层在地图上呈现这些对象。 可以在多边形层中呈现多边形的区域。 可以使用线条层呈现多边形的轮廓。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps 多边形](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**其他资源：**

- [将多边形添加到地图](map-add-shape.md)
- [将圆添加到地图](map-add-shape.md#add-a-circle-to-the-map)
- [多边形层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>显示信息窗口

在 Google Maps 中，实体的其他信息可以作为 `google.maps.InfoWindow` 类显示在地图上。 在 Azure Maps 中，可以使用 `atlas.Popup` 类实现此功能。 以下示例将一个标记添加到地图，单击该标记会显示信息窗口/弹出窗口。

**前者：Google Maps**

在 Google Maps 中，信息窗口是使用 `google.maps.InfoWindow` 构造函数创建的。

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps 弹出窗口](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，弹出窗口可用于显示某个位置的附加信息。 可将 HTML `string` 或 `HTMLElement` 对象传入弹出窗口的 `content` 选项。 如果需要，可以独立于任何形状显示弹出窗口。 因此，需要为弹出窗口指定 `position` 值 若要显示弹出窗口，请调用 `open` 方法，并传入要在其中显示弹出窗口的 `map`。

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps 弹出窗口](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 若要使用符号、气泡、线条或多边形层实现相同的目的，只需将该层传入地图事件代码，而不要使用标记。

**其他资源：**

- [添加弹出项](map-add-popup.md)
- [包含媒体内容的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [包含形状的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [重复使用包含多个图钉的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [弹出窗口类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [弹出窗口选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>导入 GeoJSON 文件

Google Maps 支持通过 `google.maps.Data` 类加载 GeoJSON 数据并动态设置其样式。 此类的功能在很大程度上与 Azure Maps 的数据驱动样式相同。 一个重要差别在于，使用 Google Maps 时，需指定回调函数。 此外，需指定业务逻辑，用于设置它在 UI 线程中单独处理的每个特征的样式。 在 Azure Maps 中，层支持将数据驱动的表达式指定为样式选项。 在呈现时，这些表达式将在单独的线程中处理。 因此，这种做法可以提高呈现性能。 快速呈现较大的数据集时，这项优势就很明显。

以下示例从 USGS 加载过去 7 天内发生的所有地震的 GeoJSON 源， 并在地图上以缩放圆的形式呈现此数据。 每个圆的颜色和比例基于每次地震的震级，此震级数据存储在数据集中每个特征的 `"mag"` 属性中。 如果震级大于或等于 5，则圆为红色。 如果震级大于或等于 3 但小于 5，则圆为橙色。 如果震级小于 3，则圆为绿色。 每个圆的半径是震级的幂乘以 0.1。

**前者：Google Maps**

在 Google Maps 中，可以在 `map.data.setStyle` 方法中指定单个回调函数。 此方法用于将业务逻辑应用到通过 `map.data.loadGeoJson` 方法从 GeoJSON 源加载的每个特征。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**后者：Azure Maps**

GeoJSON 是 Azure Maps 中的基础数据类型，可以使用 `datasource.importFromUrl` 方法轻松将其导入到数据源中。 气泡图层提供基于数据源中特征的属性呈现缩放圆的功能。 它不使用回调函数，而是将业务逻辑转换为表达式，并将其传入样式选项。 表达式定义业务逻辑的工作方式。 可将表达式传入另一个线程，并针对特征数据进行评估。 可将多个数据源和层添加到 Azure Maps，其中每个数据源和层具有不同的业务逻辑。 使用此功能可以通过不同的方式在地图上呈现多个数据集。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**其他资源：**

- [添加符号层](map-add-pin.md)
- [添加气泡层](map-add-bubble-layer.md)
- [聚类点数据](clustering-point-data-web-sdk.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>标记聚类

在地图上可视化大量的数据点时，点将会彼此重叠，使地图显得混乱，难以看清和使用。 可以使用点数据的聚类来改善此用户体验，同时提高性能。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。

以下示例代码加载过去一周的地震数据的 GeoJSON 源，并将其添加到地图。 聚类根据它们所含的点数呈现为缩放的彩色圆。

> [!NOTE]
> 可以使用多种不同的算法进行标记聚类。 Google Maps 和 Azure Maps 使用的算法略有不同。 因此，聚类中的点分布有时可能不同。

**前者：Google Maps**

在 Google Maps 中，可以通过载入 MarkerClusterer 库来聚类标记。 聚类图标限制为使用数字 1 到 5 作为名称的图像，驻留在同一目录中。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 聚类](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，数据由数据源添加和管理。 层连接到数据源并在其中呈现数据。 Azure Maps 中的 `DataSource` 类提供多个聚类选项。

- `cluster` – 告知数据源聚类点数据。
- `clusterRadius` - 要将其中的点聚类到一起的半径（以像素为单位）。
- `clusterMaxZoom` - 进行聚类的最大缩放级别。 如果放大到此级别以上，将以符号形式呈现所有点。
- `clusterProperties` - 定义自定义属性，这些属性是使用表达式针对每个聚类中的、已添加到每个聚类点的属性中的所有点计算的。

启用聚类后，数据源会将已聚类和未聚类的数据点发送到层进行呈现。 数据源能够聚类数十万个数据点。 聚类的数据点包含以下属性：

| 属性名称             | 类型    | 说明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示特征是否表示聚类。 |
| `cluster_id`              | 字符串  | 可与数据源 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法结合使用的群集唯一 ID。 |
| `point_count`             | 数字  | 聚类包含的点数。  |
| `point_count_abbreviated` | 字符串  | 用于缩写过长的 `point_count` 值的字符串。 （例如，将 4,000 缩写为 4K）  |

`DataSource` 类包含以下帮助器函数，用于通过 `cluster_id` 访问有关聚类的其他信息。

| 方法 | 返回类型 | 说明 |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是包含与 ClusteredProperties 匹配的属性的特征。 |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | 计算聚类开始展开或分开的缩放级别。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。 |

在地图上呈现聚类的数据时，最简单的方法通常是使用两个或更多个层。 以下示例使用三个层。 一个气泡层，用于根据聚类的大小绘制缩放的彩色圆。 一个符号层，用于将聚类大小呈现为文本。 此层使用另一个符号层来呈现未聚类的点。 还可以通过其他多种方式呈现聚类数据。 有关详细信息，请参阅[聚类点数据](clustering-point-data-web-sdk.md)文档。

可以使用 `DataSource` 类中的 `importDataFromUrl` 函数，在 Azure Maps 中直接导入 GeoJSON 数据。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 聚类](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**其他资源：**

- [添加符号层](map-add-pin.md)
- [添加气泡层](map-add-bubble-layer.md)
- [聚类点数据](clustering-point-data-web-sdk.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>添加热度地图

热度地图也称为点密度地图，是一种数据可视化效果。 它们使用各种颜色来表示数据密度。 此外，它们经常用于在地图上显示数据“热点”。 热度地图能够很好地呈现较大的点数据集。

以下示例从 USGS 加载过去一个月发生的所有地震的 GeoJSON 源，并将其呈现为加权的热度地图，其中 `"mag"` 属性用作权重。

**前者：Google Maps**

若要在 Google Maps 中创建热度地图，需要通过将 `&libraries=visualization` 添加到 API 脚本 URL 来加载“可视化”库。 Google Maps 中的热度地图层不直接支持 GeoJSON 数据。 需要先下载数据，然后将其转换为加权数据点的数组。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 热度地图](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，将 GeoJSON 数据载入数据源，然后将数据源连接到热度地图层。 可以使用表达式将用于权重的属性传入 `weight` 选项。 可以使用 `DataSource` 类中的 `importDataFromUrl` 函数，在 Azure Maps 中直接导入 GeoJSON 数据。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 热度地图](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**其他资源：**

- [添加热度地图层](map-add-heat-map-layer.md)
- [热度地图层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [热度地图层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>叠加图块层

图块层在 Google Maps 中也称为图像叠加层。 图块层可让你叠加大型图像，这些图像已分解成与地图图块系统相符的较小图块式图像。 这是叠加大型图像或大型数据集的常用方法。

以下示例叠加爱荷华州立大学环境气象网提供的气象雷达图块层。

**前者：Google Maps**

在 Google Maps 中，可以使用 `google.maps.ImageMapType` 类创建图块层。

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps 图块层](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，可将图块层添加到地图中，这类似于添加任何其他层。 指定包含 x 轴、y 轴、缩放比例占位符的带格式 URL；`{x}`、`{y}`、`{z}` 分别用于告知层要在何处访问图块。 Azure Maps 图块层还支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。

> [!TIP]
> 在 Azure Maps 中，可以轻松地在其他层（包括基础地图层）下面呈现层。 通常，最好是在地图标签下面呈现图块层，以便于阅读。 `map.layers.add` 方法采用另一个参数，该参数是要在其中插入新层的层的 ID。 若要在地图标签下面插入图块层，可使用以下代码：`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps 图块层](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> 可以使用地图的 `transformRequest` 选项捕获图块请求。 这样，就可以根据需要修改请求或在其中添加标头。

**其他资源：**

- [添加图块层](map-add-tile-layer.md)
- [图块层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [图块层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>显示交通情况

在 Azure Maps 和 Google Maps 中都可以叠加交通状况数据。

**前者：Google Maps**

在 Google Maps 中，可以使用交通状况层将交通状况数据叠加到地图中。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps 交通状况](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**后者：Azure Maps**

Azure Maps 提供用于显示交通状况的多种不同选项。 交通事件（例如道路封闭和事故）可以作为图标显示在地图上。 交通流量和带颜色编码的道路可以叠加在地图上，并且可以根据公布的限速、正常的预期延误时间或绝对延误时间修改颜色。 Azure Maps 中的事件数据每隔一分钟更新一次，交通流量数据每隔两分钟更新一次。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps 交通状况](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

如果在 Azure Maps 中单击某个交通状况图标，弹出窗口中会显示更多信息。

<center>

![Azure Maps 交通事件](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**其他资源：**

- [在地图上显示交通信息](map-show-traffic.md)
- [交通状况叠加选项](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>添加地面叠加层

Azure Maps 和 Google Maps 都支持在地图上叠加地理围栏图像，以便在平移和缩放地图时移动和缩放这些图像。 在 Google Maps 中，这些图像称为地面叠加层，而在 Azure Maps 中则称为图像层。 它们非常适合用于建筑楼面布置图、叠加旧地图或无人机拍图。

**前者：Google Maps**

在 Google Maps 中创建地面叠加层时，需指定要叠加的图像的 URL，以及用于在地图上限定图像的边界框。 此示例在地图上叠加 [1922 年新泽西州纽瓦克市](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地图图像。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

在浏览器中运行此代码会显示如下图所示的地图：

<center>

![Google Maps 图像叠加](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，可以使用 `atlas.layer.ImageLayer` 类叠加地理围栏图像。 此类需要指定图像的 URL，以及该图像四个角的坐标。 该图像必须位于同一个域中，或已启用 CORs。

> [!TIP]
> 如果只有东南西北坐标和旋转信息，而没有图像每个角的坐标，可以使用静态 [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) 方法。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 图像叠加](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**其他资源：**

- [叠加图像](map-add-image-layer.md)
- [图像层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>其他代码示例

下面是一些与 Google Maps 迁移相关的其他代码示例：

- [绘图工具](map-add-drawing-toolbar.md)
- [将地图限制为两指平移](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [限制滚轮缩放](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [创建全屏控件](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**服务：**

- [使用 Azure Maps 服务模块](how-to-use-services-module.md)
- [搜索兴趣点](map-search-location.md)
- [获取坐标中的信息（反向地理编码）](map-get-information-from-coordinate.md)
- [显示从 A 到 B 的路线](map-route.md)
- [使用 JQuery UI 搜索自动建议](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 到 Azure Maps Web SDK 的类映射

以下附录提供了 Google Maps V3 中的常用类以及 Azure Maps Web SDK 中的等效项的交叉参考。

### <a name="core-classes"></a>核心类

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>叠加类

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | 请参阅[将圆添加到地图](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>服务类

Azure Maps Web SDK 包含一个可单独加载的服务模块。 此模块使用 Web API 包装 Azure Maps REST 服务，可在 JavaScript、TypeScript 和 Node.js 应用程序中使用。

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>库

库将更多功能添加到地图中。 其中的许多功能包含在 Azure Maps 的核心 SDK 中。 下面是可以取代这些 Google Maps 库的等效类

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| 绘图库       | [绘图工具模块](set-drawing-options.md) |
| 几何库      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 可视化库 | [热度地图层](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps Web SDK。

> [!div class="nextstepaction"]
> [如何使用地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服务模块](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用绘图工具模块](set-drawing-options.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)

