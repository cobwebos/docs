---
title: 使用 Creator 创建室内定位
description: 使用 Azure Maps Creator 创建室内定位。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4d150135e15fb167a9c2d56c74e7bc4fc91c0953
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745925"
---
# <a name="use-creator-to-create-indoor-maps"></a>使用 Creator 创建室内定位

本教程介绍了如何创建室内定位。 本教程将介绍如何使用 API 完成以下操作：

> [!div class="checklist"]
> * 上传室内定位绘图包
> * 将绘图包转换为定位数据
> * 通过定位数据创建数据集
> * 通过数据集中的数据创建图块集
> * 通过查询 Azure Maps Web 特征服务 (WFS) API 来了解定位特征
> * 通过使用定位特征和数据集中的数据来创建特征状态集
> * 更新特征状态集

## <a name="prerequisites"></a>先决条件

若要创建室内定位，请执行以下操作：

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. [创建 Creator 资源](how-to-manage-creator.md)
4. 下载[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="upload-a-drawing-package"></a>上传绘图包

使用[数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) 将绘图包上传到 Azure Maps 资源。

数据上传 API 是一项长期事务，用于实现此处定义的模式。 在此操作完成后，我们就会使用 `udid` 访问已上传包来转换它。 若要获取 `udid`，请按照下面的步骤操作。

1. 打开 Postman 应用。 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“集合”。  命名集合，然后选择“创建”按钮。

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

3. 在生成器选项卡中选择“POST”HTTP 方法，然后输入下面的 URL，以将绘图包上传到 Azure Maps 服务。 对于此请求和本文中提到的其他请求，请将 `<Azure-Maps-Primary-Subscription-key>` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 在“头”选项卡中，指定“`Content-Type`”键的值。 由于绘图包是压缩文件夹，因此使用的是 `application/octet-stream` 值。 在“正文”选项卡中，选择“二进制文件”。 单击“选择文件”，然后选择绘图包。

     ![数据管理](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. 单击蓝色的“发送”按钮，然后等待请求处理完成。 在请求处理完成后，立即转到响应的“头”选项卡。 复制“位置”键的值，即 `status URL`。

6. 若要检查 API 调用的状态，请在 `status URL` 处创建 GET HTTP 请求。 为了进行身份验证，需要将主订阅密钥追加到 URL 中。

    ```http
    https://atlas.microsoft.com/mapData/operations/{operationsId}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. 当 GET HTTP 请求成功完成时，可以在下一步中使用 `resourceLocation` URL 从此资源中检索元数据。

    ```json
    {
        "operationId": "{operationId}",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{upload-udid}?api-version=1.0"
    }
    ```

8. 若要检索内容元数据，请在第 7 步中复制的 `resourceLocation` URL 处创建 GET HTTP 请求。 响应正文包含已上传内容的唯一 `udid`、用于日后访问/下载此内容的位置，以及其他一些内容元数据（如创建/更新日期、大小等）。 整体响应的示例如下：

     ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>转换绘图包

 至此，绘图包已上传，现在我们将使用已上传包的 `udid` 将此包转换为定位数据。 转换 API 使用一项长期事务，用于实现[此处](creator-long-running-operation.md)定义的模式。 在此操作完成后，我们就会使用 `conversionId` 来访问转换后的数据。 若要获取 `conversionId`，请按照下面的步骤操作。

1. 选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“ **保存**”。

2. 在生成器选项卡中选择“POST”HTTP 方法，然后输入下面的 URL，以将已上传绘图包转换为定位数据。 使用已上传包的 `udid`。

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={upload-udid}&inputType=DWG
    ```

3. 单击“发送”按钮，然后等待请求处理完成。 在请求处理完成后，立即转到响应的“头”选项卡，然后查找“位置”键。 复制“位置”键的值，即转换请求的 `status URL`。

4. 在生成器选项卡中，启动新的 GET HTTP 方法。将 Azure Maps 主订阅密钥追加到 `status URL` 中。 在上一步中复制的 `status URL` 处发出 GET 请求。 如果转换过程还没有完成，你可能会看到如下所示的 JSON 响应：

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. 一旦请求成功完成，响应正文中就会显示一条成功状态消息。  从已转换包的 `resourceLocation` URL 中复制 `conversionId`。 其他 API 使用 `conversionId` 来访问转换后的定位数据。

    ```json
   {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman 应用本身并不支持 HTTP 长期请求。 因此，在状态 URL 处发出 GET 请求时，你可能会注意到有长时间的延迟。  等待大约 30 秒，然后尝试重新单击“发送”按钮，直到响应显示成功或失败。

示例绘图包应该在没有错误或警告的情况下进行转换。 不过，如果你收到来自自己的绘图包的错误或警告，JSON 响应会提供[绘图错误可视化工具](drawing-error-visualizer.md)链接。 使用绘图错误可视化工具，可以检查错误和警告的详细信息。 若要获取关于如何解决转换错误和警告的建议，请参阅[绘图转换错误和警告](drawing-conversion-error-codes.md)。

```json
{
    "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>创建数据集

数据集是定位特征（如建筑物、楼层和房间等）的集合。 若要创建数据集，请使用[数据集创建 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)。 数据集创建 API 需要使用已转换绘图包的 `conversionId`，并返回已创建数据集的 `datasetId`。 下面的步骤展示了如何创建数据集。

1. 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“保存”

2. 向[数据集创建 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) 发出 POST 请求，以新建数据集。 提交请求前，请先追加订阅密钥和 `conversionId`（即在第 5 步的转换过程中获取的 `conversionId`）。  请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 获取响应“头”的“位置”键中的 `statusURL`。

4. 在 `statusURL` 处发出 GET 请求，以获取 `datasetId`。 为了进行身份验证，请追加 Azure Maps 主订阅密钥。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/dataset/operations/{operationsId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

5. 当 GET HTTP 请求成功完成时，响应头包含已创建数据集的 `datasetId`。 复制 `datasetId`。 需要使用 `datasetId` 来创建图块集。

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>创建图块集

图块集是一组在定位中渲染的矢量图块。 图块集是通过现有数据集创建的。 不过，图块集独立于它所源自的数据集。 如果数据集被删除，图块集将继续存在。 若要创建图块集，请按照下面的步骤操作：

1. 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“保存”

2. 在生成器选项卡中，发出 POST 请求。请求 URL 应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在图块集的 `statusURL` 处发出 GET 请求。 为了进行身份验证，请追加 Azure Maps 主订阅密钥。 请求应如下面的 URL 所示：

   ```http
    https://atlas.microsoft.com/tileset/operations/{operationsId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

4. 当 GET HTTP 请求成功完成时，响应头包含已创建图块集的 `tilesetId`。 复制 `tilesetId`。

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>使用 WFS API 查询数据集

 可以使用 [WFS API](https://docs.microsoft.com/rest/api/maps/wfs) 查询数据集。 使用 WFS API，可以通过特征 ID 查询特征集合、特定集合或特定特征。 特征 ID 是数据集内特征的唯一标识。 例如，它用于标识应更新给定状态集中的哪个特征状态。

1. 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“保存”

2. 发出 GET 请求，以查看数据集中的集合列表。 将 `<dataset-id>` 替换为你的 `datasetId`。 使用你的 Azure Maps 主密钥来替换占位符。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. 响应正文将以 GeoJSON 格式传递，并包含数据集中的所有集合。 为了简单起见，此部分的示例只显示 `unit` 集合。 若要查看包含所有集合的示例，请参阅 [WFS 描述集合 API](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview)。 若要详细了解任意集合，可以单击 `link` 元素中的任何一个 URL。

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. 发出 GET 请求，以获取 `unit` 特征集合。  将 `{datasetId}` 替换为你的 `datasetId`。 使用你的 Azure Maps 主密钥来替换占位符。 响应正文将包含 `unit` 集合的所有特征。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 复制单元特征的特征 `id`，此特征包含可动态修改的样式属性。  由于单元居用状态和温度可以动态更新，因此我们将在下一部分中使用这一特征 `id`。 在下面的示例中，特征 `id` 是“UNIT26”。 我们将此特征的样式属性称为状态，并使用此特征来生成状态集。

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>创建特征状态集

1. 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“保存”

2. 向[创建状态集 API](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview) 发出 POST 请求。 使用包含要修改的状态的数据集的 `datasetId`。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在 POST 请求的“头”中，将“`Content-Type`”设置为“`application/json`”。 在“正文”中，提供下面的样式，用于反映对 `occupied` 和 `temperature` 状态的更改。 完成后，单击“发送”。

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. 从响应正文中复制 `statesetId`。

5. 创建用于更新状态的 POST 请求：将 statesetId 和特征 `ID` 与你的 Azure Maps 订阅密钥一起传递。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 在 POST 请求的“头”中，将“`Content-Type`”设置为“`application/json`”。 在 POST 请求的“正文”中，复制并粘贴下面示例中的 JSON。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > 只有当 POST 请求的时间戳晚于上一请求的时间戳时，才会保存更新。 可以传递之前在创建过程中配置的任何密钥名。

7. 在成功更新后，你就会收到 `200 OK` HTTP 状态代码。 如果你已为室内定位[实现动态样式](indoor-map-dynamic-styling.md)，更新将会在指定的时间戳显示在渲染的定位中。

使用[特征状态获取 API](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview)，可以通过特征 `ID` 检索特征的状态。 还可以使用[特征状态删除 API](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview) 来删除状态集及其资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 上传室内定位绘图包
> * 将绘图包转换为定位数据
> * 通过定位数据创建数据集
> * 通过数据集中的数据创建图块集
> * 通过查询 Azure Maps WFS 服务来了解定位特征
> * 通过使用定位特征和数据集中的数据来创建特征状态集
> * 更新特征状态集

现在你已经具备了学习后续指南所需要的技能：

> [!div class="nextstepaction"]
> [使用“室内定位”模块](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [实现室内定位的动态样式](indoor-map-dynamic-styling.md)

详细了解本文中讨论的各种 Azure Maps 服务：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [特征状态集](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 服务](creator-indoor-maps.md#web-feature-service-api)
