---
title: "使用 Azure Location Based Services 查找路线 | Microsoft Docs"
description: "使用 Azure Location Based Services 查找兴趣点的路线"
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
ms.openlocfilehash: 0f784e8ecd8fc94c12df1a819055718e06547b6b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>使用 Azure Location Based Services 查找兴趣点的路线

本教程介绍如何使用 Azure Location Based Services 帐户和路线服务 SDK 来查找兴趣点的路线。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 获取地址坐标
> * 在路线服务中查询兴趣点的方向

## <a name="prerequisites"></a>先决条件

在继续操作之前，请务必[创建 Azure Location Based Services 帐户](./tutorial-search-location.md#createaccount)并[获取帐户的订阅密钥](./tutorial-search-location.md#getkey)。 此外，可以根据[使用 Azure Location Based Services 搜索附近的兴趣点](./tutorial-search-location.md)中所述，了解如何使用地图控件和搜索服务 API。


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>获取地址坐标

使用以下步骤创建通过 Location Based Services 地图控件 API 嵌入的静态 HTML 页面。 

1. 在本地计算机上，创建一个新文件并将其命名为 **MapRoute.html**。 
2. 将以下 HTML 组件添加到该文件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    请注意 HTML 标头如何为 Azure Location Based Services 库嵌入 CSS 和 JavaScript 文件的资源位置。 另请留意 HTML 文件正文中的 *script* 段，其中将会包含用于访问 Azure Location Based Services API 的内联 JavaScript 代码。

3. 将以下 JavaScript 代码添加到 HTML 文件的 *script* 块。 将占位符 *<insert-key>* 替换为 Location Based Services 帐户的主密钥。

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    **atlas.Map** 提供视觉和交互式 Web 地图的控件，它是 Azure 地图控件 API 的一个组件。

4. 将以下 JavaScript 代码添加到 *script* 块。 这会将一个 *linestrings* 层添加到地图控件以显示路线：

    ```HTML
            // Initialize the linestring layer for routes on the map
            var routeLinesLayerName = "routes";
            map.addLinestrings([], {
                name: routeLinesLayerName,
                color: "#2272B9",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    ```

5. 添加以下 JavaScript 代码用于创建路线的起点和终点：

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Microsoft",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Contoso Oil & Gas",
                icon: "pin-blue"
            });
    ```
    此代码创建两个 [GeoJSON 对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线的起点和终点。 终点是在前一篇教程[使用 Azure Location Based Services 搜索附近的兴趣点](./tutorial-search-location.md)中搜索到的某个加油站的纬度/经度组合。

6. 添加以下 JavaScript 代码，将起点和终点的图钉添加到地图中：

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    API **map.setCameraBounds** 根据起点和终点的坐标调整地图窗口。 API **map.addPins** 将这些点作为可视组件添加到地图控件中。

7. 在计算机上保存 **MapRoute.html** 文件。 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>在路线服务中查询兴趣点的方向

本部分介绍如何使用 Azure Location Based Services 的路线服务 API 查找从给定起点到目的地的路线。 路线服务提供多个 API，在考虑到实时交通状况的情况下，规划两个地点之间最快、最短或环保的路线。 它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天和任何时间的路线时间来规划路线。 

1. 打开在上一部分中创建的 **MapRoute.html** 文件，并将以下 JavaScript 代码添加到 *script* 块以演示路线服务。

    ```HTML
            // Perform a request to the route service and draw the resulting route on the map
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(xhttp.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
                }
            };
    ```
    此代码片段创建 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，并添加事件处理程序来分析传入的响应。 如果返回了成功响应，此代码片段会针对返回的第一条路线的线段构造坐标数组。 然后，它将此路线的此坐标集添加到地图的 *linestrings* 层。

2. 将以下代码添加到 *script* 块，以便将 XMLHttpRequest 发送到 Azure Location Based Services 的路线服务：

    ```HTML
            var url = "https://atlas.microsoft.com/route/directions/json?";
            url += "&api-version=1.0";
            url += "&subscription-key=" + subscriptionKey;
            url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    
            xhttp.open("GET", url, true);
            xhttp.send();
    ```
    上述请求按给定的顺序显示所需的参数，分别为帐户的订阅密钥、起点和终点的坐标。 

3. 将 **MapRoute.html** 文件保存在本地，然后在所选的 Web 浏览器中打开它并观察结果。 如果与 Location Based Services 的 API 成功建立连接，应会看到如下所示的地图。 

    ![Azure 地图控件和路线服务](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 获取地址坐标
> * 在路线服务中查询兴趣点的方向

继续学习[使用 Azure Location Based Services 查找不同旅行模式的路线](./tutorial-prioritized-routes.md)，了解如何使用 Azure Location Based Services，根据交通模式优先规划兴趣点的路线。 
