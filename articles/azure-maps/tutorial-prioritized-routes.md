---
title: 教程：按出行模式查找多个路线 | Microsoft Azure Maps
description: 本教程介绍如何使用 Microsoft Azure Maps 查找不同出行模式的路线。
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 628a3003cec2cc2ca58f1b133cf3236417dfa94e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209488"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>教程：使用 Azure Maps 查找不同出行模式的路线

本教程展示了如何使用 Azure Maps 帐户和路线服务。 路线服务可以查找通往你感兴趣的地点的路线，按你的出行模式排列优先级。 你可以在地图上显示两条不同的路线，一条是汽车路线，一条是卡车路线。 由于车辆的高度和重量，或者如果车辆运载危险货物，路线服务会考虑各种限制。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 在地图上可视化交通流量
> * 创建声明出行模式的路线查询
> * 在地图上显示多条路线

## <a name="prerequisites"></a>必备条件
在继续之前，请按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明进行操作，并选择 S1 定价层。 按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

## <a name="create-a-new-map"></a>创建新地图

以下步骤说明如何使用 Map Control API 创建一个嵌入式静态 HTML 页面。

1. 在本地计算机上，创建一个新文件并将其命名为 MapTruckRoute.html  。
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

    `atlas.Map` 类提供一个可视和交互式 Web 地图的控件，是 Azure Map Control API 的一个组件。

4. 保存文件并在浏览器中打开它。 此时，你有一个可以进一步开发的基础地图。

   ![查看基础地图](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>可视化交通流量

1. 将交通流量显示添加到地图。 地图 `ready` 事件会一直等待，直至地图资源加载完毕并做好安全交互准备。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    在地图 `ready` 事件处理程序中，地图上的交通流设置已设置为 `relative`，这是相对于自由流动的道路速度。 此外，还可以将其设置为道路的 `absolute` 速度或 `relative-delay`，它会显示与自由流动不同的相对速度。

2. 保存“MapTruckRoute.html”文件并刷新浏览器中的页  。 如果与地图交互并放大“洛杉矶”地图，应会看到街道和当前交通数据。

   ![在地图上查看交通信息](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>定义路线的呈现方式

在本教程中，将会计算两条线路并在地图上呈现它们。 一条线路使用供汽车行驶的道路，另一条线路使用供卡车行驶的道路。 在呈现时，我们将针对路线的起点和终点显示符号图标，用不同颜色的线来表示各个路线路径。

1. 初始化地图后，在地图 `ready` 事件处理程序中添加以下 JavaScript 代码。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
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
    
    在地图 `ready` 事件处理程序中，将会创建一个数据源来存储路线以及起点和终点。 将会创建一个路线层并将其附加到数据源，以便定义路线的呈现方式。 使用表达式从路线功能的属性中检索路线宽度和颜色。 向地图添加此层时，会传递另一个值为 `'labels'` 的参数，指定在地图标签下呈现此层。 这可确保路线线条不会盖住道路标签。 将会创建一个符号层并将其附加到数据源。 此层指定起点和终点的呈现方式。 此示例中添加了表达式，用于从每个点对象的属性中检索图标图像和文本标签信息。 
    
2. 在本教程中，将起点设为西雅图一家名为 Fabrikam 的虚构公司，终点设为 Microsoft 办公室。 在地图 `ready` 事件处理程序中，请添加以下代码。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    此代码创建两个 [GeoJSON 对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线的起点和终点。 将会向每个点添加 `title` 和 `icon` 属性。

3. 接下来，添加以下 JavaScript 代码，将起点和终点的图钉添加到地图中：

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    起点和终点会添加到数据源。 起点和终点的边框使用 `atlas.data.BoundingBox.fromData` 函数计算。 此边框用于通过 `map.setCamera` 函数设置基于整个路线的地图相机视图。 将会添加一个衬距来弥补符号图标的像素尺寸。

4. 保存文件并刷新浏览器以查看地图上显示的图钉。 现在，地图的中心为西雅图。 你可以看到标记起点的圆形蓝色图钉和标记终点的蓝色图钉。

   ![查看有起点和终点的地图](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>呈现按行驶模式划分优先级的路线

本部分介绍如何使用 Maps 路线服务 API。 路线 API 用于根据交通方式查找从给定起点到终点的多条路线。 路线服务提供相应的 API 用于规划最快、最短、环保或新奇的路线。     这些 API 不仅可以规划两个地点之间的路线，而且还能考虑到当前的交通状况。 

路线 API 还允许用户使用 Azure 广泛的历史交通数据库来规划将来的路线。 API 可以预测给定日期和时间的出行持续时间。 有关详细信息，请参阅 [Get Route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)（获取路线指示）。 

应该**在地图加载 eventListener 中**添加以下所有代码块，确保在地图完全加载之后加载这些块。

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

2. 设置凭据和 URL 后，添加以下 JavaScript 代码，为运输 USHazmatClass2 类货物的卡车构造从起点到终点的路线，并显示结果。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    上述代码片段通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 方法查询 Azure Maps 路线服务。 然后，从使用 `geojson.getFeatures()` 方法提取的响应中的 GeoJSON 特征集合提取路线。 该路线行随后将添加到数据源。 其索引为 0，这可以确保该路线行在数据源中的任何其他行之前呈现。 这是因为卡车路线计算通常比小汽车路线计算要慢。 如果先将小汽车路线添加到数据源，再次卡车路线添加到数据源，则会在小汽车路线的上面呈现卡车路线。 将会向卡车路线添加两个属性，一个是蓝色的描边色，另一个是九像素的描边宽度。

3. 添加以下 JavaScript 代码，以构造车辆路线并显示结果。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    上述代码片段通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 方法查询 Azure Maps 路线服务。 然后，从使用 `geojson.getFeatures()` 方法提取的响应中的 GeoJSON 特征集合提取路线。 该路线行随后将添加到数据源。 将会向汽车路线添加两个属性，一个是紫色的描边色，另一个是五像素的描边宽度。  

4. 保存“MapTruckRoute.html”文件并刷新浏览器以查看结果  。 要成功连接 Maps 的 API，应看到类似于以下内容的地图。

    ![使用 Azure 路线服务设置路线的优先级](./media/tutorial-prioritized-routes/prioritized-routes.png)

    卡车路线为深蓝色，小汽车路线为浅紫色。 小汽车路线经 I-90 穿过华盛顿湖，这条路线经过居民区下的隧道。 由于隧道靠近居住区，因此会限制危险货物。 卡车路线（指定 USHazmatClass2 货物类型）指向不同的公路。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 在地图上可视化交通流量
> * 创建声明出行模式的路线查询
> * 在地图上显示多条路线

> [!div class="nextstepaction"]
> [查看完整源代码](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [查看实时示例](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

下一教程演示使用 Azure Maps 创建简单商店定位器的过程。

> [!div class="nextstepaction"]
> [使用 Azure Maps 创建商店定位器](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)