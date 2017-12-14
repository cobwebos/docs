---
title: "Azure Location Based Services 的多种路线 | Microsoft 文档"
description: "使用 Azure Location Based Services 查找不同行驶模式的路线"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7d8eb900bdc90a391d4121b7bfb863fc274fc564
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>使用 Azure Location Based Services 查找不同行驶模式的路线

本教程将介绍如何使用你的 Azure Location Based Services 帐户和路线服务 SDK，以查找按行驶模式划分优先级的兴趣点路线。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 配置路线服务查询
> * 呈现按行驶模式划分优先级的路线

## <a name="prerequisites"></a>先决条件

在继续之前，请确保[创建 Azure Location Based Services 帐户](./tutorial-search-location.md#createaccount)，以及[获取帐户的订阅密钥](./tutorial-search-location.md#getkey)。 此外，你还可以观察如何使用 Map Control 和搜索服务 API，如教程[使用 Azure Location Based Services 搜索附近兴趣点](./tutorial-search-location.md)中所讨论的那样，并了解路线服务 API 的基本用法，如教程[使用 Azure Location Based Services 的确定到兴趣点的路线](./tutorial-route-location.md)中所讨论的那样。


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>配置路线服务查询

使用以下步骤创建通过 Location Based Services 的 Map Control API 嵌入的静态 HTML 页面。 

1. 在本地计算机上，创建一个新文件并将其命名为 MapTruckRoute.html。 
2. 将以下 HTML 组件添加到该文件中：

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
    请注意，HTML 标头为 Azure Location Based Services 库嵌入 CSS 和 JavaScript 文件的资源位置。 另请留意添加到 HTML 正文中的脚本段，以包含内联 JavaScript 代码来访问 Azure Map Control API。
3. 将以下 JavaScript 代码添加到 HTML 文件的脚本块。 将占位符 *<insert-key>* 替换为 Location Based Services 帐户的主密钥。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var subscriptionKey = "<insert-key>";
    var map = new atlas.Map("map", {
        "subscription-key": subscriptionKey
    });
    ```
    Atlas.Map 提供一个可视和交互式的 Web 地图控件，它是 Azure Map Control API 的一个组件。

4. 将以下 JavaScript 代码添加到脚本块中，以便将交通流量显示添加到地图中：

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    此代码将交通流量设置为 `relative`，这是相对于自由流动的道路的速度。 此外，还可以将其设置为道路的 `absolute` 速度或 `relative-delay`，它会显示与自由流动不同的相对速度。 

5. 添加以下 JavaScript 代码，创建路线起点和终点的 PIN：

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
    此代码创建两个 [GeoJSON 对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线起始和终点。 

6. 添加以下 JavaScript 代码，将 linestrings 层添加到 Map Control，以便基于运输模式显示路线，例如 car 和 truck。

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

7. 添加以下 JavaScript 代码，将起点和终点添加到地图中：

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
    API map.setCameraBounds 根据起点和终点的坐标调整地图窗口。 API map.addPins 将这些点作为可视组件添加到地图控件中。

8. 将 MapTruckRoute.html 文件保存在你的计算机上。 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>呈现按行驶模式划分优先级的路线

本部分介绍如何使用 Azure Location Based Services 的路线服务 API 根据运输模式查找从指定起点到目的地的多个路线。 路线服务提供多个 API，在考虑到实时交通状况下计划两个地点之间的最快、最短或环保路线。 此外，它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天任何时间的路线时间来规划路线。 

1. 打开在上一部分中创建的 MapTruckRoute.html 文件，并将以下 JavaScript 代码添加到脚本块，以使用路线服务获取卡车的路线。

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
    truckRouteUrl += "&subscription-key=" + subscriptionKey;
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
    
    此代码片段还会将查询发送到路线服务，为帐户的订阅密钥获取指定起点和终点的路线。 以下可选参数用于指示重型卡车的路线：- 参数 `travelMode=truck` 将行驶模式指定为“卡车”。 支持的其他行驶模式有“出租车”、“公交车”、“货车”、“摩托车”和默认的“汽车”。 
        - 参数 `vehicleWidth`、`vehicleHeight` 和 `vehicleLength` 以米为单位指定车辆的尺寸，仅在行驶模式为“卡车”时考虑这些参数。 
        - `vehicleLoadType` 将货物分类为危险物品，并在部分道路上是受限的。 目前也只有“卡车”模式考虑此参数。 

2. 添加以下 JavaScript 代码，以使用路线服务获取一辆汽车的路线：

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
    carRouteUrl += "&subscription-key=" + subscriptionKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    此代码片段将创建另一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，并添加事件处理程序以分析传入的响应。 对于成功的响应，它创建返回路线的坐标数组并将其添加地图的 `carRouteLayerName` 层。 
    
    此代码片段还会将查询发送到路线服务，为帐户的订阅密钥获取指定起点和终点的路线。 由于没有使用其他参数，因此，将返回默认行驶车辆模式的路线。 

3. 将 MapTruckRoute.html 文件保存在本地，然后在你选择的 Web 浏览器中打开它并观察结果。 要成功连接 Location Based Services 的 API，应看到类似于以下内容的地图。 

    ![使用 Azure 路线服务设置路线的优先级](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    请注意，卡车路线为蓝色，而汽车路线为紫色。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 配置路线服务查询
> * 呈现按行驶模式划分优先级的路线

继续阅读“概念”和“操作说明”文章，以深入了解 Azure Location Based Services SDK。 
