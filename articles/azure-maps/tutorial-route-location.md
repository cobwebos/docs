---
title: 教程：查找到某个位置的路线 | Microsoft Azure Maps
description: 本教程介绍如何查找一个到兴趣点的路线。 了解如何设置地址坐标和查询 Azure Maps 路线服务，以获取前往该点的方向。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 3cb9bee65ab7fa2c29185c40ecb48fd531192187
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321707"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>教程：如何使用 Azure Maps 路线服务和地图控件显示路线走向

本教程演示如何使用 Azure Maps [路线服务 API](https://docs.microsoft.com/rest/api/maps/route) 和[地图控件](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)显示从起点到终点的路线走向。 本教程介绍以下操作：

> [!div class="checklist"]
> * 在网页上创建和显示地图控件。 
> * 通过定义[符号层](map-add-pin.md)和[线条层](map-add-line-layer.md)来定义路线的显示呈现。
> * 创建 GeoJSON 对象并将其添加到地图，以表示起点和终点。
> * 使用[获取路线走向 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) 获取从起点到终点的路线走向。

可在[此处](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)获得示例的完整源代码。 可在[此处](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)找到实时示例。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>创建和显示地图控件

以下步骤演示如何在网页中创建和显示地图控件。

1. 在本地计算机上，创建一个新文件并将其命名为 **MapRoute.html**。
2. 将以下 HTML 标记复制/粘贴到文件中。

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    HTML 标头包含 Azure Map Control 库托管的 CSS 和 JavaScript 资源文件。 正文的 `onload` 事件调用 `GetMap` 函数。 在下一步中，我们将添加地图控件初始化代码。

3. 将以下 JavaScript 代码添加到 `GetMap` 函数。 将字符串 `<Your Azure Maps Key>` 替换为从 Maps 帐户复制的主密钥。

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

4. 保存文件并在浏览器中打开它。 此时显示一个示例。

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="地图控件的基本地图呈现":::

## <a name="define-route-display-rendering"></a>定义路线显示呈现

在本教程中，我们将使用线条层呈现路线。 起点和终点将使用符号层进行呈现。 有关添加线条层的详细信息，请参阅[将线条层添加到地图](map-add-line-layer.md)。 若要了解有关符号层的详细信息，请参阅[将符号层添加到地图](map-add-pin.md)。

1. 在 `GetMap` 函数中追加以下 JavaScript 代码。 此代码实现地图控件的 `ready` 事件处理程序。 本教程中的其余代码将放在 `ready` 事件处理程序中。

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```

    在地图控件的 `ready` 事件处理程序中，将会创建一个数据源来存储从起点到终点的路线。 为了定义路线的呈现方式，将会创建一个线条层并将其附加到数据源。  为了确保路线线条不遮盖道路标签，我们传递了第二个参数，其值为 `'labels'`。

    接下来，将会创建一个符号层并将其附加到数据源。 此层指定起点和终点的呈现方式。已添加表达式，用于从每个点对象的属性中检索图标图像和文本标签信息。 若要详细了解表达式，请参阅[数据驱动的样式表达式](data-driven-style-expressions-web-sdk.md)。

2. 将起点设为 Microsoft，将终点设为西雅图的加油站。  在地图控件的 `ready` 事件处理程序中，追加以下代码。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    此代码创建两个 [GeoJSON 点对象](https://en.wikipedia.org/wiki/GeoJSON)来表示起点和终点，这两个对象随后添加到数据源中。 

    最后一个代码块使用起点和终点的纬度和经度来设置相机视图。 起点和终点会添加到数据源。 起点和终点的边框使用 `atlas.data.BoundingBox.fromData` 函数计算。 此边框用于通过 `map.setCamera` 函数设置基于整个路线的地图相机视图。 将会添加一个填充来弥补符号图标的像素尺寸。 有关地图控件的 setCamera 属性的详细信息，请参阅 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) 属性。

3. 保存 MapRoute.html 并刷新浏览器。 现在，地图以西雅图为中心。 泪珠形蓝色图钉标记起点。 圆形蓝色图钉标记终点。

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="地图控件的基本地图呈现":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>获取路线走向

本部分演示如何使用 Azure Maps 路线走向 API 来获取路线走向以及从一个点到另一个点的预计到达时间。

>[!TIP]
>Azure Maps 路线服务提供 API 来根据不同的路线类型规划路线，例如根据距离、路况和所用交通方式提供最快路线、最短路线、生态路线或惊险路线   。 此服务还让用户可以根据历史路况规划将来的路线。 用户可以看到任何给定时间的路线时间预测。 有关详细信息，请参阅[获取路线走向 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

1. 在 `GetMap` 函数的控件的 `ready` 事件处理程序中，将以下内容添加到 JavaScript 代码中。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` 创建 `SubscriptionKeyCredentialPolicy` 以使用订阅密钥验证对 Azure Maps 的 HTTP 请求。 `atlas.service.MapsURL.newPipeline()` 接受 `SubscriptionKeyCredential` 策略并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline)实例。 `routeURL` 表示 Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) 操作的 URL。

2. 设置凭据和 URL 后，将以下代码追加到控件的 `ready` 事件处理程序中。 此代码构造从起点到终点的路线。 `routeURL` 会请求 Azure Maps 路线服务 API 计算路线走向。 然后，系统会使用 `geojson.getFeatures()` 方法从响应中提取 GeoJSON 特性集合，并将其添加到数据源。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. 保存“MapRoute.html”文件并刷新 web 浏览器****。 现在，地图应显示从起点到终点的路线。

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="地图控件的基本地图呈现":::

可在[此处](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)获得示例的完整源代码。 可在[此处](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)找到实时示例。

## <a name="next-steps"></a>后续步骤

下一教程演示如何在有限制的情况下（例如出行模式或货物类型）创建路线查询。 你随后可以在同一地图上显示多条路线。

> [!div class="nextstepaction"]
> [查找不同出行模式的路线](./tutorial-prioritized-routes.md)