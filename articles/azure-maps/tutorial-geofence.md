---
title: 教程：在 Microsoft Azure Map 上创建地理围栏并跟踪设备
description: 了解如何设置地理围栏。 请参阅如何使用 Azure Maps 空间服务跟踪与地理围栏相关的设备。
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ea9923dd98a49b1533defa3e95616655b7ea78d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299297"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>教程：使用 Azure Maps 设置地域隔离区

本教程将引导你完成在以下场景中创建和使用 Azure Maps 地理围栏服务的基本步骤：

*负责构造的站点管理员需要在设备进入和退出构造区域周界范围时对其进行跟踪。每当有设备退出或进入这些范围时，运营管理员会收到邮件通知*。

Azure Maps 提供许多服务来支持上述场景中对进入和退出构造区域的设备的跟踪。 在本教程中，我们介绍如何执行以下操作：

> [!div class="checklist"]
> * 上传用于定义要监视的构造站点区域的[地理围栏 GeoJSON 数据](geofence-geojson.md)。 我们将使用[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) 将地理围栏作为多边形坐标上传到你的 Azure Maps 账户。
> * 设置两个[逻辑应用](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps)，当设备进入或退出地理围栏区域，将触发应用并向构造站点的运营管理员发送邮件通知。
> * 使用 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)订阅 Azure Maps 地理围栏进入和退出事件。 我们将设置两个 Webhook 事件订阅，用于调用两个逻辑应用中定义的 HTTP 终结点。 然后，逻辑应用将发送有关设备移出或移入地理围栏的相应邮件通知。
> * 使用[搜索地理围栏 GET API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) 在有设备退出和进入地理围栏区域时接收通知。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="upload-geofencing-geojson-data"></a>上传地理围栏 GeoJSON 数据

在本教程中，我们将上传包含 `FeatureCollection` 的地理围栏 GeoJSON 数据。 `FeatureCollection` 包含用于定义站点内多边形区域的两个地理围栏。 第一个地理围栏不设过期时间或时间限制。 第二地理围栏只能在营业时间（太平洋标准时间下午 9-5 点）接受查询， 并且在 2022 年 1 月 1 日后将失效。 有关 GeoJSON 数据格式的详细信息，请参阅[为 GeoJSON 数据设置地理围栏](geofence-geojson.md)。

>[!TIP]
>可以随时更新地理围栏数据。 有关如何更新数据的详细信息，请参阅[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)

1. 打开 Postman 应用。 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“集合”。  命名集合，然后选择“创建”按钮。

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

3. 在生成器选项卡上选择“POST”HTTP 方法并输入以下 URL 将地理围栏数据上传到 Azure Maps 服务。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL 路径中的“geojson”参数表示正在上传的数据的数据格式。

4. 单击“正文”选项卡。选择“原始”，然后选择“JSON”作为输入格式 。 将以下 GeoJSON 数据复制并粘贴到“正文”文本区域：

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
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
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

5. 单击蓝色的“发送”按钮，然后等待请求处理完成。 在请求处理完成后，立即转到响应的“头”选项卡。 复制“位置”键的值，即 `status URL`。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. 检查 API 调用的状态，在 `status URL` 上创建“GET”HTTP 请求。 为了进行身份验证，需要将主订阅密钥追加到 URL 中。 “GET”请求应如以下 URL 所示：

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. “GET”HTTP 请求成功完成后，它将返回 `resourceLocation`。 `resourceLocation` 包含更新内容的唯一 `udid`。 需要保存此 `udid`，在本教程的最后一部分中需要用它查询 Get 地理围栏 API。 （可选）可以使用 `resourceLocation` URL 在下一步中从此资源中检索元数据。

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. 要检索内容元数据，请在第 7 步中检索到的 `resourceLocation` URL 上创建“GET”HTTP 请求。 为了进行身份验证，请确保将主订阅密钥追加到 URL 中。 “GET”请求应如以下 URL 所示：

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. “GET”HTTP 请求成功完成后，响应正文将包含第 7 步的 `resourceLocation` 中指定的 `udid`、以后的内容访问/下载位置，以及有关创建/更新日期、大小等内容的其他元数据。 整体响应的示例如下：

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>创建逻辑应用工作流

