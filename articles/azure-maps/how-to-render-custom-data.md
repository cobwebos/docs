---
title: Azure Maps 中的光栅地图上的自定义数据的呈现方式 |Microsoft Docs
description: Azure Maps 中的光栅地图上的自定义数据呈现。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086592"
---
# <a name="render-custom-data-on-a-raster-map"></a>呈现栅格地图上的自定义数据

本文介绍如何使用[静态图像服务](https://docs.microsoft.com/rest/api/maps/render/getmapimage)允许基础栅格地图上的覆盖图像组合功能使用。 映像组合包括光栅磁贴，获取与其他数据，如自定义图钉、 标签和 geometry 覆盖层的功能。

若要呈现自定义图钉、 标签和 geometry 覆盖，可以使用 Postman 应用程序。 可以使用 Azure Maps[数据服务 Api](https://docs.microsoft.com/rest/api/maps/data)来存储和呈现叠加。


## <a name="prerequisites"></a>必备组件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，必须先向[创建 Azure Maps 帐户](how-to-manage-account-keys.md)S1 定价层中。

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>呈现图钉使用标签和自定义映像

> [!Note]
> 在本部分中的过程需要 S0 或 S1 定价层中的 Azure Maps 帐户。

Azure Maps 帐户 S0 层支持单个实例的`pins`参数。 它允许你呈现最多五个图钉，URL 请求，使用自定义映像中指定。

若要呈现使用标签和自定义映像的图钉，完成以下步骤：

1. 创建要在其中存储请求的集合。 在 Postman 应用中，选择**新建**。 在中**创建新**窗口中，选择**集合**。 命名集合，并选择**创建**按钮。 

2. 若要创建该请求，请选择**新建**试。 在中**创建新**窗口中，选择**请求**。 输入**请求名称**图钉，请选择要保存该请求，然后选择在其中的位置上一步中创建的集合**保存**。
    
    ![在 Postman 中创建请求](./media/tutorial-geofence/postman-new.png)

3. 选择生成器选项卡上的 GET HTTP 方法并输入以下 URL 来创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    下面是生成的映像：

    ![标签与自定义图钉](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>从 Azure Maps 数据存储获取数据

> [!Note]
> 在本部分中的过程要求 Azure Maps 帐户定价层 S1 中。

此外可以通过使用获取的路径和 pin 的位置信息[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。 按照以下步骤上传路径和图钉数据。

1. 在 Postman 应用中，打开在上一部分中创建的集合的新选项卡。 选择生成器选项卡上的 POST HTTP 方法，并输入以下 URL 来发出 POST 请求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 上**Params**选项卡上，输入以下键/值对，用于 POST 请求 URL。 替换为`subscription-key`与 Azure Maps 订阅密钥的值。
    
    ![在 Postman 中的键/值参数](./media/how-to-render-custom-data/postman-key-vals.png)

3. 上**正文**选项卡上，选择原始的输入的格式并从下拉列表中选择作为输入格式的 JSON。 提供此 JSON 作为要上载的数据：
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 选择**发送**并查看响应标头。 位置标头包含用于访问或下载数据以供将来使用的 URI。 它还包含上传的数据的唯一 `udId`。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. 使用`udId`从数据上传 API 来呈现功能在地图上的收到的值。 若要执行此操作，打开在上一部分中创建的集合中的新选项卡。 选择生成器选项卡上的 GET HTTP 方法，并输入此 URL 发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 下面是生成的映像：

    ![从 Azure Maps 数据存储获取数据](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>呈现一个颜色和不透明度的多边形

> [!Note]
> 在本部分中的过程要求 Azure Maps 帐户定价层 S1 中。


可以使用带有[路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

1. 在 Postman 应用中，打开前面创建的集合的新选项卡。 选择生成器选项卡上的 GET HTTP 方法，并输入以下 URL 来配置 GET 请求来呈现多边形的颜色和不透明度：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

下面是生成的映像：

![呈现一个不透明的多边形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>呈现一个圆形和自定义标签图钉

> [!Note]
> 在本部分中的过程要求 Azure Maps 帐户定价层 S1 中。


您可以图钉使用和使其标签放大或缩小`sc`规模样式修饰符。 此修饰符将大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修饰符的详细信息，请参阅[静态图像服务路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。


请按照下列步骤来呈现一个圆形和图钉自定义标签：

1. 在 Postman 应用中，打开前面创建的集合的新选项卡。 选择生成器选项卡上的 GET HTTP 方法，并输入此 URL 发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

下面是生成的映像：

![呈现的圆形，自定义图钉](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>后续步骤


* 浏览 [Azure Maps 获取地图图像 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 文档。
* 若要了解有关 Azure 地图数据服务的详细信息，请参阅[服务文档](https://docs.microsoft.com/rest/api/maps/data)。

