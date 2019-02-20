---
title: 如何在 Azure Maps 中的栅格地图上呈现自定义数据 | Microsoft Docs
description: 在 Azure Maps 中的栅格地图上呈现自定义数据。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119038"
---
# <a name="render-custom-data-on-raster-map"></a>在栅格地图上呈现自定义数据

本文介绍如何使用具有图像合成功能的[静态图像服务](https://docs.microsoft.com/rest/api/maps/render/getmapimage)在栅格地图上实现覆盖。 图像合成包括使用自定义图钉、标签和几何图形覆盖等附加数据来恢复光栅图块的功能。 为了呈现自定义图钉、标签和几何图形，我们将使用 Postman 应用程序。 打开 Postman 应用，单击“新建”|“新建”，选择并命名要将其保存到的集合或文件夹，然后单击“保存”。

我们将使用 Azure Maps [数据服务 API](https://docs.microsoft.com/rest/api/maps/data) 来存储和呈现覆盖图层。 


## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户 

若要遵循本指南中的步骤，首先需要参阅[管理帐户和密钥](how-to-manage-account-keys.md)，以创建并管理采用 S1 定价层的帐户订阅。

## <a name="render-pushpins-with-labels-and-custom-image"></a>使用标签和自定义图像呈现图钉

> [!Note]
> 此示例要求使用具有 S0 或 S1 定价层的 Azure Maps 帐户。 

Azure Maps 帐户 S0 SKU 仅支持 `pins` 参数的单一实例，允许用户使用自定义图像呈现 URL 请求中指定的最多 5 个图钉。

若要使用标签和自定义图像呈现图钉，请按照以下步骤操作：

1. 打开 Postman 应用，单击“新建”>“新建”并选择“请求”。 输入呈现图钉的请求名称，选择要将其保存到的集合或文件夹，然后单击“保存”。
    
    ![使用 Postman 上传地理围栏](./media/tutorial-geofence/postman-new.png)

2. 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 发出 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    以下是获得的响应图像。

    ![使用标签呈现自定义图钉](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>从 Azure Maps 数据存储获取数据

> [!Note]
> 此示例要求使用具有 S1 定价层的 Azure Maps 帐户。

还可以通过[数据上传 API](https://docs.microsoft.com/rest/api/maps/mapdata/upload) 获得路径和图钉位置信息。 按照以下步骤上传路径和图钉数据。

1. 在 Postman 应用中，在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 POST HTTP 方法，并输入以下 URL 发出 POST 请求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 单击“参数”，输入用于 POST 请求 URL 的以下键/值对。 将 subscription-key 值替换为 Azure Maps 订阅密钥。
    
    ![Postman 中的键值对参数](./media/how-to-render-custom-data/postman-key-vals.png)

3. 单击“正文”，选择原始输入格式，然后从下拉列表中选择“JSON”作为输入格式。 提供以下 JSON 作为要上传的数据：
    
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

4. 单击“发送”并查看响应标头。 位置标头包含用于访问或下载供将来使用的数据的 URI。 它还包含上传的数据的唯一 `udId`。   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. 使用从数据上传 API 收到的 `udid` 值来呈现地图上的功能。 为此，请在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 响应图像应如下所示：

    ![呈现上传的数据](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>使用颜色和不透明度呈现多边形

> [!Note]
> 此示例要求使用具有 S1 定价层的 Azure Maps 帐户。

可以使用带有[路径参数](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

1. 在 Postman 应用中，在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 发出 GET 请求，使用颜色和不透明度呈现多边形：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

响应图像应如下所示：

![呈现不透明的多边形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>使用圆形呈现多边形并使用自定义标签呈现图钉

> [!Note]
> 此示例要求使用具有 S1 定价层的 Azure Maps 帐户。

可以使用“sc”缩放样式修改器使图钉及其标签变得更大或更小。 这是一个大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修改器的详细信息，请参阅[静态图像服务路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。

按照以下步骤使用圆形呈现多边形并使用自定义标签呈现图钉：

1. 在 Postman 应用中，在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

响应图像应如下所示：

![使用自定义图钉呈现圆形](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>后续步骤

* 浏览 [Azure Maps 获取地图图像 API](https://docs.microsoft.com/rest/api/maps/search) 文档。
* 若要详细了解 Azure Maps 数据服务功能，请参阅[服务文档](https://docs.microsoft.com/rest/api/maps/data)。