本部分中将创建两个用于触发电子邮件通知的[逻辑应用](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps)终结点。 我们将演示如何创建第一个触发器，该触发器会在其终结点被调用时发送电子邮件通知。

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 在 [Azure 门户](https://portal.azure.com)的左上角单击“创建资源”。

3. 在“在市场中搜索”框中键入“逻辑应用”。

4. 从“结果”中选择“逻辑应用”.。 单击“创建”按钮。

5. 在“逻辑应用”页上，输入以下值：
    * 要用于此逻辑应用的“订阅”。
    * 此逻辑应用的“资源组”名称。 可以选择新建或使用现有的资源组。 
    * 你的逻辑应用的“逻辑应用名称”。 在这种情况下，我们将使用 `Equipment-Enter` 作为名称。

    考虑到本教程的目的，请将其余值保留为其默认设置。

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="创建逻辑应用":::

6. 单击“查看 + 创建”按钮。 检查设置并单击“创建”以提交部署。 部署成功完成后，单击“转到资源”。 随即转到“逻辑应用设计器”

7. 现在，选择一个触发器类型。 向下滚动一点到“从常见触发器开始”部分。 单击“收到 HTTP 请求时”。

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="创建逻辑应用 HTTP 触发器":::

8. 单击设计器右上角的“保存”。 随即自动生成“HTTP POST URL”。 保存 URL，因为下一部分中需要用它来创建事件终结点。

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="逻辑应用 HTTP 请求 URL 和 JSON":::

9. 选择“+ 新步骤”。 现在选择一项操作。 在搜索框中键入“`outlook.com email`”。 在“操作”列表中，向下滚动并单击“发送电子邮件(V2)” 。
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="创建逻辑应用设计器":::

10. 登录到 Outlook.com 帐户。 确保单击“是”以允许逻辑应用访问该帐户。 填充用于发送电子邮件的字段。

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="创建逻辑应用发送电子邮件步骤":::

    >[!TIP]
    > 可以通过将逻辑应用配置为读取事件网格发送的数据，在电子邮件通知中检索 GeoJSON 响应数据（如 `geometryId` 或 `deviceId`）。 有关如何配置逻辑应用以使用事件数据并将其传递到电子邮件通知的详细信息，请参阅[教程：使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)。

11. 单击“逻辑应用设计器”左上角的“保存”。

12. 重复第 3-11 步以创建第二个逻辑应用，以便在设备退出构造站点时通知管理员。 将逻辑应用命名为 `Equipment-Exit`。

## <a name="create-azure-maps-events-subscriptions"></a>创建 Azure Maps 事件订阅

Azure Maps 支持三种事件类型。 可在[此处](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)查看 Azure Maps 支持的事件类型。  需要创建两个不同的事件订阅：一个用于地理围栏进入事件，另一个用于地理围栏退出事件。

遵循以下步骤针对地域隔离区进入事件创建事件订阅。 可按类似的方式，重复这些步骤来订阅地理围栏退出事件。

1. 导航到 Azure Maps 帐户。 在仪表板中，选择“订阅”。 单击订阅名称，然后在设置菜单中命名并选择“事件”。

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="导航到 Azure Maps 帐户事件":::

2. 要创建事件订阅，请从“事件”页中选择“+ 事件订阅”。

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="创建 Azure Maps 事件订阅":::

3. 在“创建事件订阅”页中输入以下值：
    * 事件订阅的“名称”。
    * “事件架构”应为“事件网格架构” 。
    * 此事件订阅的“系统主题名称”。 在此案例中，使用 `Contoso-Construction`。
    * 在“事件类型筛选器”上选择 `Geofence Entered` 作为事件类型。
    * 对于“终结点类型”，选择 `Web Hook`。
    * 对于“终结点”，复制在上一个部分创建的“逻辑应用进入”终结点的 HTTP POST URL。 如果忘记保存，可以直接返回到逻辑应用设计器并从 HTTP 触发器步骤复制它。

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps 事件订阅详细信息":::

4. 单击“创建”。

5. 对上一个部分中创建的逻辑应用退出终结点重复第 1-4 步。 在步骤 3 中，请确保选择 `Geofence Exited` 作为事件类型。

## <a name="use-spatial-geofence-get-api"></a>使用空间地理围栏获取 API

现在，我们将使用[空间地理围栏获取 API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) 在有设备进入或退出地理围栏时向运营管理员发送电子邮件通知。

每个设备都有一个 `deviceId`。 在本教程中，我们将跟踪单个设备，其唯一 ID 是 `device_1`。

为清楚起见，下图显示一段时间内设备的五个位置，并从位于地理围栏外的“启动”位置开始。 考虑到本教程的目的，不定义“启动”位置，因为我们不会在该位置查询设备。

当使用某个设备位置（指示最初进入或退出地理围栏的位置）查询[空间地理围栏获取 API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) 时，事件网格将调用相应的逻辑应用终结点，并向运营管理员发送电子邮件通知。

以下每个部分都使用设备的 5 个不同位置坐标发出 HTTP GET 地理围栏 API 请求。

![Azure Maps 中的地理围栏图](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>设备位置 1 (47.638237,-122.132483)

1. 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“请求”。  在“请求名称”中，输入请求名称。 我们使用名称“位置 1”。 选择在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中创建的集合，然后选择“保存”。

2. 在“生成器”选项卡中选择“GET”HTTP 方法，然后输入以下 URL。确保将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. 单击“发送”按钮。 随即响应窗口出现以下 GeoJSON。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. 在上述 GeoJSON 响应中，与主站点地理围栏的距离为负表示设备在地理围栏内。 与子场地地理围栏的距离为正表示设备在子场地地理围栏外。 由于这是第一次在主站点地理围栏内找到此设备，因此将 `isEventPublished` 参数设置为 `true`，运营管理员会收到设备已进入地理围栏的电子邮件通知。

### <a name="location-2-4763800-122132531"></a>位置 2 (47.63800,-122.132531)

1. 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“请求”。  在“请求名称”中，输入请求名称。 我们使用名称“位置 2”。 选择在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中创建的集合，然后选择“保存”。

2. 在“生成器”选项卡中选择“GET”HTTP 方法，然后输入以下 URL。确保将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. 单击“发送”按钮。 随即响应窗口中出现以下 GeoJSON：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. 在上述 GeoJSON 响应中，设备留在了主站点地理围栏中，且未进入子站点地理围栏。 因此，将 `isEventPublished` 参数设置为 `false`，运营管理员不会收到任何电子邮件通知。

### <a name="location-3-4763810783315048-12213336020708084"></a>位置 3 (47.63810783315048,-122.13336020708084)

1. 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“请求”。  在“请求名称”中，输入请求名称。 我们使用名称“位置 3”。 选择在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中创建的集合，然后选择“保存”。

2. 在“生成器”选项卡中选择“GET”HTTP 方法，然后输入以下 URL。确保将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`。

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. 单击“发送”按钮。 随即响应窗口中出现以下 GeoJSON：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. 在上述 GeoJSON 响应中，设备仍位于主站点地理围栏内，但已进子站点地理围栏。 因此，将 `isEventPublished` 参数设置为 `true`，运营管理员将收到指示设备已进入地理围栏的电子邮件通知。

    >[!NOTE]
    >如果设备在营业时间后移入子站点，则不会发布任何事件，运营管理员不会收到任何通知。  

### <a name="location-4-47637988-1221338344"></a>位置 4 (47.637988,-122.1338344)

1. 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“请求”。  在“请求名称”中，输入请求名称。 我们使用名称“位置 4”。 选择在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中创建的集合，然后选择“保存”。

2. 在“生成器”选项卡中选择“GET”HTTP 方法，然后输入以下 URL。确保将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. 单击“发送”按钮。 随即响应窗口中出现以下 GeoJSON：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. 在上述 GeoJSON 响应中，设备仍留在主站点地理围栏中，但已退出子站点地理围栏。 但是，如果留意，会发现 `userTime` 值在 `expiredTime` 之后（如地理围栏数据中所定义）。 因此，将 `isEventPublished` 参数设置为 `false`，运营管理员不会收到任何电子邮件通知。

### <a name="location-547637988-1221338344"></a>位置 5(47.637988,-122.1338344)

1. 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“请求”。  在“请求名称”中，输入请求名称。 我们使用名称“位置 4”。 选择在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中创建的集合，然后选择“保存”。

2. 在“生成器”选项卡中选择“GET”HTTP 方法，然后输入以下 URL。确保将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. 单击“发送”按钮。 随即响应窗口中出现以下 GeoJSON：

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. 在上述 GeoJSON 响应中，设备已退出主站点地理围栏。 因此，将 `isEventPublished` 参数设置为 `true`，运营管理员将收到指示设备已退出地理围栏的电子邮件通知。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 逻辑应用中处理内容类型](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [使用事件网格和逻辑应用发送电子邮件通知](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [事件网格中支持的事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。
