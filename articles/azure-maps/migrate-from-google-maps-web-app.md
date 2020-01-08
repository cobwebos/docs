---
title: 从 Google Maps 迁移 web 应用 |Microsoft Docs
description: 本教程介绍如何将 web 应用从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562164"
---
# <a name="migrate-a-web-app-from-google-maps"></a>从 Google Maps 迁移 web 应用

使用 Google maps 的大多数 web 应用都使用 Google Maps V3 JavaScript SDK。 Azure Maps Web SDK 是要迁移到的基于 Azure 的合适 SDK。 Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图，以便在 Web 或移动应用程序中显示。 此控件使用 WebGL，因此可以渲染大型数据集，同时保持很高的性能。 使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

如果迁移现有 web 应用程序，请检查其是否正在使用开源地图控件库，如 Cesium、Leaflet 和 OpenLayers。 如果你不想使用 Azure Maps Web SDK，迁移应用程序的另一种方法是继续使用开源地图控件，并将其连接到 Azure Maps 磁贴服务（\|[卫星磁](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)贴的[公路磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)）。 下面是有关如何在某些常用的开源地图控件库中使用 Azure Maps 的详细信息。

- Cesium-用于 web 的3D 地图控件。 \|[文档](https://cesiumjs.org/)的[代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS)
- Leaflet-web 的轻型二维地图控件。 \|[文档](https://leafletjs.com/)的[代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS)
- OpenLayers-支持投影的 web 的二维地图控件。 \|[文档](https://openlayers.org/)的[代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers)

## <a name="key-features-support"></a>主要功能支持

下表列出了 Google Maps V3 JavaScript SDK 中的关键 API 功能，以及 Azure Maps Web SDK 中的类似 API 的支持。

| Google Maps 功能     | Azure Maps Web SDK 支持 |
|-------------------------|:--------------------------:|
| 标记                 | ✓                          |
| 标记聚类       | ✓                          |
| 折线 & 多边形    | ✓                          |
| 数据层             | ✓                          |
| 地面叠加         | ✓                          |
| 热图               | ✓                          |
| 图块层             | ✓                          |
| KML 层               | ✓                          |
| 绘图工具           | ✓                          |
| Geocoder 服务        | ✓                          |
| 行车服务      | ✓                          |
| 距离矩阵服务 | ✓                          |
| 提升服务       | 已计划                    |

## <a name="notable-differences-in-the-web-sdks"></a>Web Sdk 中的显著差异

下面是 Google Maps 与 Azure Maps Web Sdk 之间的一些主要区别：

- 除了提供用于访问 Azure Maps Web SDK 的托管终结点外，还可以使用 NPM 包将 Web SDK 嵌入应用程序（如果需要）。 有关详细信息，请参阅此[文档](how-to-use-map-control.md)。 此程序包还包括了 TypeScript 定义。
- 在 Azure Maps 中创建 Map 类的实例后，代码应等待映射 `ready` 或 `load` 事件，然后才能与映射进行交互。 这将确保所有映射资源已加载并准备好进行访问。
- 这两个平台为基本地图使用类似的平铺系统，但 Google Maps 中的磁贴在 dimension 中为256像素，而 Azure Maps 中的磁贴是维度中的512像素。 因此，若要在 Azure Maps 中获取与 Google Maps 相同的地图视图，则在 Google Maps 中使用的缩放级别需要在 Azure Maps 中减去一。
- Google Maps 中的坐标称为 "纬度，经度"，而 Azure Maps 使用 "经度，纬度"。 这与标准 `[x, y]` （后跟大多数 GIS 平台）一致。
- Azure Maps Web SDK 中的形状基于 GeoJSON 架构。 Helper 类通过[*阿特拉斯. 数据*命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)公开。 还有一个[*塔。Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)类，可用于包装 GeoJSON 的对象，并使其易于以数据可绑定的方式进行更新和维护。
- Azure Maps 中的坐标定义为 Position 对象，可将其指定为简单数字数组，格式为 `[longitude, latitude]` 或新的
    > [!TIP]
    > Position 类具有静态帮助器方法，用于导入 "纬度，经度" 格式的坐标。 [FromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)方法通常可以替换 Google Maps 代码中的 `new google.maps.LatLng` 方法。
- 不是在添加到地图的每个形状上指定样式信息，Azure Maps 将样式与数据分隔开。 数据存储在数据源中，并连接到 Azure Maps 代码用于呈现数据的呈现层。 此方法可提高性能。 此外，很多层支持数据驱动样式，其中，业务逻辑可以添加到层样式选项，这些选项将更改各个形状在层中的呈现方式，这是基于在形状中定义的属性。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 并排示例

下面是每个平台的代码示例集合，涵盖了常见用例，可帮助你将 web 应用程序从 Google Maps V3 JavaScript SDK 迁移到 Azure Maps Web SDK。 与 web 应用程序相关的代码示例在 JavaScript 中提供;不过，Azure Maps 还通过[NPM 模块](how-to-use-map-control.md)提供了 TypeScript 定义作为附加选项。

### <a name="load-a-map"></a>加载映射

在这两个 SDK 中加载地图都遵循相同的一组步骤：

- 添加对地图 SDK 的引用。
- 将 `div` 标记添加到将充当地图占位符的页面的正文中。
- 创建一个在加载页面时调用的 JavaScript 函数。
- 创建相应 map 类的实例。

**一些主要区别**

- Google maps 需要在 API 的脚本引用中指定帐户密钥。 Azure Maps 的身份验证凭据指定为 map 类的选项。 这可以是订阅密钥或 Azure Active Directory 信息。
- Google Maps 采用 API 的脚本引用中的回调函数，该函数用于调用初始化函数来加载映射。 使用 Azure Maps 应使用页面的 onload 事件。
- 在引用将在其中呈现映射的 `div` 元素时，Azure Maps 中的 `Map` 类只需要 `id` 值，而 Google Maps 需要 `HTMLElement` 对象。
- Azure Maps 中的坐标定义为 Position 对象，可将其指定为 `[longitude, latitude]`格式的简单数字数组。
- 由于在平台之间平铺系统大小的差异，Azure Maps 中的缩放级别比 Google Maps 示例小一级。
- 默认情况下，Azure Maps 不会将任何导航控件添加到地图画布，如缩放按钮和地图样式按钮。 但有一些用于添加地图样式选取器、缩放按钮、罗盘或旋转控件和螺距控件的控件。
- 在 Azure Maps 中添加了一个事件处理程序，用于监视 map 实例的 `ready` 事件。 当映射完成加载 WebGL 上下文和所需的所有资源时，将激发此情况。 可以在此事件处理程序中添加任何 post load 代码。

下面的示例演示如何加载基本地图，如位于纽约的坐标（经度：-73.985，纬度：40.747），在 Google Maps 中的缩放级别为12。

**之前： Google Maps**

下面的代码是一个示例，演示如何在某个位置居中和缩放某个 Google 地图。

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

在浏览器中运行此代码将显示如下图所示的地图：

<center>

![简单的 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**后： Azure Maps**

下面的代码演示如何在 Azure Maps 中加载具有相同视图的地图以及地图样式控件和缩放按钮。

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

在浏览器中运行此代码将显示如下图所示的地图：

<center>

![简单 Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

可在[此处](how-to-use-map-control.md)找到有关如何设置和使用 web 应用中的 Azure Maps map control 的详细文档。

> [!NOTE]
> 与 Google Maps 不同，Azure Maps 在加载地图时不需要指定初始中心和缩放级别。 如果在加载地图时未提供此信息，地图将尝试确定用户所在的城市，并将地图居中并缩放到该处。

**其他资源：**

- Azure Maps 还提供了用于旋转和间距调整地图视图的导航控件，如[此处](map-add-controls.md)所述。

### <a name="localizing-the-map"></a>本地化地图

如果你的受众遍布多个国家或地区，则本地化非常重要。

**之前： Google Maps**

若要本地化 Google Maps，请将语言和区域参数添加到

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

下面是将语言设置为 "fr" 的 Google Maps 的示例。

<center>

![Google Maps 本地化](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**后： Azure Maps**

Azure Maps 提供了两种不同的方法来设置地图的语言和区域视图。 第一种方法是将此信息添加到全局*阿特拉斯*命名空间，这会导致应用中的所有地图控件实例默认设置为这些设置。 下面将语言设置为法语（"fr"），并将区域视图设置为 "auto"：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二种方法是在加载映射时将此信息传递到映射选项，如下所示：

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
> 通过 Azure Maps 可以在具有不同语言和区域设置的同一页上加载多个映射实例。 此外，还可以在映射加载后在映射中更新这些设置。 可在[此处](supported-languages.md)找到 Azure Maps 中支持的语言的详细列表。

下面是将语言设置为 "fr" 并将用户区域设置为 "fr" 的 Azure Maps 的示例。

<center>

![Azure Maps 本地化](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>设置地图视图

可以通过在 JavaScript 中调用相应的函数以编程方式将 Azure 和 Google Maps 中的动态映射移动到新的地理位置。 下面的示例演示如何使地图显示卫星高空图像，将地图居中放置在具有坐标的位置（经度：-111.0225，纬度：35.0272），并在 Google Maps 中将缩放级别更改为15。

> [!NOTE]
> Google Maps 使用大小为256像素的磁贴，而 Azure Maps 使用较大的512像素磁贴。 这会减少 Azure Maps 加载与 Google Maps 相同的映射区所需的网络请求数。 不过，由于磁贴金字塔在地图控件中的工作方式，Azure Maps 中的较大磁贴意味着在 Google Maps 中实现与地图相同的可查看区域，则使用 Azure Maps 时，需要将 Google Maps 中使用的缩放级别减一。

**之前： Google Maps**

可以使用 `setOptions` 方法以编程方式移动 Google Maps 地图控件，该方法允许您指定地图的中心和缩放级别。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps 集视图](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**后： Azure Maps**

在 Azure Maps 中，可以使用地图的 `setCamera` 方法以编程方式更改地图位置，使用 `setStyle` 方法更改地图样式更改。 请注意，Azure Maps 中的坐标采用 "经度，纬度" 格式，缩放级别值为1。

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

![Azure Maps 集视图](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**其他资源：**

- [选择地图样式](choose-map-style.md)
- [支持的地图样式](supported-map-styles.md)

### <a name="adding-a-marker"></a>添加标记

在 Azure Maps 可以通过多种方式在地图上呈现点数据;

- **HTML 标记**–使用传统 DOM 元素呈现点。 HTML 标记支持拖动。
- **符号层**–在 WebGL 上下文中使用图标和/或文本呈现点。
- **气泡图层**–将点呈现为地图上的圆圈。 可以根据数据中的属性缩放圆的半径。

符号和气泡层都在 WebGL 上下文中呈现，并且能够在地图上呈现非常大的点集。 这些层要求将数据存储在数据源中。 引发 `ready` 事件之后，应将数据源和呈现层添加到地图中。 HTML 标记在页中呈现为 DOM 元素，不使用数据源。 页面具有的 DOM 元素越多，页面变慢的速度就越慢。 如果在地图上呈现的几百个以上的点，建议改用其中一种呈现层。

下面的示例将一个标记添加到地图上（经度：-0.2，纬度：51.5），并将数字10作为标签进行重叠。

**之前： Google Maps**

使用 Google Maps，使用 `google.maps.Marker` 类将标记添加到地图，并通过将地图指定为其中一个选项。

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

**后：使用 HTML 标记 Azure Maps**

在 Azure Maps 中，HTML 标记可用于显示地图上的某个点，建议仅用于只需在地图上显示小点的应用。 若要使用 HTML 标记，只需创建 `atlas.HtmlMarker` 类的实例，设置文本和位置选项，然后使用 `map.markers.add` 方法将标记添加到地图中。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

HTML 标记 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**After： Azure Maps 使用符号层**

使用符号层时，必须将数据添加到数据源，并将数据源附加到层。 此外，在 `ready` 事件激发后，应将数据源和层添加到映射。 若要在符号上方呈现唯一文本值，需要将文本信息存储为数据点的属性，并将该属性存储在该层的 `textField` 选项中。 这比使用 HTML 标记要多得多，但有很多性能优势。

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
- [群集点数据](clustering-point-data-web-sdk.md)
- [添加 HTML 标记](map-add-custom-html.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)
- [符号层图标选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [符号层文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 标记类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 标记选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>添加自定义标记

自定义图像可用于表示地图上的点。 下面的示例使用自定义图像来显示地图上的一个点（纬度：51.5，经度：-0.2），并偏移标记的位置，使图钉图标的点与地图上的正确位置对齐。

<center>

![黄色图钉图像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_图钉</center>

**之前： Google Maps**

在 Google Maps 中，通过指定包含图像的 `url` 的 `Icon` 对象来创建自定义标记，`anchor` 点将图钉图像的点与地图上的坐标对齐。 Google Maps 中的定位点值相对于图像的左上角。

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

**后：使用 HTML 标记 Azure Maps**

若要在中自定义 HTML 标记 Azure Maps 可以将 HTML `string` 或 `HTMLElement` 传递到标记的 `htmlContent` 选项。 在 Azure Maps 中，`anchor` 选项用于使用九个已定义的引用点之一来指定相对于位置坐标的标记的相对位置;"中心"、"上"、"下"、"左"、"右"、"左上方"、"右上方"、"左下方"。 默认情况下，内容将锚定到 html 内容的底部。 若要更轻松地从 Google Maps 迁移代码，请将 `anchor` 设置为 "左上角"，然后将 "`pixelOffset`" 选项与 Google Maps 中使用的相同偏移量一起使用。 Azure Maps 中的偏移量以 Google Maps 的相反方向移动，因此将它们乘以减一。

> [!TIP]
> 添加 `pointer-events:none` 作为 html 内容的样式，以禁用 Microsoft Edge 中将显示不需要的图标的默认拖动行为。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

Azure Maps 自定义 HTML 标记 ![](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**After： Azure Maps 使用符号层**

Azure Maps 中的符号层也支持自定义图像，但需要先将该图像加载到映射资源中，并为其分配唯一的 ID。 然后，符号层可以引用此 ID。 通过使用图标 `offset` 选项，符号可以偏移，以与图像上的正确点对齐。 在 Azure Maps 中，`anchor` 选项用于使用九个已定义的引用点之一来指定符号相对于位置坐标的相对位置;"中心"、"上"、"下"、"左"、"右"、"左上方"、"右上方"、"左下方"。 默认情况下，内容将锚定到 html 内容的底部。 若要更轻松地从 Google Maps 迁移代码，请将 `anchor` 设置为 "左上角"，然后将 "`offset`" 选项与 Google Maps 中使用的相同偏移量一起使用。 Azure Maps 中的偏移量以 Google Maps 的相反方向移动，因此将它们乘以减一。

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
> 若要创建点的高级自定义呈现，请同时使用多个呈现层。 例如，如果想要在不同的彩色圆圈上具有多个具有相同图标的图钉，而不是为每个颜色叠加创建一组图像，使其在气泡图层之上，并使它们引用同一数据源。 这比创建更高效，并使地图维护一组不同的图像。

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

折线用于表示地图上的行或路径。 下面的示例演示如何在地图上创建虚线折线。

**之前： Google Maps**

在 Google Maps 中，折线类采用一组选项。 用折线的 `path` 选项传递坐标数组。

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

**后： Azure Maps**

在 Azure Maps 中，折线称为 LineString 或 MultiLineString 对象。 可以将这些对象添加到数据源，并使用线条层进行呈现。

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

- [向地图添加行](map-add-line-layer.md)
- [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>添加多边形

多边形用于表示地图上的某个区域。 Azure Maps 和 Google Maps 为多边形提供非常类似的支持。 下面的示例演示如何创建一个多边形，使其基于地图的中心坐标形成三角形。

**之前： Google Maps**

在 Google Maps 中，多边形类采用一组选项。 在多边形的 `paths` 选项中传递坐标数组。

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

**后： Azure Maps**

在 Azure Maps 中，可以使用层将多边形和 MultiPolygon 对象添加到数据源，并在地图上呈现这些对象。 多边形的面积可以在多边形层中呈现。 可以使用线条层呈现多边形的轮廓。

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

Azure Maps 多边形 ![](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**其他资源：**

- [向地图添加多边形](map-add-shape.md)
- [向地图添加圆圈](map-add-shape.md#add-a-circle-to-the-map)
- [多边形层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [线条层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>显示信息窗口

实体的其他信息可以在 Google Maps 中作为 `google.maps.InfoWindow` 类显示在地图上，Azure Maps 可以使用 `atlas.Popup` 类实现此目的。 下面的示例向地图添加标记，并在单击时显示信息窗口/弹出窗口。

**之前： Google Maps**

使用 Google Maps，将使用 `google.maps.InfoWindow` 构造函数创建一个 "信息" 窗口。

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

![Google Maps 快捷菜单](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**后： Azure Maps**

在 Azure Maps 弹出式窗口可用于显示某个位置的附加信息。 HTML `string` 或 `HTMLElement` 对象可以传递到 popup 的 `content` 选项中。 如果需要，可以独立于任何形状显示弹出窗口，因而需要指定 `position` 值。 若要显示弹出窗口，请调用 `open` 方法，并传入要在其中显示弹出窗口的 `map`。

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

![Azure Maps popup](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 若要使用符号、气泡、线条或多边形层执行相同操作，只需将该层传递到 maps 事件代码而不是标记。

**其他资源：**

- [添加弹出项](map-add-popup.md)
- [带媒体内容的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [形状上的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [重复使用多个 Pin 的弹出窗口](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [弹出式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>导入 GeoJSON 文件

Google Maps 支持通过 `google.maps.Data` 类加载并动态设置 GeoJSON 数据的样式。 此类的功能与 Azure Maps 的数据驱动样式设置更密切。 一个主要区别是，使用 Google Maps 指定了一个回调函数，以及用于设置它在 UI 线程中单独处理的每个功能的样式的业务逻辑。 在 Azure Maps 中，层支持将数据驱动的表达式指定为样式选项。 在单独的线程上，在呈现时处理这些表达式，提供更高的呈现性能，并允许更快地呈现更大的数据集。

下面的示例从 USGS 中加载过去7天内所有地震的 GeoJSON 源，并将其呈现为地图上的缩放圆圈。 每个圆的颜色和比例基于每个地震的大小，每次地震都存储在数据集中每个功能的 `"mag"` 属性中。 如果量值大于或等于5，则圆圈将为红色，如果大于或等于三但小于五，则圆圈将为橙色，如果小于三，圆圈将为绿色。 每个圆的半径将是数量级乘以0.1 的幂值。

**之前： Google Maps**

在 Google Maps 中，可以在 `map.data.setStyle` 方法中指定一个回调函数，该函数将用于将业务逻辑应用于通过 `map.data.loadGeoJson` 方法从 GeoJSON 源加载的每个功能。

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

**后： Azure Maps**

GeoJSON 是 Azure Maps 中的基本数据类型，可以使用 `datasource.importFromUrl` 方法轻松地将其导入到数据源中。 气泡图层提供的功能用于根据数据源中的功能的属性呈现缩放的圆。 将业务逻辑转换为表达式并将其传递给样式选项，而不是使用回调函数。 表达式定义业务逻辑的工作方式，以便可以将它传递到另一个线程并针对功能数据进行计算。 可以将多个数据源和层添加到 Azure Maps，每个数据源和层具有不同的业务逻辑，从而允许以不同的方式在地图上呈现多个数据集。

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
- [群集点数据](clustering-point-data-web-sdk.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>标记聚类

当在地图上可视化多个数据点时，点彼此重叠，地图看起来显得有些混乱，因而难以查看和使用。 可使用点数据的聚类分析来改善此用户体验，同时提高性能。 聚类数据是将相邻的点数据组合在一起并将其作为单个群集数据点表示在地图上的过程。 当用户放大到地图中时，分类将分成各自的数据点。

以下示例从过去一周加载地震数据的 GeoJSON 源，并将其添加到地图中。 根据所含点数的数量，分类呈现为缩放和彩色圆圈。

> [!NOTE]
> 标记聚类使用多种不同的算法。 Google 和 Azure Maps 使用略微不同的算法。 因此，群集中的点分布有时可能会有所不同。

**之前： Google Maps**

在 Google Maps 中，可以通过在 MarkerClusterer 库中加载来对标记进行群集。 群集图标被限制为包含从1到5的数字作为其名称，并托管在同一目录中的映像。

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

![Google Maps 群集](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**后： Azure Maps**

在 Azure Maps 中，数据由数据源添加和管理。 层连接到数据源并在其中呈现数据。 Azure Maps 中的 `DataSource` 类提供多个群集选项。

- `cluster` –将数据源告知群集点数据。
- `clusterRadius`-将分类点组合在一起的半径（以像素为单位）。
- `clusterMaxZoom`-进行分类的最大缩放级别。 如果放大以上内容，则所有点将呈现为符号。
- `clusterProperties`-定义自定义属性，这些属性是使用表达式对每个分类中的所有点进行计算并添加到每个群集点的属性中的。

启用群集功能后，数据源会将群集和非群集的数据点发送到层进行呈现。 数据源能够聚集成千上万个数据点。 群集数据点具有以下属性：

| 属性名称             | 类型    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示功能是否表示群集。 |
| `cluster_id`              | 字符串  | 可以与 DataSource 一起使用的群集的唯一 ID `getClusterExpansionZoom`、`getClusterChildren`和 `getClusterLeaves` 方法。 |
| `point_count`             | 数字  | 群集包含的点数。  |
| `point_count_abbreviated` | 字符串  | 一个字符串，如果 `point_count` 值过长，则该字符串缩写。 （例如，4000变为4K）  |

`DataSource` 类具有以下 helper 函数，用于访问有关使用 `cluster_id`的群集的其他信息。

| 方法 | 返回类型 | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | 承诺&lt;数组&lt;功能&lt;几何，任何&gt; \| 形状&gt;&gt; | 在下一个缩放级别检索给定分类的子项。 这些子级可以是形状和 subclusters 的组合。 Subclusters 将是具有与 ClusteredProperties 匹配的属性的功能。 |
| `getClusterExpansionZoom(clusterId: number)` | 承诺&lt;号&gt; | 计算群集开始展开或分离的缩放级别。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | 承诺&lt;数组&lt;功能&lt;几何，任何&gt; \| 形状&gt;&gt; | 检索群集中的所有点。 设置 `limit` 以返回部分点，并使用 `offset` 逐页浏览点。 |

在地图上呈现群集数据时，使用两个或更多层通常是最简单的方法。 下面的示例使用三个层，这是一个气泡图层，用于根据分类的大小绘制缩放的彩色圆，使用一个符号层将群集大小呈现为文本，将另一个符号层用于呈现非群集点。 在[群集点数据](clustering-point-data-web-sdk.md)文档中突出显示的 Azure Maps 中，可以通过多种方式来呈现群集数据。

可以使用 `DataSource` 类上的 `importDataFromUrl` 函数，在 Azure Maps 中直接导入 GeoJSON 数据。

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

![Azure Maps 群集](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**其他资源：**

- [添加符号层](map-add-pin.md)
- [添加气泡层](map-add-bubble-layer.md)
- [群集点数据](clustering-point-data-web-sdk.md)
- [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>添加热度地图

热度地图，也称为点密度地图，是一种数据可视化类型，它们使用一系列颜色来表示数据密度。 它们通常用于在地图上显示数据“热点”，是呈现大型点数据集的好方法。

下面的示例从 USGS 中加载过去一个月的所有地震的 GeoJSON 源，并将其呈现为加权热度地图，其中 `"mag"` 属性用作权重。

**之前： Google Maps**

在 Google Maps 中，若要创建热度地图，需要通过将 `&libraries=visualization` 添加到 API 脚本 URL 来加载 "可视化" 库。 Google Maps 中的热度地图层不支持直接 GeoJSON 的数据，而是需要先下载数据，然后将其转换为一组加权数据点。

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

**后： Azure Maps**

在 Azure Maps 中，将 GeoJSON 数据加载到数据源，并将数据源连接到热度地图层。 可以使用表达式将用于权重的属性传递到 `weight` 选项。 可以使用 `DataSource` 类上的 `importDataFromUrl` 函数，在 Azure Maps 中直接导入 GeoJSON 数据。

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

### <a name="overlay-a-tile-layer"></a>覆盖图块层

图块层（也称为 Google Maps 中的图像叠加）允许覆盖已分解为较小的平铺图像、与地图平铺系统对齐的大图像。 这是一种覆盖大图像或非常大的数据集的常用方法。

下面的示例将覆盖爱荷华州大学爱荷华环境 Mesonet 的天气预报。

**之前： Google Maps**

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

![Google 地图图块层](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**后： Azure Maps**

在 Azure Maps 中，图块层可添加到地图中，这与任何其他层的方式几乎相同。 具有 x、y、缩放占位符的格式 URL;分别 `{x}`、`{y}``{z}`，告诉层在何处访问磁贴。 Azure Maps 图块层还支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。

> [!TIP]
> 在 Azure Maps 层中，可以轻松地在其他层（包括基本地图层）下呈现。 通常，需要在地图标签下面呈现图块层以便于阅读。 `map.layers.add` 方法采用第二个参数，该参数是要在其中插入新层的层的 id。 若要在地图标签下面插入图块层，可以使用以下代码： `map.layers.add(myTileLayer, "labels");`

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
> 可以使用映射的 `transformRequest` 选项捕获磁贴请求。 这将允许你根据需要修改或添加标头。

**其他资源：**

- [添加图块层](map-add-tile-layer.md)
- [图块层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [图块层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>显示交通情况

流量数据可以重叠到 Azure 和 Google maps。

**之前： Google Maps**

在 Google Maps 中，可以使用流量层将流量数据覆盖到地图中。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps 流量](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**后： Azure Maps**

Azure Maps 提供了用于显示流量的多种不同选项。 流量事件（例如公路和事故）可以显示为地图上的图标。 流量可以在地图上重叠，可以在地图上重叠，并且可以根据正常的预期延迟或绝对延迟，将颜色修改为相对于已发布的速度限制。 Azure Maps 中的事件数据每分钟更新一次，流数据每两分钟更新一次。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

Azure Maps 流量 ![](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

如果单击其中一个流量图标 Azure Maps 中，将在弹出窗口中显示附加信息。

<center>

Azure Maps 流量事件 ![](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**其他资源：**

- [在地图上显示交通信息](map-show-traffic.md)
- [流量覆盖选项](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>添加地面覆盖

Azure 和 Google maps 都支持在地图上覆盖 georeferenced 的图像，以便在平移和缩放地图时移动和缩放。 在 Google Maps 中，这两个称为地面叠加，而在中，它们被称为图像层 Azure Maps。 它们非常适合用于构建楼面计划、覆盖旧地图或无人机的图像。

**之前： Google Maps**

在 Google Maps 中创建地面覆盖时，需要指定要叠加的图像的 URL，以及用于将图像绑定到地图上的边界框。 此示例将地图上[纽瓦克 New Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地图图像叠加为1922。

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

在浏览器中运行此代码将显示如下图所示的地图：

<center>

![Google Maps 图像覆盖](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**后： Azure Maps**

在 Azure Maps 中，可以使用 `atlas.layer.ImageLayer` 类对 georeferenced 图像进行重叠。 此类需要图像的 URL 和图四个角的一组坐标。 该映像必须位于同一个域中或已启用 CORs。

> [!TIP]
> 如果只包含北部、南部、东、西和旋转信息，而不是图像每个角的坐标，则可以使用静态[`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-)方法。

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

Azure Maps 图像覆盖 ![](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**其他资源：**

- [覆盖图像](map-add-image-layer.md)
- [图像层类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>其他代码示例

下面是一些与 Google Maps 迁移相关的其他代码示例：

- [绘图工具](map-add-drawing-toolbar.md)
- [将地图限制为两指平移](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [限制滚动滚轮缩放](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [创建全屏控件](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**服务：**

- [使用 Azure Maps services 模块](how-to-use-services-module.md)
- [搜索兴趣点](map-search-location.md)
- [从坐标获取信息（反向地理编码）](map-get-information-from-coordinate.md)
- [显示从 A 到 B 的路线](map-route.md)
- [用 JQuery UI 搜索自动建议](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 到 Azure Maps Web SDK 类映射

以下附录提供了 Google Maps V3 中最常用类的交叉引用映射，并将其用于 Azure Maps Web SDK 等效项。

### <a name="core-classes"></a>核心类

| Google 地图   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [阿特拉斯.将](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [阿特拉斯.菜单](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [阿特拉斯.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [塔数据。位置](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [阿特拉斯.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [阿特拉斯.坏](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>覆盖类

| Google 地图  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [阿特拉斯.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[阿特拉斯. 数据点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [阿特拉斯.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[阿特拉斯.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [阿特拉斯. 数据多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [阿特拉斯.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [阿特拉斯.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[阿特拉斯.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | 请参阅[向地图添加圆圈](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [阿特拉斯.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [阿特拉斯.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[阿特拉斯.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>服务类

Azure Maps Web SDK 包括可单独加载的[服务模块](how-to-use-services-module.md)。 此模块将 Azure Maps REST 服务与 web API 包装在一起，并可在 JavaScript、TypeScript 和 node.js 应用程序中使用。

| Google 地图 | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [阿特拉斯.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[阿特拉斯.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [阿特拉斯.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>库

库将其他功能添加到地图中。 其中很多是 Azure Maps 的核心 SDK。 下面是一些用于替代这些 Google Maps 库的等效类

| Google 地图           | Azure Maps   |
|-----------------------|--------------|
| 绘图库       | [绘图工具模块](set-drawing-options.md) |
| 几何库      | [阿特拉斯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 可视化效果库 | [热度地图层](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>后续步骤

了解有关 Azure Maps Web SDK 的详细信息。

> [!div class="nextstepaction"]
> [如何使用地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服务模块](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用绘图工具模块](set-drawing-options.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)

