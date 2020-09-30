---
title: 教程：使用 Microsoft Azure Maps 查找和显示特定出行模式的路线
description: 了解如何使用 Azure Maps 查找和显示特定出行模式的路线。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: a5b8dba920db0227c400e62ef7ddaf718d27c78a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085053"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>教程：使用 Azure Maps 查找和显示不同出行模式的路线

本教程演示如何使用 Azure Maps [路线服务](https://docs.microsoft.com/rest/api/maps/route)和[地图控件](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)为私家车和货物类型为 `USHazmatClass2` 的商用车（卡车）显示路线走向。 此外，我们还将演示如何在地图上直观呈现实时交通数据。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在网页上创建和显示地图控件
> * 在地图上呈现实时交通数据
> * 在地图上请求并显示私家车和商用车路线

## <a name="prerequisites"></a>先决条件

1. 登录 [Azure 门户](https://portal.azure.com)。

2. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

可在[此处](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)获得示例的完整源代码。 可在[此处](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)找到实时示例。

## <a name="create-a-new-web-page-using-the-map-control-api"></a>使用 Map Control API 创建新网页

以下步骤演示如何在网页中创建和显示地图控件。

1. 在本地计算机上，创建一个新文件并将其命名为 MapTruckRoute.html  。
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

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="地图控件的基本地图呈现":::

## <a name="render-real-time-traffic-data-on-a-map"></a>在地图上呈现实时交通数据

1. 在 `GetMap` 函数中追加以下 JavaScript 代码。 此代码实现地图控件的 `ready` 事件处理程序。 本教程中的其余代码将放在 `ready` 事件处理程序中。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    在地图 `ready` 事件处理程序中，地图上的交通流设置已设置为 `relative`，这是相对于自由流动的道路速度。 有关更多交通选项，请参阅 [TrafficOptions 接口](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions?view=azure-maps-typescript-latest&preserve-view=false)。

2. 保存“MapTruckRoute.html”文件并刷新浏览器中的页  。 如果放大地图并显示任何城市（如洛杉矶），你可看到显示了当前交通流数据的街道。

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="地图控件的基本地图呈现":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>定义路线显示呈现

在本教程中，将会计算两条线路并在地图上呈现它们。 将为私家车（小汽车）计算第一条路线。 将为商用车（卡车）计算第二条路线，以显示结果之间的差异。 在呈现时，地图上将针对路线的起点和终点显示符号图标，用不同颜色的路线几何图形来表示各个路线路径。 有关添加线条层的详细信息，请参阅[将线条层添加到地图](map-add-line-layer.md)。 若要了解有关符号层的详细信息，请参阅[将符号层添加到地图](map-add-pin.md)。

1. 在地图控件的 `ready` 事件处理程序中，追加以下代码。

    ```JavaScript

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

    ```

    在地图控件的 `ready` 事件处理程序中，将会创建一个数据源来存储从起点到终点的路线。 使用[表达式](data-driven-style-expressions-web-sdk.md)从路线功能的属性中检索路线宽度和颜色。 为了确保路线线条不遮盖道路标签，我们传递了第二个参数，其值为 `'labels'`。

    接下来，将会创建一个符号层并将其附加到数据源。 此层指定起点和终点的呈现方式。已添加表达式，用于从每个点对象的属性中检索图标图像和文本标签信息。 若要详细了解表达式，请参阅[数据驱动的样式表达式](data-driven-style-expressions-web-sdk.md)。

2. 将起点设为西雅图一家名为 Fabrikam 的虚构公司，终点设为 Microsoft 办公室。  在地图控件的 `ready` 事件处理程序中，追加以下代码。

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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    此代码创建两个 [GeoJSON 点对象](https://en.wikipedia.org/wiki/GeoJSON)来表示起点和终点，这两个对象随后添加到数据源中。

    最后一个代码块使用起点和终点的纬度和经度来设置相机视图。 起点和终点会添加到数据源。 起点和终点的边框使用 `atlas.data.BoundingBox.fromData` 函数计算。 此边框用于通过 `map.setCamera` 函数设置基于整个路线的地图相机视图。 将会添加一个填充来弥补符号图标的像素尺寸。 有关地图控件的 setCamera 属性的详细信息，请参阅 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) 属性。

3. 保存 TruckRoute.html 并刷新浏览器。 现在，地图以西雅图为中心。 泪珠形蓝色图钉标记起点。 圆形蓝色图钉标记终点。

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="地图控件的基本地图呈现":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>在地图上请求并显示私家车和商用车路线

本部分演示如何使用 Azure Maps 路线服务，根据交通方式获取从一个点到另一个点的走向。 我们将使用两种交通方式：卡车和小汽车。

>[!TIP]
>路线服务提供多个 API，根据距离、路况和所用交通方式，规划最快、最短、环保或令人兴奋的路线   。 此服务还让用户可以根据历史路况规划将来的路线。 用户可以看到任何给定时间的路线时间预测。 有关详细信息，请参阅[获取路线走向 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

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

2. 在设置凭据和 URL 以后，请添加以下 JavaScript 代码，以便构建从起点到终点的卡车路线。 此路线是为装载 `USHazmatClass2` 类货物的卡车创建和显示的。

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

    上述代码通过 [Azure Maps 路线走向 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) 查询 Azure Maps 路线服务。 然后，从使用 `geojson.getFeatures()` 方法提取的响应中的 GeoJSON 特征集合提取路线。 最后，该路线将添加到数据源。 我们要将它添加到索引 0 处，以确保先于数据源中的任何其他路线之前呈现卡车路线，因为卡车路线计算速度通常比小汽车路线计算速度慢。 如果先将小汽车路线添加到数据源，再次卡车路线添加到数据源，则会在小汽车路线的上面呈现卡车路线。 将会向卡车路线添加两个属性：一个是蓝色的描边色，另一个是九像素的描边宽度。

    >[!TIP]
    > 要查看 Azure Maps 路线走向 API 的所有可能的选项和值，请参阅 [Post 路线走向的 URI 参数](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#uri-parameters)。

3. 现在追加以下 JavaScript 代码，以构造小汽车路线。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    上述代码通过 [Azure Maps 路线走向 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) 方法来查询 Azure Maps 路线服务。 然后，从使用 `geojson.getFeatures()` 方法提取的响应中的 GeoJSON 特征集合提取路线。 最后，该路线将添加到数据源。 将会向卡车路线添加两个属性：一个是紫色的描边色，另一个是五像素的描边宽度。

4. 保存 TruckRoute.html 文件并刷新 web 浏览器。 地图现在应显示卡车路线和小汽车路线。

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="地图控件的基本地图呈现":::

    卡车路线使用蓝色粗线显示。 小汽车路线使用紫色细线显示。 小汽车路线经 I-90 穿过华盛顿湖，会经过居民区下的隧道。 由于隧道靠近居住区，因此会限制危险货物。 卡车路线（指定 `USHazmatClass2` 货物类型）指向不同的公路。

    可在[此处](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)获得示例的完整源代码。 可在[此处](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)找到实时示例。

## <a name="next-steps"></a>后续步骤

下一教程演示使用 Azure Maps 创建简单商店定位器的过程。

> [!div class="nextstepaction"]
> [使用 Azure Maps 创建商店定位器](./tutorial-create-store-locator.md)
