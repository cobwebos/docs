---
title: 如何在 Azure Maps 中呈现光栅地图上的自定义数据 |Microsoft Docs
description: Azure Maps 中的光栅地图上呈现自定义数据。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b6343931287ed59363db2715641ca63a814a9c32
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638799"
---
# <a name="render-custom-data-on-a-raster-map"></a>在光栅地图上呈现自定义数据

本文介绍如何将[静态图像服务](https://docs.microsoft.com/rest/api/maps/render/getmapimage)与图像组合功能一起使用, 以允许叠加到光栅地图的顶部。 图像组合包括使用额外数据 (例如自定义图钉、标签和几何叠加) 恢复光栅磁贴的功能。

若要呈现自定义图钉、标签和几何叠加, 可以使用 Postman 应用程序。 您可以使用 Azure Maps[数据服务 api](https://docs.microsoft.com/rest/api/maps/data)来存储和渲染覆盖区。


## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程, 首先需要在 S1 定价层中[创建 Azure Maps 帐户](how-to-manage-account-keys.md)。

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>使用标签和自定义图像渲染图钉

> [!Note]
> 本部分中的过程需要定价层 S0 或 S1 中的 Azure Maps 帐户。

Azure Maps 帐户 S0 层仅支持`pins`参数的单个实例。 使用该功能, 可以在 URL 请求中使用自定义图像最多呈现五个图钉。

若要呈现带标签和自定义图像的图钉, 请完成以下步骤:

1. 创建用于存储请求的集合。 在 Postman 应用中, 选择 "**新建**"。 在 "**新建**" 窗口中, 选择 "**集合**"。 将该集合命名为, 然后选择 "**创建**" 按钮。 

2. 若要创建请求, 请再次选择 "**新建**"。 在 "**新建**" 窗口中, 选择 "**请求**"。 输入图钉的**请求名称**, 选择你在上一步中创建的集合作为要保存请求的位置, 然后选择 "**保存**"。
    
    ![在 Postman 中创建请求](./media/how-to-render-custom-data/postman-new.png)

3. 在 "生成器" 选项卡上选择 "获取 HTTP" 方法, 然后输入以下 URL 创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    生成的图像如下所示:

    ![带有标签的自定义图钉](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>从 Azure Maps 数据存储获取数据

> [!Note]
> 本部分中的过程需要定价层 S1 中的 Azure Maps 帐户。

你还可以使用[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)获取路径和 pin 位置信息。 按照以下步骤上传路径和图钉数据。

1. 在 Postman 应用中, 打开在上一部分中创建的集合中的新选项卡。 在 "生成器" 选项卡上选择 POST HTTP 方法, 然后输入以下 URL 进行 POST 请求:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 在 "**参数**" 选项卡上, 输入以下键/值对, 用于 POST 请求 URL。 将`subscription-key`该值替换为你的 Azure Maps 订阅密钥。
    
    ![Postman 中的键/值参数](./media/how-to-render-custom-data/postman-key-vals.png)

3. 在 "**正文**" 选项卡上, 选择 "原始输入格式", 然后从下拉列表中选择 "JSON" 作为输入格式。 将此 JSON 作为要上传的数据提供:
    
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

4. 选择 "**发送**" 并查看响应标头。 成功请求后, Location 标头将包含状态 URI 以检查上传请求的当前状态。 状态 URI 的格式应为以下格式。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 复制状态 URI, 并将订阅密钥参数追加到它, 其值为你用于上传数据 Azure Maps 帐户订阅密钥。 状态 URI 格式应如下所示:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. 若要获取, udId 在 Postman 应用中打开新选项卡, 然后在 "生成器" 选项卡上选择 "获取 HTTP 方法" 并在状态 URI 发出 GET 请求。 如果数据上传成功, 将在响应正文中收到 udId。 复制 udId。

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 使用从数据上传 API 接收到的值在地图上呈现功能。`udId` 为此, 请在上一节中创建的集合中打开新选项卡。 在 "生成器" 选项卡上选择 "获取 HTTP" 方法, 然后输入此 URL 以发出 GET 请求:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    下面是响应图像:

    ![从 Azure Maps 数据存储获取数据](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>使用颜色和不透明度呈现多边形

> [!Note]
> 本部分中的过程需要定价层 S1 中的 Azure Maps 帐户。


可以使用带有[路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

1. 在 Postman 应用中, 打开前面创建的集合中的新选项卡。 在 "生成器" 选项卡上选择 "获取 HTTP" 方法, 然后输入以下 URL, 以配置用于呈现颜色和不透明度的多边形的 GET 请求:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    下面是响应图像:

    ![渲染不透明多边形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>使用自定义标签呈现圆形和图钉

> [!Note]
> 本部分中的过程需要定价层 S1 中的 Azure Maps 帐户。


您可以使用`sc`缩放样式修饰符使图钉及其标签更大或更小。 此修饰符采用大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修饰符的详细信息, 请参阅[静态图像服务路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。


请按照以下步骤使用自定义标签呈现圆形和图钉:

1. 在 Postman 应用中, 打开前面创建的集合中的新选项卡。 在 "生成器" 选项卡上选择 "获取 HTTP" 方法, 然后输入此 URL 以发出 GET 请求:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    下面是响应图像:

    ![使用自定义图钉呈现圆形](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>后续步骤


* 浏览 [Azure Maps 获取地图图像 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 文档。
* 若要详细了解 Azure Maps Data Service, 请参阅[服务文档](https://docs.microsoft.com/rest/api/maps/data)。

