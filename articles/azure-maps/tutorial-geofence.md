---
title: 使用 Azure Maps 创建地域隔离区 | Microsoft Docs
description: 使用 Azure Maps 设置地域隔离区。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7bd4c261af4159429a91bd8b425180037eec8c23
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670887"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>使用 Azure Maps 设置地域隔离区

本教程将引导你完成使用 Azure Maps 设置地域隔离区的基本步骤。 本教程中所述的方案可帮助工地管理员监视潜在危险的设备越过指定的施工区域。 工地使用昂贵的设备，并需要遵守相关法规。 这些设备通常必须保留在工地范围以内，在未经许可的情况下不可离开工地。

我们将使用 Azure Maps 数据上传 API 来存储一个地域隔离区，并使用 Azure Maps 地域隔离区 API 来检查设备相对于地域隔离区的位置。 我们将使用 Azure 事件网格来流式传输地域隔离区结果，并根据地域隔离区结果设置通知。
若要详细了解事件网格，请参阅 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)。
本教程将介绍以下操作：

> [!div class="checklist"]
* 使用数据上传 API 在 Azure Maps 数据服务中上传地域隔离区。
*   设置事件网格用于处理地域隔离区事件。
*   设置地域隔离区事件处理程序。
*   使用逻辑应用设置警报以响应地域隔离区事件。
*   使用 Azure Maps 地域隔离区服务 API 跟踪施工资产是否在工地范围内。


## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户 

若要完成本教程中的步骤，首先需要参阅[管理帐户和密钥](how-to-manage-account-keys.md)以创建并管理采用 S1 定价层的帐户订阅。

## <a name="upload-geofences"></a>上传地域隔离区

