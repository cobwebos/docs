---
title: 使用 Azure Maps 查找路线 | Microsoft Docs
description: 使用 Azure Maps 查找前往兴趣点的路线
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fd75ca1fbad358e80a2c040b5ead8c50611489e2
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478872"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>使用 Azure Maps 查找前往兴趣点的路线

本教程介绍如何使用 Azure Maps 帐户和路线服务 SDK 来查找前往兴趣点的路线。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 设置地址坐标
> * 在路线服务中查询兴趣点的方向

## <a name="prerequisites"></a>先决条件

在继续之前，按照前面的教程中的步骤[创建 Azure Maps 帐户](./tutorial-search-location.md#createaccount)并且[获取帐户的订阅密钥](./tutorial-search-location.md#getkey)。

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>创建新地图

以下步骤说明如何使用 Map Control API 创建一个嵌入式静态 HTML 页面。

1. 在本地计算机上，创建一个新文件并将其命名为 **MapRoute.html**。
2. 将以下 HTML 组件添加到该文件：

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

    请注意，HTML 标头包含 Azure 地图控件库托管的 CSS 和 JavaScript 资源文件。 请注意页面正文中的 `onload` 事件。当页面正文加载时，此事件会调用 `GetMap` 函数。 此函数会包含用于访问 Azure Maps API 的内联 JavaScript 代码。 

3. 将以下 JavaScript 代码添加到 `GetMap` 函数。 将字符串 `<Your Azure Maps Key>` 替换为从 Maps 帐户复制的主要密钥。

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

    `atlas.Map` 提供一个可视和交互式的 Web 地图控件，它是 Azure Map Control API 的一个组件。

4. 保存文件并在浏览器中打开它。 此时，你有一个可以进一步开发的基础地图。

   ![查看基础地图](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>定义路线的呈现方式

在本教程中，在呈现简单的路线时，将使用符号图标来表示路线的起点和终点，用一条线来表示路线的路径。

1. 在初始化地图以后，添加以下 JavaScript 代码。

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
    
    在地图 `ready` 事件处理程序中，将会创建一个数据源来存储路线以及起点和终点。 将会创建一个路线层并将其附加到数据源，以便定义路线的呈现方式。 路线会呈现为蓝色，其宽度为 5 个像素，其连接处和端口处以圆形表示。 向地图添加此层时，会传递另一个值为 `'labels'` 的参数，指定在地图标签下呈现此层。 这样可确保路线不覆盖道路标签。 将会创建一个符号层并将其附加到数据源。 此层指定起点和终点的呈现方式。此示例中添加了表达式，用于从每个点对象的属性中检索图标图像和文本标签信息。 
    
2. 在本教程中，将起点设为 Microsoft，将终点设为西雅图的加油站。 在地图 `ready` 事件处理程序中，请添加以下代码。

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

    此代码创建两个 [GeoJSON 点对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线的起点和终点，并将这些点添加到数据源。 将会向每个点添加 `title` 和 `icon` 属性。 最后一个块通过地图的 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性使用起点和终点的纬度和经度信息来设置相机视图。

3. 保存“MapRoute.html”文件并刷新浏览器  。 现在，地图的中心为西雅图，可以看到标记起点的蓝色图钉和标记终点的圆形蓝色图钉。

   ![查看标记了起点和终点的地图](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>获取方向

本部分介绍如何使用 Azure Maps 路线服务 API 查找从给定起点到终点的路线。 路线服务提供多个 API，规划两个地点之间最快、最短、环保或令人兴奋的路线     。 此外，它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天任何时间的路线时间来规划路线。 有关详细信息，请参阅 [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)（获取路线指示）。 应该**在 map ready eventListener 中**添加以下所有功能，以确保在准备好访问地图资源后加载它们。

1. 在 GetMap 函数中，将以下内容添加到 JavaScript 代码。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` 创建 `SubscriptionKeyCredentialPolicy` 以使用订阅密钥验证对 Azure Maps 的 HTTP 请求。 `atlas.service.MapsURL.newPipeline()` 接受 `SubscriptionKeyCredential` 策略并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)实例。 `routeURL` 表示 Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) 操作的 URL。

2. 在设置凭据和 URL 以后，请添加以下 JavaScript 代码，以便构建从起点到终点的路线。 `routeURL` 会请求 Azure Maps 路线服务计算路线走向。 然后，系统会使用 `geojson.getFeatures()` 方法从响应中提取 GeoJSON 特性集合，并将其添加到数据源。

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

3. 保存“MapRoute.html”文件并刷新 web 浏览器  。 要成功连接 Maps API，应看到类似于以下内容的地图。

    ![Azure 地图控件和路线服务](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 设置地址坐标
> * 在路线服务中查询兴趣点的方向

> [!div class="nextstepaction"]
> [查看完整源代码](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [查看实时示例](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

下一教程演示如何在有限制的情况下（例如出行模式或货物类型）创建路线查询，以及如何在同一地图上显示多条路线。

> [!div class="nextstepaction"]
> [查找不同出行模式的路线](./tutorial-prioritized-routes.md)
