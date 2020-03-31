---
title: 如何使用 Azure 地图空间 IO 模块 |微软 Azure 地图
description: 了解如何使用 Azure 地图 Web SDK 提供的空间 IO 模块。 此模块提供了强大的功能，使开发人员可以轻松地将空间数据与 Azure 地图 Web sdk 集成。
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcfeebc1fcb96cfdf6ea802293eb4027f339815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335214"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>如何使用 Azure 地图空间 IO 模块

Azure 地图 Web SDK 提供**空间 IO 模块**，该模块使用 JavaScript 或 TypeScript 将空间数据与 Azure 地图 Web SDK 集成。 此模块中的强大功能允许开发人员：

- [读取和写入常见的空间数据文件](spatial-io-read-write-spatial-data.md)。 支持的文件格式包括：KML、KMZ、GPX、GeoRSS、GML、GeoJSON 和 CSV 文件，其中包含包含空间信息的列。 还支持已知文本 （WKT）。
- [连接到开放地理空间联盟 （OGC） 服务并与 Azure 地图 Web SDK 集成。将 Web 地图服务 （WMS） 和 Web 地图磁贴服务 （WMTS） 叠加为地图上的图层](spatial-io-add-ogc-map-layer.md)。
- [查询 Web 要素服务 （WFS） 中的数据](spatial-io-connect-wfs-service.md)。
- [覆盖包含样式信息的复杂数据集，并让他们使用最小代码自动呈现](spatial-io-add-simple-data-layer.md)。
- [利用高速XML和分隔文件读取器和编写器类](spatial-io-core-operations.md)。

在本指南中，我们将了解如何在 Web 应用程序中集成和使用空间 IO 模块。

> [!WARNING]
> 仅使用来自您信任的源的数据和服务，尤其是在从其他域引用数据和服务时。 空间 IO 模块确实采取措施将风险降至最低，但最安全的方法是不允许从应用程序开始任何淫人的数据。 

## <a name="prerequisites"></a>先决条件

在使用 Spatial IO 模块之前，需要[创建 Azure 地图帐户](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps)并[获取帐户的主订阅密钥](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)。

## <a name="installing-the-spatial-io-module"></a>安装空间 IO 模块

可以使用以下两个选项之一加载 Azure 地图空间 IO 模块：

* Azure 地图空间 IO 模块的全局托管 Azure CDN。 对于此选项，在 HTML 文件`<head>`的元素中添加对 JavaScript 的引用。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [Azure 地图空间 io](https://www.npmjs.com/package/azure-maps-spatial-io)的源代码可以在本地加载，然后随应用一起托管。 此程序包还包括了 TypeScript 定义。 对于此选项，请使用以下命令安装包：

    ```sh
    npm install azure-maps-spatial-io
    ```

    然后，在 HTML 文档的元素中`<head>`添加对 JavaScript 的引用：

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>使用空间 IO 模块

1. 创建新的 HTML 文件。

2. 加载 Azure 映射 Web SDK 并初始化地图控件。 有关详细信息，请参阅[Azure 地图地图控件](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)指南。 完成此步骤后，HTML 文件应如下所示：

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. 加载 Azure 地图空间 IO 模块。 在本练习中，使用 Azure 地图空间 IO 模块的 CDN。 将下面的引用添加到 HTML`<head>`文件的元素：

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 初始化`datasource`， 并将数据源添加到地图中。 初始化`layer`，并将数据源添加到地图图层。 然后，呈现数据源和图层。 在下一步向下滚动查看完整代码之前，请考虑放置数据源和层代码段的最佳位置。 回想一下，在以编程方式操作地图之前，我们应该等到地图资源准备就绪。

    ```javascript
    var datasource, layer;
    ```

    and

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. 将所有代码放在一起，您的 HTML 代码应类似于以下代码。 此示例演示如何从 URL 读取 XML 文件。 然后，在地图上加载并显示文件的要素数据。 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. 请记住用主`<Your Azure Maps Key>`键替换。 打开 HTML 文件，您将看到类似于下图的结果：

    <center>

    ![空间数据示例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>后续步骤

我们在这里演示的功能只是空间 IO 模块中可用的众多功能之一。 阅读以下指南，了解如何在空间 IO 模块中使用其他功能：

> [!div class="nextstepaction"]
> [添加简单的数据层](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [添加 OGC 地图图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心业务](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)

请参阅 Azure 地图空间 IO 文档：

> [!div class="nextstepaction"]
> [Azure 地图空间 IO 包](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
