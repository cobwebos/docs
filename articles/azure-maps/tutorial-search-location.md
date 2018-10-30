---
title: 使用 Azure Maps 进行搜索 | Microsoft Docs
description: 使用 Azure Maps 搜索附近兴趣点
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 10fb30b77cc3cd18cbb6b3def9682349474fba71
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645808"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>使用 Azure Maps 搜索附近兴趣点

本教程演示如何为 Azure Maps 设置帐户，然后使用 Maps API 搜索兴趣点。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Azure Maps 帐户
> * 检索 Maps 帐户的主要密钥
> * 使用 Map Control API 创建新网页
> * 使用 Maps 搜索服务查找附近的兴趣点

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>通过 Azure Maps 创建帐户

使用以下步骤创建新的 Maps 帐户：

1. 在 [Azure 门户](https://portal.azure.com)的左上角单击“创建资源”。
2. 在“在市场中搜索”框中，键入“Maps”。
3. 从“结果”中，选择“Maps”。 单击地图下面显示的“创建”按钮。
4. 在“创建 Maps 帐户”页上，输入以下值：
    * 新帐户的名称。
    * 要用于此帐户的订阅。
    * 此帐户的资源组名称。 可以选择新建或使用现有的资源组。
    * 选择“资源组位置”。
    * 阅读许可证和隐私声明，并选择复选框接受这些条款。
    * 单击“创建”  按钮。

    ![在门户中创建 Maps 帐户](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>获取帐户的主密钥

成功创建 Maps 帐户后，检索查询 Maps API 的密钥。

1. 在门户中打开 Maps 帐户。
2. 在设置部分中，选择“密钥”。
3. 将“主密钥”复制到剪贴板。 本地保存它以便稍后在本教程中使用。

    ![在门户中获取主密钥](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>创建新地图

Map Control API 是一个方便的客户端库，使用它可以轻松将 Maps 集成到 Web 应用程序中。 它消除了单纯 REST 服务调用的复杂性，并可以通过有样式且可自定义的组件促升工作效率。 以下步骤说明如何使用 Map Control API 创建一个嵌入式静态 HTML 页面。

1. 在本地计算机上，创建一个新文件并将其命名为 **MapSearch.html**。
2. 将以下 HTML 组件添加到该文件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>

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
    请注意，HTML 标头包含 Azure 地图控件库托管的 CSS 和 JavaScript 资源文件。 请注意已在 HTML 文件的正文中添加 *script* 段。 此段将会包含用于访问 Azure Maps API 的内联 JavaScript 代码。

3. 将以下 JavaScript 代码添加到 HTML 文件的 *script* 块。 使用从 Maps 帐户复制的主要密钥替换字符串 \<your account key\>。

    ```JavaScript
    // Instantiate map to the div with id "map"
    atlas.setSubscriptionKey("<your account key>");
    var map = new atlas.Map("map");
    ```

    此段为 Azure Maps 帐户密钥初始化地图控件 API。 **Atlas** 是包含 API 和相关视觉组件的命名空间。 **Atlas.Map** 提供视觉对象和交互式 Web 地图的控件。

4. 将更改保存到文件并在浏览器中打开 HTML 页。 这是使用帐户密钥调用“atlas.map”所能生成的最基本的地图。

   ![查看地图](./media/tutorial-search-location/basic-map.png)

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>添加搜索功能

本部分演示如何使用 Maps Search API 在地图上查找兴趣点。 这是一个 RESTful API，可让开发人员搜索地址、兴趣点和其他地理信息。 搜索服务会将纬度和经度信息分配到指定的地址。 下面介绍的“服务模块”可以用来通过地图搜索 API 搜索位置。

### <a name="service-module"></a>服务模块

1. 为地图添加新层以显示搜索结果。 将以下 Javascript 代码添加到脚本块，在初始化地图的代码后。

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    ```

2. 若要实例化客户端服务，请将以下 Javascript 代码添加到脚本块，在初始化地图的代码后。

    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. 加载地图后，应加载地图上的所有函数。 可以通过将所有地图函数都放在地图的 eventListener 块中来确保这一点。 添加以下代码行将 [eventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 添加到地图中，以确保在添加功能之前完全加载地图。
    
    ```JavaScript
         map.events.add("load", function() {
         });
    ```

4. **在地图加载事件中**添加以下脚本块来构建查询。 它使用模糊搜索服务，这是搜索服务的基本搜索 API。 模糊搜索服务可处理最模糊的输入，例如地址和兴趣点 (POI) 标记的任意组合。 它在指定半径内搜索邻近的加油站。 然后，响应会解析成 GeoJSON 格式并转换为点特征，作为图钉添加到地图中。 脚本的最后一部分使用地图的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 属性为地图添加照相机边界。

    ```JavaScript

            // Execute a POI search query then add pins to the map for each result once a response is received
            client.search.getSearchFuzzy("gasoline station", {
            lat: 47.6292,
            lon: -122.2337,
            radius: 100000
            }).then(response => {
       
            // Parse the response into GeoJSON 
            var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);

            // Create the point features that will be added to the map as pins
            var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => {
               var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat];
               return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                name: poiResult.properties.poi.name,
                address: poiResult.properties.address.freeformAddress,
                position: poiPosition[1] + ", " + poiPosition[0]
               });
            });

            // Add pins to the map for each POI
            map.addPins(searchPins, {
               name: searchLayerName,
               cluster: false, 
               icon: "pin-round-darkblue" 
            });

            // Set the camera bounds
            map.setCameraBounds({
               bounds: geojsonResponse.getGeoJsonResults().bbox,
               padding: 50
            });
    });
    ```
5. 保存“MapSearch.html”文件并刷新浏览器。 现在应看到地图以西雅图为中心，蓝色的图钉标记了该区域中加油站的位置。

   ![查看包含搜索结果的地图](./media/tutorial-search-location/pins-map.png)

6. 在浏览器中输入以下 HTTPRequest，即可看到地图呈现的原始数据。 使用主要密钥替换帐户密钥\<\>。

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

此时，MapSearch 页可显示模糊搜索查询返回的兴趣点的位置。 让我们添加一些交互功能和有关位置的详细信息。

## <a name="add-interactive-data"></a>添加交互式数据

现在为止已完成的地图仅显示搜索结果的纬度/经度数据。 但是，如果查看该 Maps Search 服务返回的原始 JSON，会看到它包含有关每个加油站的其他信息，包括名称和街道地址。 可以使用交互式弹出框将数据合并到地图。

1. 将以下行添加到 *script* 块，以创建搜索服务返回的兴趣点的弹出窗口：

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API **atlas.Popup** 提供一个固定在地图上所需位置的信息窗口。 此代码片段设置弹出窗口的内容和位置。 它还向 `map` 控件添加一个事件侦听器，用于等待_鼠标_在弹出窗口上滚动。

2. 保存文件并刷新浏览器。 现在，将鼠标悬停在任何搜索图钉上时，浏览器中的地图显示信息弹出窗口。

    ![Azure 地图控件和搜索服务](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 通过 Azure Maps 创建帐户
> * 获取帐户的主密钥
> * 使用地图控件 API 创建新网页
> * 使用搜索服务查找附近的兴趣点

可以在此处访问本教程的代码示例：

> [使用 Azure Maps 搜索位置](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/search.html)

下一教程演示如何显示两个地点之间的路线。

> [!div class="nextstepaction"]
> [目的地路线](./tutorial-route-location.md)
