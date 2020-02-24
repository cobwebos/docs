---
title: 教程：在地图上创建地理围栏并跟踪设备 | Microsoft Azure Maps
description: 了解如何使用 Microsoft Azure Maps 空间服务设置地理围栏并跟踪相对于地理围栏的设备。
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e0e918a2508bb65de176b030a73598e221aff676
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370162"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>教程：使用 Azure Maps 设置地域隔离区

本教程将引导你完成使用 Azure Maps 设置地理围栏的基本步骤。 请考虑这种情况：工地管理员必须监视潜在的危险设备。 该管理员需确保设备处于所选的整个施工区域中。 这整个施工区域是一个硬参数。 法规要求设备在此参数内保持不变，遇到冲突情况需报告给运营管理员。  

我们使用数据上传 API 来存储一个地理围栏，并使用地理围栏 API 来检查设备相对于地理围栏的位置。 数据上传 API 和地理围栏 API 都来自 Azure Maps。 我们还使用 Azure 事件网格来流式传输地理围栏结果，并根据地理围栏结果设置通知。 若要详细了解事件网格，请参阅 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)。

在本教程中，我们介绍如何执行以下操作：

> [!div class="checklist"]
> * 使用数据上传 API 在 Azure Maps 数据服务中上传地域隔离区。
> *   设置事件网格用于处理地域隔离区事件。
> *   设置地域隔离区事件处理程序。
> *   使用逻辑应用设置警报以响应地域隔离区事件。
> *   使用 Azure Maps 地域隔离区服务 API 跟踪施工资产是否在工地范围内。


## <a name="prerequisites"></a>必备条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户 

