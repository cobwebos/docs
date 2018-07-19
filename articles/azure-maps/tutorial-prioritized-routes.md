---
title: 使用 Azure Maps 查找多条路线 | Microsoft Docs
description: 使用 Azure Maps 查找不同出行模式的路线
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 83ca46ecb8f0cce2ff8c749016eb3ad1ac7df7cf
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988963"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>使用 Azure Maps 查找不同出行模式的路线

本教程将介绍如何使用 Azure Maps 帐户和路线服务，以查找按行驶模式划分优先级的兴趣点路线。 地图上显示了两条路线，一条针对汽车，另一条针对可能有路线限制（因为高度、重量或者危险货物）的卡车。 本教程介绍如何执行以下操作：

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
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    
    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML 标头如何为 Azure Maps 库嵌入 CSS 和 JavaScript 文件的资源位置。 HTML 正文的脚本段将包含地图的内联 JavaScript 代码。
3. 将以下 JavaScript 代码添加到 HTML 文件的 *script* 块。 使用从 Maps 帐户复制的主要密钥替换字符串 \<your account key\>。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Atlas.Map 提供一个可视和交互式的 Web 地图控件，它是 Azure Map Control API 的一个组件。

4. 保存文件并在浏览器中打开它。 此时，你有一个可以进一步开发的基础地图。 

   ![查看基础地图](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>可视化交通流量

1. 如果未向地图指示焦点位置，则显示全世界视图。 若要查看交通流量，请在地图上设置中心点和缩放级别。 使用以下 JavaScript 代码替换声明 `new atlas.Map` 的代码： 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    此代码设置地图的中心点并声明缩放级别，以便可以默认聚焦于一个特定区域。 

1. 将交通流量显示添加到地图：

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    此代码将交通流量设置为 `relative`，这是相对于自由流动的道路的速度。 此外，还可以将其设置为道路的 `absolute` 速度或 `relative-delay`，它会显示与自由流动不同的相对速度。 

2. 保存“MapTruckRoute.html”文件并刷新浏览器中的页。 应看到洛杉矶街道及其当前交通数据。

   ![查看交通地图](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>设置起点和终点

在本教程中，将起点设为西雅图一家名为 Fabrikam 的虚构公司，终点设为 Microsoft 办公室。 

1. 添加以下 JavaScript 代码，创建路线起点和终点的 PIN：

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    此代码创建两个 [GeoJSON 对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线的起点和终点。 

2. 添加以下 JavaScript 代码，将起点和终点添加到地图中：

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    map.setCameraBounds 调用根据起点和终点的坐标调整地图窗口。 API **map.addPins** 将这些点作为可视组件添加到地图控件中。

3. 保存文件并刷新浏览器以查看地图上显示的图钉。 即使已经声明将地图的中心点设在洛杉矶，map.setCameraBounds 仍会移动视图以显示起点和终点。 

   ![查看有起点和终点的地图](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>呈现按行驶模式划分优先级的路线

本部分介绍如何使用 Maps 的路线服务 API 根据运输模式查找从指定起点到目的地的多个路线。 路线服务提供多个 API，在考虑到当前交通状况的情况下，规划两个地点之间最快、最短、环保或令人兴奋的路线。 此外，它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天任何时间的路线时间来规划路线。 有关详细信息，请参阅 [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)（获取路线指示）。


1. 首先，在地图上添加一个新层以显示路线路径或 linestring。 在本教程中，有两种不同的路线 -“汽车路线”和“卡车路线”，每种路线有自己的样式。 将以下 JavaScript 代码添加到脚本块：

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. 将以下 JavaScript 代码添加到脚本块，请求卡车路线并在地图上显示结果：

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    此代码片段将创建一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，并添加事件处理程序以分析传入的响应。 对于成功的响应，它创建返回路线的坐标数组并将其添加地图的 `truckRouteLayerName` 层。 
    
    此代码片段还使用帐户密钥创建 Maps 路线服务的查询。 该查询包含起点坐标、终点坐标和指示该路线适用于重型卡车的可选参数。

2. 添加下面的 JavaScript 代码，请求汽车路线并显示结果：

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    此代码片段将创建另一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，并添加事件处理程序以分析传入的响应。 对于成功的响应，它创建返回路线的坐标数组并将其添加到地图的 `carRouteLayerName` 层。 
    
    此代码片段还使用帐户密钥创建 Maps 路线服务的查询。 查询包含起点坐标和终点坐标。 因为未提供其他参数，路线服务的出行模式默认为“汽车”。 

3. 保存“MapTruckRoute.html”文件并刷新浏览器以查看结果。 要成功连接 Maps 的 API，应看到类似于以下内容的地图。 

    ![使用 Azure 路线服务设置路线的优先级](./media/tutorial-prioritized-routes/prioritized-routes.png)

    卡车路线是蓝色的粗线，而汽车路线是紫色的细线。 汽车路线经 I-90 穿过华盛顿湖，由于这条路线经过居民区下的隧道，所以会限制危险货物。 卡车路线（指定 USHazmatClass2 货物类型）正确指向不同的公路。 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 在地图上可视化交通流量
> * 创建声明出行模式的路线查询
> * 在地图上显示多条路线

若要了解有关 Azure Maps 的覆盖率和功能的详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)以及其他概念型文章。 

有关更多代码示例和交互式编码体验，请参阅[如何使用地图控件](how-to-use-map-control.md)和其他操作说明型指南。 
