---
title: 教程：从必应地图迁移 Web 应用 | Microsoft Azure Maps
description: 如何将 Web 应用从必应地图迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 469565385ce4b3ee4b1589f105216213d584c8c9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319724"
---
# <a name="migrate-a-web-app-from-bing-maps"></a>从必应地图迁移 Web 应用

使用必应地图的 Web 应用通常使用必应地图 V8 JavaScript SDK。 Azure Maps Web SDK 是适合用于迁移目标的基于 Azure 的 SDK。 Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图，以便在 Web 或移动应用程序中显示。 此控件使用 WebGL，因此可以渲染大型数据集，同时保持很高的性能。 使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

如果迁移现有的 Web 应用程序，请检查该应用程序是否使用 Cesium、Leaflet 和 OpenLayers 等开源地图控件库。 如果使用上述某个库，并且你希望继续使用该库，那么可将其连接到 Azure Maps 图块服务（[道路图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)）。 可在下面的链接中详细了解如何在一些常用开源地图控件库中使用 Azure Maps。

-   Cesium - 适用于 Web 的 3D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [文档](https://cesiumjs.org/)
-   Leaflet - 适用于 Web 的轻型 2D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [文档](https://leafletjs.com/)
-   OpenLayers - 支持投影的适用于 Web 的 2D 地图控件。 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [文档](https://openlayers.org/)

如果使用 JavaScript 框架进行开发，则下述某一开源项目可能很有用：

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - 围绕 Azure Maps 的 Angular 10 包装器。
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure Maps Blazor 组件。
- [Azure Maps React 组件](https://github.com/WiredSolutions/react-azure-maps) - Azure Maps 控件的 React 包装器。
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Vue 应用程序的 Azure Maps 组件。

## <a name="key-features-support"></a>重要功能支持

下表列出了必应地图 V8 JavaScript SDK 中的重要 API 功能，以及 Azure Maps Web SDK 中的类似 API 的支持。

| 必应地图功能        | Azure Maps Web SDK 支持                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| 图钉                 | ✓                                                                                      |
| 图钉聚类       | ✓                                                                                      |
| 折线和多边形     | ✓                                                                                      |
| 地面叠层          | ✓                                                                                      |
| 热度地图                | ✓                                                                                      |
| 图块层              | ✓                                                                                      |
| KML 层                | ✓                                                                                      |
| 等高线层            | [示例](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| 数据装箱层       | [示例](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| 动态图块层      | 包含在开源 Azure Maps [动画模块](https://github.com/Azure-Samples/azure-maps-animations)中 |
| 绘图工具            | ✓                                                                                      |
| 地理编码器服务         | ✓                                                                                      |
| 方向服务       | ✓                                                                                      |
| 距离矩阵服务  | ✓                                                                                      |
| 空间数据服务     | 空值                                                                                    |
| 卫星/航拍图像 | ✓                                                                                      |
| 鸟瞰图像         | 已计划                                                                                |
| 街景图像       | 已计划                                                                                |
| GeoJSON 支持          | ✓                                                                                      |
| GeoXML 支持           | ✓                                                                                      |
| 已知文本支持  | ✓                                                                                      |
| 自定义地图样式        | 部分                                                                                |

Azure Maps 还具有其他很多[用于 Web SDK 的开源模块](open-source-projects.md#open-web-sdk-modules)，这些模块扩展了它的功能。

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK 中的显著差异

下面是必应地图与 Azure Maps Web SDK 之间需要注意的一些重要区别：

-   除了提供托管终结点用于访问 Azure Maps Web SDK 以外，还可以根据偏好使用某个 NPM 包将 Web SDK 嵌入应用。 有关详细信息，请参阅此[文档](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。 此程序包还包括了 TypeScript 定义。
-   必应地图提供了其 SDK 的两个托管分支：发布和实验。 在进行新的开发时，实验分支每天可能会收到多个更新。 Azure Maps 仅托管发布分支，但实验功能在开源 Azure Maps 代码示例项目中作为自定义模块进行创建。 必应地图之前有一个冻结的分支，该分支更新频率较低，因此降低了因发布而出现中断性变更的风险。 在 Azure Maps 中可使用 NPM 模块，并指向之前任何的次要版本。

> [!TIP]
> Azure Maps 同时发布了 SDK 的简化版和非简化版本。 只需从文件名中删除 `.min`。 在调试问题时，非简化版本非常有用，但请务必在生产环境中使用简化版本来利用更小的文件大小。

-   在 Azure Maps 中创建 Map 类的实例后，代码应等待激发地图 `ready` 或 `load` 事件，然后与地图交互。 这些事件可确保所有地图资源均已加载且准备好可供访问。
-   这两个平台为基础地图使用类似的图块系统，但必应地图中图块的尺寸为 256 像素，而 Azure Maps 中图块的尺寸为 512 像素。 因此，若要在 Azure Maps 中获得与必应地图相同的地图视图，在必应地图中使用的缩放级别在 Azure Maps 中需要减 1。
-   必应地图中的坐标称为 `latitude, longitude`，而 Azure Maps 使用 `longitude, latitude`。 此格式符合大多数 GIS 平台所遵循的标准 `[x, y]`。

-   Azure Maps Web SDK 中的形状基于 GeoJSON 架构。 帮助器类通过 [atlas.data 命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)公开。 还有 [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 类可用于包装 GeoJSON 对象，使它们可以数据可绑定的方式轻松更新和维护。
-   Azure Maps 中的坐标定义为 Position 对象，可将这些对象指定为采用 `[longitude, latitude]` 或 `new atlas.data.Position(longitude, latitude)` 格式的简单数字数组。

> [!TIP]
> Position 类有一个静态帮助程序函数，用于导入 `latitude, longitude` 格式的坐标。 [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position) 函数常常可取代必应地图代码中的 `new Microsoft.Maps.Location` 函数。

-   Azure Maps 将样式与数据相区分，而不是在添加到地图的每个形状中指定样式信息。 数据存储在数据源中，并连接到 Azure Maps 代码用来呈现数据的呈现层。 此方法提供增强的性能优势。 此外，很多层支持数据驱动的样式，其中，业务逻辑可添加到层样式选项，这些选项可以更改单个形状根据形状中定义的属性在层中的呈现方式。
-   Azure Maps 在 `atlas.math` 命名空间中提供了一组有用的空间数学函数，但这些函数不同于必应地图空间数学模块中的函数。 主要区别是 Azure Maps 不为二元运算（例如并集和交集）提供内置函数，但由于 Azure Maps 是基于开放标准 GeoJSON，因此有许多可用的开源库。 一个与 Azure Maps 配合良好且提供海量空间数学功能的常用选项是 [turf js](http://turfjs.org/)。

另请参阅 [Azure Maps 术语表](https://docs.microsoft.com/azure/azure-maps/glossary)，获取与 Azure Maps 关联的术语的详细列表。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 对比示例

下面是适用于每个平台的代码示例集合，其中涵盖了常见用例，可帮助你将 Web 应用程序从必应地图 V8 JavaScript SDK 迁移到 Azure Maps Web SDK。 与 Web 应用程序相关的代码示例以 JavaScript 提供；但是，Azure Maps 还通过 [NPM 模块](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)提供 TypeScript 定义作为附加选项。

**主题**

- [加载地图](#load-a-map)
- [本地化地图](#localizing-the-map)
- [设置地图视图](#setting-the-map-view)
- [添加图钉](#adding-a-pushpin)
- [添加自定义图钉](#adding-a-custom-pushpin)
- [添加折线](#adding-a-polyline)
- [添加多边形](#adding-a-polygon)
- [显示信息框](#display-an-infobox)
- [图钉聚类](#pushpin-clustering)
- [添加热度地图](#add-a-heat-map)
- [叠加图块层](#overlay-a-tile-layer)
- [显示交通情况数据](#show-traffic-data)
- [添加地面叠加层](#add-a-ground-overlay)
- [将 KML 数据添加到地图](#add-kml-data-to-the-map)
- [添加绘图工具](#add-drawing-tools)


### <a name="load-a-map"></a>加载地图

在这两个 SDK 中加载地图需遵循相同的一组步骤；

-   添加对 Map SDK 的引用。
-   将充当地图占位符的 `div` 标记添加到页面的正文。
-   创建加载页面时要调用的 JavaScript 函数。
-   创建相应 map 类的实例。

**一些重要差别**

-   必应地图需要在 API 的脚本引用中指定帐户密钥或将其指定为 map 选项。 Azure Maps 的身份验证凭据指定为 map 类的选项。 它可以是订阅密钥或 Azure Active Directory 信息。
-   必应地图在 API 的脚本引用中采用一个回调函数，该函数用于调用初始化函数来加载地图。 使用 Azure Maps 时，应使用页面的 onload 事件。
-   当使用 ID 引用将在其中呈现地图的 `div` 元素时，必应地图使用 HTML 选择器（即 `#myMap`），而 Azure Maps 仅使用 ID 值（即 `myMap`）。
-   Azure Maps 中的坐标定义为 Position 对象，可将这些对象指定为采用 `[longitude, latitude]` 格式的简单数字数组。
-   Azure Maps 中的缩放级别比必应地图示例低一级，原因是这两个平台之间的图块系统大小有差异。
-   默认情况下，Azure Maps 不会将任何导航控件（例如缩放按钮和地图样式按钮）添加到地图画布。 但是，有一些控件可用于添加地图样式选取器、缩放按钮、罗盘或旋转控件以及俯仰角控件。
-   Azure Maps 中添加了一个事件处理程序用于监视地图实例的 `ready` 事件。 在地图加载完 WebGL 上下文以及所需的所有资源后，将激发此事件。 任何加载后的代码可添加到此事件处理程序中。

以下示例演示如何加载基本地图，使该地图在 Google Maps 中的中心点位于纽约，坐标为（经度：-73.985，纬度：40.747），必应地图中的缩放级别为 12。

**前者：必应地图**

以下代码示例演示如何显示在某个位置居中并缩放的必应地图。

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在浏览器中运行此代码会显示如下图所示的地图：

<center>

![必应地图地图](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
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

![Azure Maps 地图](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

在[此处](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)可以找到有关如何在 Web 应用中设置和使用 Azure Maps 地图控件的详细文档。

> [!TIP]
> Azure Maps 同时发布了 SDK 的简化版和非简化版本。 从文件名中删除 `.min`。 在调试问题时，非简化版本非常有用，但请务必在生产环境中使用简化版本来利用更小的文件大小。

**其他资源**

-   Azure Maps 还提供了导航控件，用于按[此文](https://docs.microsoft.com/azure/azure-maps/map-add-controls)所述旋转地图视图及调整其俯仰角。

### <a name="localizing-the-map"></a>本地化地图

如果你的受众遍布多个国家/地区或讲不同的语言，则本地化就非常重要。

**前者：必应地图**

若要本地化必应地图，请使用 `setLang` 指定语言和区域，并将 `UR` 参数添加到 API 的 `<script>` 标记引用中。 必应地图中的某些功能仅在某些市场中可用，因为用户的那些市场是使用 `setMkt` 参数指定的。

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

下面是将语言设置为“fr-FR”的必应地图示例。

<center>

![本地化必应地图地图](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**后者：Azure Maps**

Azure Maps 仅提供设置地图的语言和区域视图的选项。 市场参数不用于限制功能。 可通过两种不同的方式来设置地图的语言和区域视图。 第一种做法是将此信息添加到全局 `atlas` 命名空间，使应用中的所有地图控件实例默认采用这些设置。 以下示例将语言设置为法语（“fr-FR”），将区域视图设置为 `"auto"`：

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
> 使用 Azure Maps 时，可以在同一页面上加载使用不同语言和区域设置的多个地图实例。 此外，还可以在加载地图后在其中更新这些设置。 在[此处](https://docs.microsoft.com/azure/azure-maps/supported-languages)可以找到 Azure Maps 支持的语言的详细列表。

下面是将语言设置为“fr”、将用户区域设置为“fr-FR”的 Azure Maps 示例。

<center>

![本地化 Azure Maps 地图](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>设置地图视图

可调用 JavaScript 中的相应函数，以编程方式将必应地图和 Azure Maps 中的动态地图移到新的地理位置。 以下示例演示如何在 Google Maps 中使地图显示卫星航拍图像，并将地图中心点置于坐标（经度：-111.0225，纬度：35.0272），并将必应地图中的缩放级别更改为 15。

> [!NOTE]
> 必应地图使用尺寸为 256 像素的图块，而 Azure Maps 则使用更大的 512 像素的图块。 在加载相同的地图区域时，与必应地图相比，这可减少 Azure Maps 所需的网络请求数。 但是，由于地图控件中图块金字塔的工作方式，Azure Maps 中更大的图块意味着在使用 Azure Maps 时，若要实现与必应地图中的地图相同的可视区域，需要将必应地图中使用的缩放级别减 1。

**前者：必应地图**

可使用 `setView` 函数以编程方式移动必应地图地图控件，此函数允许指定地图的中心点和缩放级别。

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![必应地图设置地图视图](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，可使用地图的 `setCamera` 函数以编程方式更改地图位置，可使用 `setStyle` 函数更改地图样式。 请注意，Azure Maps 中的坐标采用“longitude, latitude”格式，缩放级别值需减 1。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Azure Maps 设置地图视图](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**其他资源**

-   [选择地图样式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
-   [支持的地图样式](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)

### <a name="adding-a-pushpin"></a>添加图钉

在 Azure Maps 中，可通过多种方式在地图上呈现点数据；

-   HTML 标记 – 使用传统的 DOM 元素呈现点。 HTML 标记支持拖动。
-   符号层 – 在 WebGL 上下文中使用图标和/或文本呈现点。
-   气泡层 – 在地图上以圆的形式呈现点。 可根据数据中的属性缩放圆的半径。

符号层和气泡层都在 WebGL 上下文中呈现，并且能够在地图上呈现极大的点集。 这些层要求将数据存储在数据源中。 激发 `ready` 事件后，应将数据源和呈现层添加到地图中。 HTML 标记在页面中呈现为 DOM 元素，且不使用数据源。 页面中的 DOM 元素越多，页面加载速度越慢。 若要在地图上呈现好几百个点，则我们建议改用某个呈现层。

下面的示例将一个标记添加到地图上的（经度：-0.2，纬度：51.5）位置，并使用叠加的数字 10 作为标签。

**前者：必应地图**

在必应地图中，使用 `Microsoft.Maps.Pushpin` 类将标记添加到地图中*。 然后，使用下面两个函数的其中一个将图钉添加到地图中。

第一个函数用于创建层，将图钉插入到该层，然后将该层添加到地图的 `layers` 属性。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

第二个函数使用地图的 `entities` 属性添加图钉。 此函数在必应地图 V8 的文档中标记为“已弃用”，但它仍保留了部分功能可用于基本场景。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![必应地图添加图钉](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**后者：使用 HTML 标记的 Azure Maps**

在 Azure Maps 中，HTML 标记可用于在地图上轻松显示某个点；对于只需在地图上显示少量点的简单应用，建议使用此类标记。 若要使用 HTML 标记，请创建 `atlas.HtmlMarker` 类的实例，设置文本和位置选项，然后使用 `map.markers.add` 函数将标记添加到地图。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps 添加标记](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**后者：使用符号层的 Azure Maps**

使用符号层时，必须将数据添加到数据源，并将数据源附加到层。 此外，在 `ready` 事件激发后，应将数据源和层添加到地图。 若要在符号上方呈现唯一的文本值，需将文本信息存储为数据点的属性，并在层的 `textField` 选项中引用该属性。 这比使用 HTML 标记要略微繁琐一些，但可以提供许多性能优势。

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

![Azure Maps 添加符号层](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**其他资源**

-   [创建数据源](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [添加符号层](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [添加气泡层](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [聚类点数据](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [添加 HTML 标记](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [符号层图标选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [符号层文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML 标记类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML 标记选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>添加自定义图钉

可使用自定义图像来表示地图上的点。 下图在以下示例中使用，它使用自定义图像在地图上的（纬度：51.5，经度：-0.2）位置显示一个点，并偏移标记的位置，使图钉图标的点与地图上的正确位置对齐。

| ![Azure Maps 添加图钉](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**前者：必应地图**

在必应地图中，通过将图像的 URL 传递到图钉的 `icon` 选项来创建自定义标记。 `anchor` 选项用于将图钉图像的点与地图上的坐标对齐。 必应地图中的定位点值相对于图像的左上角。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![必应地图添加自定义图钉](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**后者：使用 HTML 标记的 Azure Maps**

若要在 Azure Maps 中自定义 HTML 标记，可将 HTML `string` 或 `HTMLElement` 传入该标记的 `htmlContent` 选项。 在 Azure Maps 中，`anchor` 选项用于通过九个已定义的参照点之一，来指定标记相对于位置坐标的位置：“center”、“top”、“bottom”、“left”、“right”、“top-left”、“top-right”、“bottom-left”、“bottom-right” 默认情况下，内容将定位并设置为“bottom”，即 html 内容的中下方。 若要更轻松地从必应地图迁移代码，请将定位点设置为“top-left”，然后将 `offset` 选项与必应地图中所用的相同偏移量结合使用。 Azure Maps 中的偏移方向与必应地图中相反，因此需将偏移量乘以 -1。

> [!TIP]
> 在显示不需要的图标的 MS Edge 中，添加 `pointer-events:none` 作为 HTML 内容的样式可禁用默认拖放行为。

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps 添加自定义标记](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**后者：使用符号层的 Azure Maps**

Azure Maps 中的符号层也支持自定义图像，但需要先将图像载入地图资源，并为其分配唯一的 ID。 然后，符号层可以引用此 ID。 可以使用图标 `offset` 选项来偏移符号，使之与图像上的正确点对齐。 在 Azure Maps 中，`anchor` 选项用于通过九个已定义的参照点之一，来指定符号相对于位置坐标的位置：“center”、“top”、“bottom”、“left”、“right”、“top-left”、“top-right”、“bottom-left”、“bottom-right” 默认情况下，内容将定位并设置为“bottom”，即 HTML 内容的中下方。 若要更轻松地从必应地图迁移代码，请将定位点设置为“top-left”，然后将 `offset` 选项与必应地图中所用的相同偏移量结合使用。 Azure Maps 中的偏移方向与必应地图中相反，因此需将偏移量乘以 -1。

```javascript
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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

![必应地图添加自定义符号层](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> 若要创建点的高级自定义呈现，请结合使用多个呈现层。 例如，若要使用多个图钉，而这些图钉在不同的彩色圆上使用相同的图标，则不需要针对每种颜色创建大量的图像，而可以在气泡层上叠加一个符号层，并使该符号层引用同一个数据源。 这比创建大量不同的图像并让地图维护这些图像要高效得多。

**其他资源**

-   [创建数据源](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [添加符号层](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [添加 HTML 标记](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [符号层图标选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [符号层文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML 标记类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML 标记选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>添加折线

折线用于表示地图上的线条或路径。 下面的示例演示如何在地图上创建虚线折线。

**前者：必应地图**

在必应地图中，Polyline 类包含位置数组和一组选项。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![必应地图折线](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，折线指的是更常见的地理空间术语 `LineString` 或 `MultiLineString` 对象。 可将这些对象添加到数据源，并使用线条层来呈现。 各平台之间的笔划颜色、宽度和虚线数组选项几乎相同。

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

![Azure Maps 线条](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**其他资源**

-   [将线条添加到地图](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-lines-to-the-map)
-   [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="adding-a-polygon"></a>添加多边形

多边形用于表示地图上的某个区域。 Azure Maps 和必应地图为多边形提供非常类似的支持。 下面的示例演示如何创建一个多边形，该多边形基于地图的中点坐标构成一个三角形。

**前者：必应地图**

在必应地图中，Polygon 类包含坐标或坐标环的数组和一组选项。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![必应地图多边形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，可将 Polygon 和 MultiPolygon 对象添加到数据源，并使用层在地图上呈现这些对象。 可以在多边形层中呈现多边形的区域。 可以使用线条层呈现多边形的轮廓。

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

![Azure Maps 多边形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**其他资源**

-   [将多边形添加到地图](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-polygon-to-the-map)
-   [将圆添加到地图](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-circle-to-the-map)
-   [多边形层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="display-an-infobox"></a>显示信息框

在必应地图中，实体的其他信息可作为 `Microsoft.Maps.Infobox` 类显示在地图上；在 Azure Maps 中，可使用 `atlas.Popup` 类实现此目的。 下面的示例向地图添加图钉/标记，并在单击时显示信息框/弹出窗口。

**前者：必应地图**

在必应地图中，信息框是使用 `Microsoft.Maps.Infobox` 构造函数创建的。

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![必应地图信息框](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，弹出窗口可用于显示某个位置的附加信息。 可将 HTML `string` 或 `HTMLElement` 对象传入弹出窗口的 `content` 选项。 弹出窗口可根据需要独立于任何形状显示，因而需要指定 `position` 值。 若要显示弹出窗口，请调用 `open` 函数，并传入要在其中显示弹出窗口的地图。

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
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

![Azure Maps 弹出窗口](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> 若要使用符号、气泡、线条或多边形层实现相同的目的，请将该层传入地图事件代码而不是标记。

**其他资源**

-   [添加弹出项](https://docs.microsoft.com/azure/azure-maps/map-add-popup)
-   [包含媒体内容的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [包含形状的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [重复使用包含多个图钉的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [弹出窗口类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)
-   [弹出窗口选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>图钉聚类

在地图上可视化大量的数据点时，点将会彼此重叠，使地图显得混乱，难以看清和使用。 可以使用点数据的聚类来改善此用户体验，同时提高性能。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。

下面的示例加载过去一周的地震数据的 GeoJSON 源，并将其添加到地图。 聚类根据它们所含的点数呈现为缩放的彩色圆。

> [!NOTE]
> 可使用多种不同的算法进行图钉聚类。 必应地图使用简单的基于网格的函数，而 Azure Maps 使用更高级、更具视觉吸引力的基于点的聚类方法。

**前者：必应地图**

在必应地图中，可使用 GeoJSON 模块加载 GeoJSON 数据。 可通过在聚类模块中加载并使用图钉所包含的聚类层来对图钉进行聚类。

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![必应地图聚类](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，数据由数据源添加和管理。 层连接到数据源并在其中呈现数据。 Azure Maps 中的 `DataSource` 类提供多个聚类选项。

-   `cluster` – 告知数据源聚类点数据。 
-   `clusterRadius` - 要将其中的点聚类到一起的半径（以像素为单位）。
-   `clusterMaxZoom` - 进行聚类的最大缩放级别。 如果放大到此级别以上，将以符号形式呈现所有点。
-   `clusterProperties` - 定义自定义属性，这些属性是使用表达式针对每个聚类中的、已添加到每个聚类点的属性中的所有点计算的。

启用聚类后，数据源会将已聚类和未聚类的数据点发送到层进行呈现。 数据源能够聚类数十万个数据点。 聚类的数据点包含以下属性：

| 属性名称               | 类型    | 说明                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | 指示特征是否表示聚类。     |
| `cluster_id`                | 字符串  | 可与 `DataSource` 类 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 函数结合使用的群集唯一 ID。 |
| `point_count`               | 数字  | 聚类包含的点数。     |
| `point_count_abbreviated`   | 字符串  | 用于缩写过长的 `point_count` 值的字符串。 （例如，将 4,000 缩写为 4K） |

`DataSource` 类包含以下帮助器函数，用于通过 `cluster_id` 访问有关聚类的其他信息。

| 函数       | 返回类型        | 说明     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是具有与群集属性匹配的属性的特征。 |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | 计算聚类开始展开或分开的缩放级别。    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。    |

在地图上呈现聚类的数据时，最简单的方法通常是使用两个或更多个层。 下面的示例使用 3 个层：气泡层用于根据聚类的大小绘制缩放的彩色圆，一个符号层将聚类大小呈现为文本，另一个符号层用于呈现未聚类的点。 [聚类点数据](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)文档中重点介绍了在 Azure Maps 中呈现聚类数据的其他多种方式。

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

![Azure Maps 聚类](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**其他资源**

-   [添加符号层](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [添加气泡层](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [聚类点数据](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="add-a-heat-map"></a>添加热度地图

热度地图，也称为点密度地图，是一种数据可视化类型，它们使用一系列颜色来表示数据密度。 它们通常用于在地图上显示数据“热点”，是呈现大型点数据集的好方法。

下面的示例从 USGS 加载过去一个月发生的所有地震的 GeoJSON 源，并将其呈现为热图。

**前者：必应地图**

在必应地图中，若要创建热图，请在热图模块中加载。 同样，加载 GeoJSON 模块以添加对 GeoJSON 数据的支持。

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![必应地图热图](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，将 GeoJSON 数据载入数据源，然后将数据源连接到热度地图层。 可以使用 `DataSource` 类中的 `importDataFromUrl` 函数，在 Azure Maps 中直接导入 GeoJSON 数据。

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Azure Maps 热图](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**其他资源**

-   [添加热度地图层](https://docs.microsoft.com/azure/azure-maps/map-add-heat-map-layer)
-   [热度地图层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [热度地图层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [使用数据驱动样式表达式](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="overlay-a-tile-layer"></a>叠加图块层

图块层可让你叠加大型图像，这些图像已分解成与地图图块系统相符的较小图块式图像。 这是叠加大型图像或超大型数据集的常用方法。

下面的示例叠加爱荷华州立大学环境气象网提供的气象雷达图块层，该层使用“X 轴、Y 轴和缩放平铺”命名架构。

**前者：必应地图**

在必应地图中，可使用 `Microsoft.Maps.TileLayer` 类创建图块层。

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![必应地图加权热图](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，基本上可以像添加任何其他层一样将图块层添加到地图中。 指定包含 x 轴、y 轴、缩放比例占位符的带格式 URL；`{x}`、`{y}`、`{z}` 分别用于告知层要在何处访问图块。 Azure Maps 图块层还支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。

> [!TIP]
> 在 Azure Maps 中，可以轻松地在其他层（包括基础地图层）下面呈现层。 通常，最好是在地图标签下面呈现图块层，以便于阅读。 `map.layers.add` 函数采用另一个参数，该参数是要在下面插入新层的另一个层的 ID。 若要在地图标签下面插入图块层，可使用以下代码：
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps 加权热图](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> 可以使用地图的 `transformRequest` 选项捕获图块请求。 这样，就可以根据需要修改请求或在其中添加标头。

**其他资源**

-   [添加图块层](https://docs.microsoft.com/azure/azure-maps/map-add-tile-layer)
-   [图块层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [图块层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>显示交通情况数据

在必应地图和 Azure Maps 中都可叠加交通状况数据。

**前者：必应地图**

在必应地图中，可使用交通状况模块将交通状况数据叠加到地图中。

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![必应地图交通状况](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**后者：Azure Maps**

Azure Maps 提供用于显示交通状况的多种不同选项。 交通事件（例如道路封闭和事故）可以作为图标显示在地图上。 交通流量和带颜色编码的道路可以叠加在地图上，并且可以根据公布的限速、正常的预期延误时间或绝对延误时间修改颜色。 Azure Maps 中的事件数据每一分钟更新一次，交通流量数据每 2 分钟更新一次。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps 交通状况](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

如果在 Azure Maps 中单击某个交通状况图标，弹出窗口中会显示更多信息。

<center>

![Azure Maps 交通状况弹出窗口](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**其他资源**

-   [在地图上显示交通信息](https://docs.microsoft.com/azure/azure-maps/map-show-traffic)
-   [交通状况叠加选项](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [交通控制](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>添加地面叠加层

必应地图和 Azure Maps 都支持在地图上叠加地理围栏图像，以便在平移和缩放地图时移动和缩放这些图像。 在必应地图中，这些图像称为地面叠加层，而在 Azure Maps 中则称为图像层。 它们非常适合用于建筑楼面布置图、叠加旧地图或无人机拍图。

**前者：必应地图**

在必应地图中创建地面叠加层时，需指定要叠加的图像的 URL，以及用于在地图上限定图像的边界框。 此示例在地图上叠加 [1922 年新泽西州纽瓦克市](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地图图像。

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在浏览器中运行此代码会显示如下图所示的地图：

<center>

![必应地图地面叠加层](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，可以使用 `atlas.layer.ImageLayer` 类叠加地理围栏图像。 此类需要指定图像的 URL，以及该图像四个角的坐标。 该图像必须位于同一个域中，或已启用 CORs。

> [!TIP]
> 如果只有东南西北坐标和旋转信息，而没有图像每个角的坐标，可使用静态 [atlas.layer.ImageLayer.getCoordinatesFromEdges](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) 函数。

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Azure Maps 地面叠加层](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**其他资源**

-   [叠加图像](https://docs.microsoft.com/azure/azure-maps/map-add-image-layer)
-   [图像层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>将 KML 数据添加到地图

Azure Maps 和必应地图均可导入 KML、KMZ、GeoRSS、GeoJSON 和已知文本 (WKT) 数据并在地图上呈现它们。 Azure Maps 还支持 GPX、GML、空间 CSV 文件、Web 地图服务 (WMS)、Web 地图图块服务 (WMTS) 和 Web 特征服务 (WFS)。

**前者：必应地图**

在浏览器中运行此代码会显示如下图所示的地图：

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![必应地图 kml](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，GeoJSON 是在 Web SDK 中使用的主要数据格式，可在其中使用 [空间 IO 模块](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)轻松集成其他空间数据格式。 此模块提供读取和写入空间数据的功能，并且包含一个简单的数据层，可轻松地从上述任何空间数据格式呈现数据。 若要读取空间数据文件中的数据，请在 `atlas.io.read` 函数中传入一个 URL，或者传入字符串或 Blob 形式的原始数据。 这会返回该文件中的所有已分析数据，然后可将这些数据添加到地图中。 KML 比大多数空间数据格式要复杂一些，因为它包含的样式信息要多得多。  `SpatialDataLayer` 类支持呈现其中的大多数样式，但是在加载特征数据之前必须先将图标图像载入地图，并将地面叠加层作为图层单独添加到地图中。 通过 URL 加载数据时，应将数据托管在支持 CORs 的终结点上，或者应该将代理服务作为一个选项传入 read 函数中。

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
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

![Azure Maps kml](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**其他资源**

-   [atlas.io.read 函数](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>添加绘图工具

必应地图和 Azure Maps 都提供一个模块；通过它，用户可使用鼠标或其他输入设备在地图上绘制和编辑形状。 它们都支持绘制图钉、线条和多边形。 Azure Maps 还提供用于绘制圆形和矩形的选项。

**前者：必应地图**

在必应地图中，使用 `Microsoft.Maps.loadModule` 函数加载 `DrawingTools` 模块。 加载后，可创建 DrawingTools 类的实例，并调用 `showDrawingManager` 函数将工具栏添加到地图中。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![必应地图绘图工具](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，需要通过加载要在应用中引用的 JavaScript 和 CSS 文件来加载绘图工具模块。 加载地图后，可创建 `DrawingManager` 类的实例，并附加 `DrawingToolbar` 实例。

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Azure Maps 绘图工具](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> 在 Azure Maps 层中，绘图工具提供了多种方式可供用户用来绘制形状。 例如，在绘制多边形时，用户可单击以添加每个点，或按住鼠标左键并拖动鼠标来绘制路径。 可使用 `DrawingManager` 的 `interactionType` 选项来对其进行修改。

**其他资源**

-   [文档](https://docs.microsoft.com/azure/azure-maps/set-drawing-options)
-   [代码示例](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>后续步骤

请查看[开源 Azure Maps Web SDK 模块](open-source-projects.md#open-web-sdk-modules)。 这些模块提供了海量附加功能，并且可完全自定义。

请查看与迁移其他必应地图功能相关的代码示例：

**数据可视化**

> [!div class="nextstepaction"]
> [等高线层](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [数据装箱](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**服务**

> [!div class="nextstepaction"]
> [使用 Azure Maps 服务模块](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [搜索兴趣点](https://docs.microsoft.com/azure/azure-maps/map-search-location)

> [!div class="nextstepaction"]
> [获取坐标中的信息（反向地理编码）](https://docs.microsoft.com/azure/azure-maps/map-get-information-from-coordinate)

> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [使用 JQuery UI 搜索自动建议](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

详细了解 Azure Maps Web SDK。

> [!div class="nextstepaction"]
> [如何使用地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服务模块](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用绘图工具模块](set-drawing-options.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure Maps Web SDK 服务 API 参考文档](https://docs.microsoft.com/javascript/api/azure-maps-control/)
