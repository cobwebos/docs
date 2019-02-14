---
title: 使用 Azure Maps 进行搜索 | Microsoft Docs
description: 使用 Azure Maps 搜索附近兴趣点
author: walsehgal
ms.author: v-musehg
ms.date: 01/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ce425278bfc0f9b95285c33e9863b508246d5e79
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992282"
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
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        
        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>
        
        <script>        
        var map, datasource, client, popup;
        
        function GetMap(){
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
            
            #map {
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

3. 将以下 JavaScript 代码添加到 HTML 文件的 `GetMap` 函数。 使用从 Maps 帐户复制的主键替换字符串 **\<Your Azure Maps Key\>**。

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   此段为 Azure Maps 帐户密钥初始化地图控件 API。 **atlas** 是包含 API 和相关视觉组件的命名空间。 **atlas.Map** 提供视觉对象和交互式 Web 地图的控件。 

4. 将更改保存到文件并在浏览器中打开 HTML 页。 这是使用帐户密钥调用“atlas.map”所能生成的最基本的地图。

   ![查看地图](./media/tutorial-search-location/basic-map.png)

5. 在 `GetMap` 函数中，请在初始化地图以后，添加以下 JavaScript 代码。 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

   });
   ```

   将会向地图添加一个加载事件。当地图资源完全加载以后，该事件会触发。 在地图加载事件处理程序中，将会创建一个数据源来存储结果数据。 将会创建一个符号层并将其附加到数据源。 此层指定如何呈现数据源中的结果数据。在此示例中，深蓝色的圆形针位于结果坐标的中心，其他图标可以与其重叠。 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>添加搜索功能

本部分演示如何使用 Maps Search API 在地图上查找兴趣点。 这是一个 RESTful API，可让开发人员搜索地址、兴趣点和其他地理信息。 搜索服务会将纬度和经度信息分配到指定的地址。 下面介绍的“服务模块”可以用来通过地图搜索 API 搜索位置。

### <a name="service-module"></a>服务模块

1. 在地图加载事件处理程序中，通过添加以下 Javascript 代码来实例化客户端服务。

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. 接下来添加以下脚本块，以便生成搜索查询。 它使用模糊搜索服务，这是搜索服务的基本搜索 API。 模糊搜索服务可处理最模糊的输入，例如地址、位置和兴趣点 (POI)。 此代码在指定半径内搜索邻近的加油站。 然后，响应会解析成 GeoJSON 格式并添加到数据源，后者自动通过符号层在地图上呈现数据。 脚本的最后一部分使用地图的 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性通过结果的边框设置地图相机视图。 将会添加一个衬距来弥补符号图标的像素尺寸，因为边框是根据坐标计算的。 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. 保存“MapSearch.html”文件并刷新浏览器。 现在应看到地图以西雅图为中心，蓝色的图钉标记了该区域中加油站的位置。

   ![查看包含搜索结果的地图](./media/tutorial-search-location/pins-map.png)

4. 在浏览器中输入以下 HTTPRequest，即可看到地图呈现的原始数据。 将 \<Your Azure Maps Key\> 替换为主键。

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

此时，MapSearch 页可显示模糊搜索查询返回的兴趣点的位置。 让我们添加一些交互功能和有关位置的详细信息。

## <a name="add-interactive-data"></a>添加交互式数据

现在为止已完成的地图仅显示搜索结果的经度/纬度数据。 但是，如果查看该 Maps Search 服务返回的原始 JSON，会看到它包含有关每个加油站的其他信息，包括名称和街道地址。 可以使用交互式弹出框将数据合并到地图。

1. 在地图加载事件处理程序中，在用于查询模糊搜索服务的代码后面添加以下代码行。 这样会创建弹出窗口的实例，并向符号层添加鼠标悬停事件。

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    API **atlas.Popup** 提供一个固定在地图上所需位置的信息窗口。 
      
2. 在 *script* 标记中的 `GetMap` 函数后面添加以下代码，以便在弹出窗口中显示鼠标悬停结果信息。 

   ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
   ```

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

> [使用 Azure Maps 搜索位置](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[实时查看此处的示例](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

下一教程演示如何显示两个地点之间的路线。

> [!div class="nextstepaction"]
> [目的地路线](./tutorial-route-location.md)