按照[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)中的说明，在 S1 定价层中创建一个 Azure Maps 帐户订阅。 [获取主键](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤说明了如何检索帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

## <a name="upload-geofences"></a>上传地域隔离区

我们假设主要地理围栏为子场地 1，它有一个设置好的过期时间。 可根据要求创建更多的嵌套地域隔离区。 可以使用这些围栏集按计划跟踪整个施工区域内的不同施工场地。 例如，子场地 1 的工作可在计划的第 1 至第 4 周进行， 而子场地 2 的工作可在第 5 至第 7 周进行。 可在项目开始时，将所有此类围栏作为单个数据集加载。 这些围栏用于根据时间和空间来跟踪规则。 

为了通过数据上传 API 上传工地的地理围栏，我们使用 Postman 应用程序。 安装 [Postman 应用程序](https://www.getpostman.com/)并创建一个免费帐户。 

安装 Postman 应用以后，请按以下步骤使用 Azure Maps 数据上传 API 上传工地地理围栏。

1. 打开 Postman 应用，单击“新建”>“新建”并选择“请求”。 输入“上传地域隔离区数据”的请求名称，选择用于保存该请求的集合或文件夹，然后单击“保存”。

    ![使用 Postman 上传地理围栏](./media/tutorial-geofence/postman-new.png)

2. 在生成器选项卡上选择 POST HTTP 方法，并输入以下 URL 发出 POST 请求。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 路径中的 GEOJSON 参数表示正在上传的数据的数据格式。

3. 单击“参数”，输入用于 POST 请求 URL 的以下键/值对。  将 {subscription-key} 替换为你的 Azure Maps 订阅密钥，也称主密钥。
   
    ![Postman 中用于上传数据的参数（地理围栏）](./media/tutorial-geofence/postman-key-vals.png)

4. 单击“正文”，选择原始输入格式，然后从下拉列表中选择“JSON”作为输入格式  。 提供以下 JSON 作为要上传的数据：

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

5. 单击“发送”并查看响应标头。 成功请求后，**Location** 标头会包含状态 URI。 状态 URI 采用以下格式。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 复制状态 URI 并追加 subscription-key。 状态 URI 格式应如下所示。 请注意，在下面的格式中，你将使用自己的订阅密钥来替换 {subscription-key}（包括 {}）。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. 若要获取 `udId`，请在 Postman 应用中打开一个新选项卡，在生成器选项卡上选择 GET HTTP 方法。在上一步的状态 URI 中发出 GET 请求。 如果数据上传成功，则会在响应正文中收到 udId。 请复制该 udId 供稍后使用。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>设置事件处理程序

在本部分中，我们创建一个用于接收通知的事件处理程序。 此事件处理程序应将任何设备的进入和退出事件通知给运营管理员。

我们将生成两项[逻辑应用](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps)服务来处理进入和退出事件。 逻辑应用中的事件触发时，会按顺序触发更多事件。 可以向运营管理员发送警报（在本例中为电子邮件）。 下图演示了如何针对地域隔离区进入事件创建逻辑应用。 可按类似方式针对退出事件创建另一个逻辑应用。 有关详细信息，请参阅所有[支持的事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。

1. 在 Azure 门户中创建逻辑应用。 在 Azure 市场中选择逻辑应用。 然后，选择“创建”按钮。 

   ![创建 Azure 逻辑应用以处理地理围栏事件](./media/tutorial-geofence/logic-app.png)

2. 在逻辑应用的设置菜单上，导航到“逻辑应用设计器” 

3. 选择 HTTP 请求触发器，然后选择“新建步骤”。 在 Outlook 连接器中，选择“发送电子邮件”作为操作
  
   ![逻辑应用架构](./media/tutorial-geofence/logic-app-schema.png)

4. 填充用于发送电子邮件的字段。 保留 HTTP URL，单击“保存”后，它会自动生成

   ![生成逻辑应用终结点](./media/tutorial-geofence/logic-app-endpoint.png)

5. 保存该逻辑应用以生成 HTTP URL 终结点并复制 HTTP URL。

## <a name="create-an-azure-maps-events-subscription"></a>创建 Azure Maps 事件订阅

Azure Maps 支持三种事件类型。 可在[此处](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)查看 Azure Maps 支持的事件类型。 我们需要两个不同的事件订阅，一个用于输入事件，另一个用于退出事件。

遵循以下步骤针对地域隔离区进入事件创建事件订阅。 可按类似的方式订阅地域隔离区退出事件。

1. 导航到 Azure Maps 帐户。 在仪表板中，选择“订阅”。 单击订阅名称，然后在设置菜单中命名并选择“事件”。 

   ![导航到 Azure Maps 帐户事件](./media/tutorial-geofence/events-tab.png)

2. 若要创建事件订阅，请从“事件”页中选择“事件订阅”。

   ![创建 Azure Maps 事件订阅](./media/tutorial-geofence/create-event-subscription.png)

3. 为事件订阅命名，并订阅“进入”事件类型。 现在，选择“Web Hook”作为“终结点类型”。 单击“选择终结点”，将逻辑应用 HTTP URL 终结点复制到“{Endpoint}”中

   ![Azure Maps 事件订阅详细信息](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>使用地域隔离区 API

可以使用地理围栏 API 来检查**设备**是在地理围栏内部还是外部。 让我们针对不同位置（其中的特定设备随时间而移动）查询地理围栏 GET API。 下图演示了五个施工设备所处的五个位置。 

> [!Note]
> 该方案和行为基于相同的**设备 ID**，因此，它反映了下图所示的五个不同位置。

在查询设备位置时，“deviceId”是在 GET 请求中为设备提供的唯一 ID。 向“搜索地理围栏 - GET API”  发出异步请求时，可以借助“deviceId”针对该设备发布地理围栏事件（相对于指定的地理围栏）。 在本教程中，我们已通过唯一的“deviceId”向 API 发出了异步请求。 本教程中的请求按时间顺序发出，如图所示。 每当设备进入或退出地理围栏时，响应中的“isEventPublished”属性就会发布。 无需注册设备即可完成本教程。

让我们回顾一下关系图。 其中的每个位置用于针对隔离区评估地域隔离区进入和退出状态更改。 如果发生状态更改，地域隔离区服务会触发一个事件，事件网格会将该事件发送到逻辑应用。 因此，运营管理员会通过电子邮件收到相应的进入或退出通知。

![Azure Maps 中的地理围栏图](./media/tutorial-geofence/geofence.png)

在 Postman 应用中，在上面创建的同一集合中打开一个新的选项卡。 在生成器选项卡上选择 GET HTTP 方法：

下面是五个 HTTP GET 地理围栏 API 请求，其中包含设备的不同位置坐标。 这些坐标是按时间顺序观察到的。 每个请求后接响应正文。
 
1. 位置 1：
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![地域隔离区查询 1](./media/tutorial-geofence/geofence-query1.png)

   在上述响应中，与主要地理围栏的距离为负表示设备在地理围栏内。 与子场地地理围栏的距离为正表示设备在子场地地理围栏外。 

2. 位置 2： 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![地域隔离区查询 2](./media/tutorial-geofence/geofence-query2.png)

   仔细查看上述 JSON 响应可以发现，设备在子场地范围之外，但在主要围栏之内。 不触发任何事件，也不发送任何电子邮件。

3. 位置 3： 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地域隔离区查询 3](./media/tutorial-geofence/geofence-query3.png)

   发生了状态更改，设备现在位于主要地域隔离区和子场地地域隔离区的范围之内。 此更改导致系统发布事件，并向运营管理员发送通知电子邮件。

4. 位置 4： 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![地域隔离区查询 4](./media/tutorial-geofence/geofence-query4.png)

   仔细观察相应的响应可以发现，此处即使设备已经退出子场地地域隔离区，也不会发布任何事件。 查看用户在 GET 请求中的指定时间可以发现，子场地地理围栏此时已过期。 设备仍在主地理围栏内。 在响应正文中的 `expiredGeofenceGeometryId` 下面还可以看到子场地地域隔离区的几何 ID。


5. 位置 5：
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地域隔离区查询 5](./media/tutorial-geofence/geofence-query5.png)

   可以看到，设备已离开主要工地地域隔离区。 事件已发布，警报电子邮件已发送给运营管理员。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何通过在 Azure Maps 和数据服务中使用数据上传 API 上传地理围栏来设置地理围栏。 此外，介绍了如何使用 Azure Maps 事件网格来订阅和处理地域隔离区事件。 

* 请参阅[在 Azure 逻辑应用中处理内容类型](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)，了解如何使用逻辑应用来分析 JSON，以生成更复杂的逻辑。
* 若要详细了解事件网格中的事件处理程序，请参阅[事件网格支持的事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。
