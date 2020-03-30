---
title: 在栅格地图上渲染自定义数据 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 映射静态图像服务在栅格地图上呈现自定义数据。
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335519"
---
# <a name="render-custom-data-on-a-raster-map"></a>在栅格地图上渲染自定义数据

本文介绍如何使用具有图像合成功能的[静态图像服务](https://docs.microsoft.com/rest/api/maps/render/getmapimage)，以允许在栅格贴图顶部叠加。 图像合成包括返回栅格磁贴的能力，以及自定义图钉、标签和几何叠加等其他数据。

要渲染自定义图钉、标签和几何叠加，可以使用 Postman 应用程序。 可以使用 Azure 地图[数据服务 API](https://docs.microsoft.com/rest/api/maps/data)来存储和呈现叠加。

> [!Tip]
> 使用 Azure 地图 Web SDK 在网页上显示简单地图通常比使用静态图像服务更具成本效益。 Web SDK 使用地图切片，除非用户平移和缩放地图，否则它们通常仅生成每个地图负载的事务的一小部分。 请注意，Azure 地图 Web SDK 具有禁用平移和缩放的选项。 此外，Azure 地图 Web SDK 提供了比静态地图 Web 服务更丰富的数据可视化选项集。  

## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

要完成本文中的过程，首先需要创建 Azure 地图帐户并获取地图帐户密钥。 按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明创建 Azure 地图帐户订阅，然后按照[获取主键](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>使用标签和自定义图像渲染图钉

> [!Note]
> 本节中的过程要求在定价层 S0 或 S1 中使用 Azure 地图帐户。

Azure 映射帐户 S0 层仅支持`pins`参数的单个实例。 它允许您使用自定义图像渲染最多五个在 URL 请求中指定的图钉。

要使用标签和自定义图像渲染图钉，完成以下步骤：

1. 创建用于存储请求的集合。 在"邮递员"应用中，选择 **"新建**"。 在 **"创建新"** 窗口中，选择 **"集合**"。 命名集合并选择"**创建**"按钮。 

2. 要创建请求，请再次选择 **"新建**"。 在 **"创建新"** 窗口中，选择 **"请求**"。 输入图钉**的请求名称**。 选择您在上一步中创建的集合，作为保存请求的位置。 然后，选择 **"保存**"。
    
    ![在邮递员中创建请求](./media/how-to-render-custom-data/postman-new.png)

3. 选择生成器选项卡上的 GET HTTP 方法，然后输入以下 URL 以创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    下面是生成的图像：

    ![带有标签的自定义图钉](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>从 Azure Maps 数据存储获取数据

> [!Note]
> 本节中的过程要求在定价层 S1 中使用 Azure 地图帐户。

您还可以使用[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)获取路径和引脚位置信息。 按照以下步骤上传路径和图钉数据。

1. 在 Postman 应用中，在上一节中创建的集合中打开一个新选项卡。 在生成器选项卡上选择 POST HTTP 方法，然后输入以下 URL 以发出 POST 请求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 在**Params**选项卡上，输入以下用于 POST 请求 URL 的键/值对。 将`subscription-key`该值替换为 Azure 地图订阅密钥。
    
    ![邮递员中的关键/值参数](./media/how-to-render-custom-data/postman-key-vals.png)

3. 在 **"正文"** 选项卡上，选择原始输入格式，并从下拉列表中选择 JSON 作为输入格式。 提供此 JSON 作为要上载的数据：
    
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

4. 选择 **"发送**并查看响应标头"。 成功请求后，Location 标头将包含状态 URI，可在其中检查上传请求的当前状态。 状态 URI 将采用以下格式。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 复制状态 URI 并将订阅键参数追加到其使用 Azure 地图帐户订阅密钥的值。 使用用于上载数据的相同帐户订阅密钥。 状态 URI 格式应如下所示：

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. 要获取 udId，在 Postman 应用中打开一个新选项卡。 在生成器选项卡上选择 GET HTTP 方法。在状态 URI 发出 GET 请求。 如果数据上传成功，将在响应正文中收到一个 udId。 复制 udId。

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 使用从`udId`数据上传 API 接收的值在地图上呈现要素。 为此，在上一节中创建的集合中打开一个新选项卡。 选择生成器选项卡上的 GET HTTP 方法，将 [订阅密钥] 和 [udId] 替换为您的值，然后输入此 URL 以发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    下面是响应图像：

    ![从 Azure Maps 数据存储获取数据](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>渲染具有颜色和不透明的多边形

> [!Note]
> 本节中的过程要求在定价层 S1 中使用 Azure 地图帐户。


可以使用带有[路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

1. 在 Postman 应用中，在之前创建的集合中打开一个新选项卡。 选择生成器选项卡上的 GET HTTP 方法，然后输入以下 URL 以配置 GET 请求以呈现具有颜色和不透明的多边形：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    下面是响应图像：

    ![渲染不透明多边形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>使用自定义标签渲染圆和图钉

> [!Note]
> 本节中的过程要求在定价层 S1 中使用 Azure 地图帐户。


您可以通过添加样式修改器来修改引脚的外观。 例如，要使图钉及其标签更大或更小，请使用`sc`"缩放样式"修改器。 此修改器获取大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修改器的详细信息，请参阅[静态图像服务路径参数](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。


按照以下步骤使用自定义标签渲染圆和图钉：

1. 在 Postman 应用中，在之前创建的集合中打开一个新选项卡。 选择生成器选项卡上的 GET HTTP 方法，然后输入此 URL 以发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    下面是响应图像：

    ![使用自定义图钉渲染圆](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 要更改上一步图钉的颜色，更改"co"样式修改器。 看`pins=default|la15+50|al0.66|lc003C62|co002D62|`，当前颜色将在 CSS 中指定为#002D62。 假设您要将其更改为#41d42a。 在"co"指定器之后写入新的颜色值，如下所示： `pins=default|la15+50|al0.66|lc003C62|co41D42A|`。 发出新的 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    以下是更改引脚颜色后的响应图像：

    ![使用更新的图钉渲染圆](./media/how-to-render-custom-data/circle-updated-pins.png)

同样，您可以更改、添加和删除其他样式修饰符。

## <a name="next-steps"></a>后续步骤


* 浏览 [Azure Maps 获取地图图像 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 文档。
* 要了解有关 Azure 地图数据服务的更多内容，请参阅[服务文档](https://docs.microsoft.com/rest/api/maps/data)。