为了使用数据上传 API 上传工地的地域隔离区，我们将使用 Postman 应用程序。 本教程假设存在一个整体性的施工区域，施工设备不可违反该区域的硬性参数。 违反此隔离区的参数属于严重违规，将向运营经理汇报。 可以使用一组优化的附加隔离区来按计划跟踪整个施工区域内的不同施工场地。 可以假设主要地域隔离区包含子场地 1，该场地设置了过期时间，该时间过后它将会过期。 可根据要求创建更多的嵌套地域隔离区。 例如，子场地 1 的工作可在计划的第 1 至第 4 周进行，而子场地 2 的工作可在第 5 至第 7 周进行。 可在项目开始时，将所有此类隔离区作为单个数据集加载，并使用它们根据时间和空间来跟踪规则。 有关地域隔离区数据格式的详细信息，请参阅[地域隔离区 GeoJSON 数据](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)。 有关将数据上传到 Azure Maps 服务的详细信息，请参阅[数据上传 API 文档](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。

打开 Postman 应用并遵循以下步骤使用 Azure Maps 数据上传 API 上传工地地域隔离区。

1. 打开 Postman 应用，单击“新建”>“新建”并选择“请求”。 输入“上传地域隔离区数据”的请求名称，选择用于保存该请求的集合或文件夹，然后单击“保存”。

    ![使用 Postman 上传地域隔离区](./media/tutorial-geofence/postman-new.png)

2. 在生成器选项卡上选择 POST HTTP 方法，并输入以下 URL 发出 POST 请求。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 路径中的 GEOJSON 参数表示正在上传的数据的数据格式。

3. 单击“参数”，输入用于 POST 请求 URL 的以下键/值对。 将 subscription-key 值替换为 Azure Maps 订阅密钥。
   
    ![Postman 中的键值对参数](./media/tutorial-geofence/postman-key-vals.png)

4. 单击“正文”，选择原始输入格式，然后从下拉列表中选择“JSON”作为输入格式。 提供以下 JSON 作为要上传的数据：

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. 单击“发送”并查看响应标头。 位置标头包含用于访问或下载供将来使用的数据的 URI。 它还包含上传的数据的唯一 `udId`。

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

## <a name="set-up-an-event-handler"></a>设置事件处理程序

若要通知运营经理发生了进入和退出事件，应创建一个用于接收通知的事件处理程序。

我们将创建两个[逻辑应用](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps)服务来处理进入和退出事件。 此外，我们还将在逻辑应用中创建这些事件触发的事件触发器。 思路是每当设备进入或退出工地时，向运营经理发送警报（在本例中为电子邮件）。 下图演示了如何针对地域隔离区进入事件创建逻辑应用。 可按类似方式针对退出事件创建另一个逻辑应用。
有关详细信息，请参阅所有[支持的事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。

1. 在 Azure 门户中创建逻辑应用

  ![创建逻辑应用](./media/tutorial-geofence/logic-app.png)

2. 选择 HTTP 请求触发器，然后在 Outlook 连接器中选择“发送电子邮件”作为操作
  
  ![逻辑应用架构](./media/tutorial-geofence/logic-app-schema.png)

3. 保存该逻辑应用以生成 HTTP URL 终结点并复制 HTTP URL。

  ![逻辑应用终结点](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>创建 Azure Maps 事件订阅

Azure Maps 支持三种事件类型。 可在[此处](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)查看 Azure Maps 支持的事件类型。 我们将创建两个不同的订阅，一个针对进入事件，另一个针对退出事件。

遵循以下步骤针对地域隔离区进入事件创建事件订阅。 可按类似的方式订阅地域隔离区退出事件。

1. 通过[此门户链接](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/)导航到 Azure Maps 帐户，然后选择“事件”选项卡。

   ![Azure Maps 事件](./media/tutorial-geofence/events-tab.png)

2. 若要创建事件订阅，请从“事件”页中选择“事件订阅”。

   ![Azure Maps 事件订阅](./media/tutorial-geofence/create-event-subscription.png)

3. 为事件订阅命名，并订阅“进入”事件类型。 现在，选择“Web Hook”作为“终结点类型”，并将逻辑应用 HTTP URL 终结点复制到“终结点”中

   ![事件订阅](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>使用地域隔离区 API

可以使用地域隔离区 API 来检查**设备**（设备是状态的一部分）是在地域隔离区的内部还是外部。 为了更好地了解地域隔离区 GET API， 我们将针对其中的特定设备在不同时间会发生移动的不同位置查询该 API。 下图演示了所观测到的、具有唯一**设备 ID** 的特定施工设备所处的五个不同位置（按时间顺序列出）。 其中的每个位置用于针对隔离区评估地域隔离区进入和退出状态更改。 如果发生状态更改，地域隔离区服务会触发一个事件，事件网格会将该事件发送到逻辑应用。 因此，运营经理将通过电子邮件收到相应的进入或退出通知。

> [!Note]
> 上述方案和行为基于相同的**设备 ID**，因此，它反映了下图所示的五个不同位置。

![地域隔离区图](./media/tutorial-geofence/geofence.png)

在 Postman 应用中，在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 GET HTTP 方法：

下面是五个 HTTP GET 地域隔离 API 请求，其中包含观测到的、设备的不同相应位置坐标（按时间顺序列出）。 每个请求后接响应正文。
 
1. 位置 1：
    
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  ![地域隔离区查询 1](./media/tutorial-geofence/geofence-query1.png)

  在上述响应中，从主要地域隔离区的距离为负表示该设备在地域隔离区范围之内，从子场地地域隔离区的距离为正表示该设备在子场地地域隔离区范围之外。 

2. 位置 2： 
   
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
    
  ![地域隔离区查询 2](./media/tutorial-geofence/geofence-query2.png)

  仔细查看上述 JSON 响应可以发现，设备在子场地范围之外，但在主要隔离区范围之内。 这不会触发事件，且不会发送电子邮件。

3. 位置 3： 
  
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![地域隔离区查询 3](./media/tutorial-geofence/geofence-query3.png)

  发生了状态更改，设备现在位于主要地域隔离区和子场地地域隔离区的范围之内。 这会发布事件，并向运营经理发送通知电子邮件。

4. 位置 4： 

  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  
  ![地域隔离区查询 4](./media/tutorial-geofence/geofence-query4.png)

   仔细观察相应的响应可以发现，此处即使设备已经退出子场地地域隔离区，也不会发布任何事件。 查看用户在 GET 请求中指定的时间可以发现，子场地地域隔离区相对于此时间已过期，设备仍在主要地域隔离区范围内。 在响应正文中的 `expiredGeofenceGeometryId` 下面还可以看到子场地地域隔离区的几何 ID。


5. 位置 5：
      
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![地域隔离区查询 5](./media/tutorial-geofence/geofence-query5.png)

  可以看到，设备已离开主要工地地域隔离区。 这属于严重的违规，因此将发布事件，并向运营经理发送严重警报电子邮件。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何通过在 Azure Maps 数据服务中使用数据上传 API 上传地域隔离区来设置地域隔离区。 此外，介绍了如何使用 Azure Maps 事件网格来订阅和处理地域隔离区事件。 

* 请参阅[在 Azure 逻辑应用中处理内容类型](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)，了解如何使用逻辑应用来分析 JSON，以生成更复杂的逻辑。
* 若要详细了解事件网格中的事件处理程序，请参阅[事件网格支持的事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。
