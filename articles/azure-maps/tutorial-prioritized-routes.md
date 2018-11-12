---
title: 使用 Azure Maps 查找多条路线 | Microsoft Docs
description: 使用 Azure Maps 查找不同出行模式的路线
author: walsehgal
ms.author: v-musehg
ms.date: 10/29/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 67b68489f2e06b9149f842f293a769fa7f688be0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412697"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>使用 Azure Maps 查找不同出行模式的路线

本教程将介绍如何使用 Azure Maps 帐户和路线服务，以查找按行驶模式划分优先级的兴趣点路线。 地图上显示了两条路线，一条针对汽车，另一条针对可能有路线限制（因为高度、重量或者危险货物）的卡车。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 在地图上可视化交通流量
> * 创建声明出行模式的路线查询
> * 在地图上显示多条路线

## <a name="prerequisites"></a>先决条件

在执行下一个操作前，请按照第一个教程中的步骤[创建 Azure Maps 帐户](./tutorial-search-location.md#createaccount)并且[获取帐户的订阅密钥](./tutorial-search-location.md#getkey)。

## <a name="create-a-new-map"></a>创建新地图

以下步骤说明如何使用 Map Control API 创建一个嵌入式静态 HTML 页面。

1. 在本地计算机上，创建一个新文件并将其命名为 MapTruckRoute.html。
2. 将以下 HTML 组件添加到该文件：

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

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
                position: relative;
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

3. 将以下 JavaScript 代码添加到 `GetMap` 函数。 使用从 Maps 帐户复制的主键替换字符串 **\<Your Azure Maps Key\>**。

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    Atlas.Map 提供一个可视和交互式的 Web 地图控件，它是 Azure Map Control API 的一个组件。

4. 保存文件并在浏览器中打开它。 此时，你有一个可以进一步开发的基础地图。

   ![查看基础地图](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>可视化交通流量

1. 将交通流量显示添加到地图。 `map.events.add` 可确保在完全加载地图后加载添加到地图的所有地图函数。

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     将会向地图添加一个加载事件。当地图资源完全加载以后，该事件会触发。 在地图加载事件处理程序中，地图上的交通流设置已设置为 `relative`，这是相对于自由流动的道路速度。 此外，还可以将其设置为道路的 `absolute` 速度或 `relative-delay`，它会显示与自由流动不同的相对速度。

2. 保存“MapTruckRoute.html”文件并刷新浏览器中的页。 应看到洛杉矶街道及其当前交通数据。

   ![查看交通地图](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>定义路线的呈现方式

在本教程中，将会计算两条线路并在地图上呈现它们。 一条线路使用供汽车行驶的道路，另一条线路使用供卡车行驶的道路。 在呈现时，我们将针对路线的起点和终点显示符号图标，用不同颜色的线来表示各个路线路径。

1. 在 GetMap 函数中，请在初始化地图以后，添加以下 JavaScript 代码。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
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
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    将会向地图添加一个加载事件。当地图资源完全加载以后，该事件会触发。 在地图加载事件处理程序中，将会创建一个数据源来存储路线以及起点和终点。 将会创建一个路线层并将其附加到数据源，以便定义路线的呈现方式。 使用表达式从路线功能的属性中检索路线宽度和颜色。 将会添加一个筛选器，确保此层仅呈现 GeoJSON LineString 数据。 向地图添加此层时，会传递另一个值为 `'labels'` 的参数，指定在地图标签下呈现此层。 这样可确保路线不覆盖道路标签。 将会创建一个符号层并将其附加到数据源。 此层指定起点和终点的呈现方式。此示例中添加了表达式，用于从每个点对象的属性中检索图标图像和文本标签信息。 
    
2. 在本教程中，将起点设为西雅图一家名为 Fabrikam 的虚构公司，终点设为 Microsoft 办公室。 在地图加载事件处理程序中，请添加以下代码。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
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
    
    起点和终点会添加到数据源。 起点和终点的边框使用 `atlas.data.BoundingBox.fromData` 函数计算。 此边框用于通过 `map.setCamera` 函数设置基于起点和终点的地图照相机视图。 将会添加一个衬距来弥补符号图标的像素尺寸。

4. 保存文件并刷新浏览器以查看地图上显示的图钉。 现在，地图的中心为西雅图，可以看到标记起点的圆形蓝色图钉和标记终点的蓝色图钉。

   ![查看有起点和终点的地图](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>呈现按行驶模式划分优先级的路线

本部分介绍如何使用 Maps 的路线服务 API 根据运输模式查找从指定起点到目的地的多个路线。 路线服务提供多个 API，在考虑到当前交通状况的情况下，规划两个地点之间最快、最短、环保或令人兴奋的路线。 此外，它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天任何时间的路线时间来规划路线。 有关详细信息，请参阅 [Get Route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)（获取路线指示）。 应该**在 map load eventListener 中**添加以下所有代码块，以确保它们在地图完全加载之后加载。

1. 通过在地图加载事件处理程序中添加以下 Javascript 代码来实例化服务客户端。

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. 添加以下代码块来构造路由查询字符串。

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. 添加以下 JavaScript 代码，为装载 USHazmatClass2 类货物的卡车请求路线并显示结果。

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    上面的这个代码片段通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法查询 Azure Maps 路线服务，然后使用 [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) 将响应分析成 GeoJSON 格式。 然后，它创建返回路线的坐标数组并将其添加到数据源，但同时也添加索引 0，确保将其呈现后再呈现数据源中的任何其他路线。 之所以这样做，是因为卡车路线的计算通常会慢于汽车路线的计算，如果卡车路线在汽车路线之后添加到数据源，则会在其上进行呈现。 将会向卡车路线添加两个属性，一个是蓝色的描边色，另一个是 9 像素的描边宽度。 

4. 添加下面的 JavaScript 代码，请求汽车路线并显示结果：

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    此代码片段对汽车使用相同的卡车路线查询。 它通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法查询 Azure Maps 路线服务，然后使用 [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) 将响应分析为 GeoJSON 格式。 然后创建返回路线的坐标数组并将其添加到数据源。 将会向汽车路线添加两个属性，一个是紫色的描边色，另一个是 5 像素的描边宽度。 

5. 保存“MapTruckRoute.html”文件并刷新浏览器以查看结果。 要成功连接 Maps 的 API，应看到类似于以下内容的地图。

    ![使用 Azure 路线服务设置路线的优先级](./media/tutorial-prioritized-routes/prioritized-routes.png)

    卡车路线是蓝色的粗线，而汽车路线是紫色的细线。 汽车路线经 I-90 穿过华盛顿湖，由于这条路线经过居民区下的隧道，所以会限制危险货物。 卡车路线（指定 USHazmatClass2 货物类型）正确指向不同的公路。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 在地图上可视化交通流量
> * 创建声明出行模式的路线查询
> * 在地图上显示多条路线

可以在此处访问本教程的代码示例：

> [使用 Azure Maps 查找多条路线](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[实时查看此处的示例](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

若要了解有关 Azure Maps 的覆盖范围和功能的更多信息，请参阅：

> [!div class="nextstepaction"]
> [缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)

若要查看更多代码示例和交互式编码体验，请参阅：

> [!div class="nextstepaction"]
> [如何使用地图控件](how-to-use-map-control.md)
