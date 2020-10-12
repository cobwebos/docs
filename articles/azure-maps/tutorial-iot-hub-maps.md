---
title: 教程：实现 IoT 空间分析 | Microsoft Azure Maps
description: 本教程介绍如何将 IoT 中心与 Microsoft Azure Maps 服务 API 集成
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3eb405783b16d1bb7de27f6638dba394457601c8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321826"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>教程：使用 Azure Maps 实现 IoT 空间分析

在 IoT 场景中，通常需要捕获和跟踪时空中发生的相关事件。 示例包括机群管理、资产跟踪、物流和智能城市应用。 本教程指导你完成使用 Azure Maps API 跟踪出租二手车移动轨迹的解决方案。

在本教程中，将：

> [!div class="checklist"]
> * 创建 Azure 存储帐户用于记录汽车跟踪数据。
> * 使用数据上传 API 将地理围栏上传到 Azure Maps 数据服务。
> * 在 Azure IoT 中心创建中心并注册设备。
> * 在 Azure Functions 中创建一个函数，基于 Azure Maps 空间分析实现业务逻辑。
> * 通过 Azure 事件网格订阅 Azure 函数发出的 IoT 设备遥测事件。
> * 使用 IoT 中心消息路由筛选遥测事件。

## <a name="prerequisites"></a>先决条件

1. 登录 [Azure 门户](https://portal.azure.com)。

2. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。 有关详细信息，请参阅[使用 Azure Maps 管理身份验证](how-to-manage-authentication.md)。

4. [创建资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)。 在本教程中，我们将资源组命名为 ContosoRental，但你可以选择任何喜欢的名称。

5. 下载 [rentalCarSimulation C# 项目](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="use-case-rental-car-tracking"></a>用例：出租汽车跟踪

假设一家汽车出租公司希望记录其出租汽车的位置信息、行驶距离以及运行状态。 该公司还希望在每次汽车离开正确授权地理区域时存储此信息。

出租汽车配备了 IoT 设备，可定期将遥测数据发送到 IoT 中心。 遥测数据包括当前位置，指示汽车引擎是否正在运转。 设备位置架构符合 IoT [地理空间数据的即插即用架构](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)。 出租汽车的设备遥测架构类似以下 JSON 代码：

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

在本教程中，你仅跟踪一辆车。 设置 Azure 服务后，你需要下载 [rentalCarSimulation C# 项目](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)以运行车辆模拟器。 以下步骤总结从事件到函数执行的整个过程：

1. 车载设备将遥测数据发送到 IoT 中心。

2. 如果汽车发动机正在运转，该中心会将遥测数据发布到事件网格。

3. 触发 Azure 函数的原因在于其事件订阅了设备遥测事件。

4. 函数记录车辆设备位置坐标、事件时间和设备 ID。 然后，它使用[空间地理围栏获取 API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) 来确定汽车行驶范围是否已超出地理围栏范围。 如果已超出地理围栏边界，则函数会将从事件接收的位置数据存储到 blob 容器中。 该函数会查询[反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，以将坐标位置转换为街道地址，并将其与其余设备位置数据存储在一起。

下图高度概括了此系统。

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="系统概述图。":::

下图以蓝色突出显示地理围栏区域。 出租汽车的路线以绿线表示。

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="系统概述图。":::

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

若要存储汽车违规跟踪数据，请在资源组中创建[常规用途 v2 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts)。 如果尚未创建资源组，请按照[创建资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)中的指示执行操作。 在本教程中，需要将资源组命名为 ContosoRental。

若要创建存储帐户，请按照[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)中的说明执行操作。 在本教程中，需要将存储帐户命名为 contosorentalstorage，但通常可以将其命名为你喜欢的任何名称。

成功创建存储帐户后，你需要创建一个容器来存储日志记录数据。

1. 转到新创建的存储帐户。 在“概要”部分中，选择“容器”连接 。

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="系统概述图。":::

2. 在左上角，选择“+ 容器”。 浏览器右侧将显示一个面板。 将容器命名为 contoso-rental-logs，然后选择“创建”。

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="系统概述图。":::

3. 转到存储帐户中的“访问密钥”窗格，然后复制“key1”部分中的“存储帐户名称”和“密钥”值   。 你将在“创建 Azure 函数并添加事件网格订阅”部分中用到这两个值。

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="系统概述图。":::

## <a name="upload-a-geofence"></a>上传地理围栏

接下来，使用 [Postman 应用](https://www.getpostman.com)将[地理围栏](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)上传到 Azure Maps。 地理围栏定义出租车辆的授权地理区域。 你将在 Azure 函数中使用此地理围栏来确定汽车是否已行驶至地理围栏区域之外。

按照以下步骤，使用 Azure Maps 数据上传 API 上传地理围栏： 

1. 打开 Postman 应用，然后选择“新建”。 在“新建”窗口中，选择“集合”。 命名集合，然后选择“创建”。

2. 若要创建请求，请再次选择“新建”。 在“创建新项”窗口中，选择“请求”，然后输入请求名称 。 选择在上一步中创建的集合，然后选择“保存”。

3. 在生成器选项卡上选择“POST”HTTP 方法并输入以下 URL，将地理围栏上传到数据上传 API。 确保将 `{subscription-key}` 替换为主要订阅密钥。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    在 URL 路径中，`dataFormat` 参数对应的 `geojson` 值表示正在上传的数据的格式。

4. 为输入格式选择“正文” > “原始”，然后从下拉列表选择“JSON”  。 [打开 JSON 数据文件](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)，然后将 JSON 复制到正文部分。 选择“发送”。

5. 选择“发送”，然后等待请求处理完成。 在请求处理完成后，请转到响应的“标头”选项卡。 复制“位置”键的值，即 `status URL`。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. 检查 API 调用的状态，在 `status URL` 上创建“GET”HTTP 请求。 为了进行身份验证，需要将主订阅密钥追加到 URL 中。 “GET”请求应如以下 URL 所示：

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it returns a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. Copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

IoT 中心使 IoT 应用程序与其管理的设备之间能够安全可靠地进行双向通信。 在本教程中，你希望从车载设备获取信息，以确定出租汽车的位置。 在本部分中，你将在 ContosoRental 资源组内创建一个 IoT 中心。 此中心将负责发布设备遥测事件。

> [!NOTE]
> 在事件网格中发布设备遥测事件的功能当前处于预览阶段。 此功能已在除以下区域之外的所有区域推出：美国东部、美国西部、西欧、Azure 政府、Azure 中国世纪互联和 Azure 德国。

若要在 ContosoRental 资源组中创建 IoT 中心，请按照[创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)中的步骤执行操作。

## <a name="register-a-device-in-your-iot-hub"></a>在 IoT 中心注册设备

除非在 IoT 中心标识注册表中注册了设备，否则它们无法连接到 IoT 中心。 在这里，你将创建名为 InVehicleDevice 的设备。 若要在 IoT 中心创建和注册设备，请按照[在 IoT 中心注册新设备](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub)中的步骤执行操作。 确保复制设备的主连接字符串。 稍后需要用到此信息。

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>创建函数并添加事件网格订阅

Azure Functions 是无服务器计算服务，使用它可以运行一小段代码（“函数”），而无需显式预配或管理计算基础结构。 有关详细信息，请参阅 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)。

函数由特定事件触发。 在本文中，你将创建一个由事件网格触发器触发的函数。 通过为 IoT 中心设备遥测事件创建事件订阅来创建触发器与函数之间的关系。 发生设备遥测事件时，函数作为终结点被调用，并接收之前在 IoT 中心注册的设备的相关数据。

下面是[函数将包含的 C# 脚本代码](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)。

接下来，设置 Azure 函数。

1. 在 Azure 门户仪表板中，选择“创建资源”。 在搜索文本框中键入“函数应用”。 选择“函数应用” > “创建” 。

1. 在“函数应用”创建页上为函数应用命名。**** 在“资源组”下，从下拉列表中选择“ContosoRental”。 选择“.NET Core”作为“运行时堆栈”。 在页面底部，选择“下一页:托管 >”。

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="系统概述图。":::

1. 对于“存储帐户”，选择在[创建 Azure 存储帐户](#create-an-azure-storage-account)中创建的存储帐户。 选择“查看 + 创建”。

1. 查看函数应用详细信息，然后选择“创建”。

1. 创建应用后，请向其添加一个函数。 转到函数应用。 选择“函数”窗格。 在页面顶部，选择“+ 添加”。**** 随即将显示函数模板面板。 向下滚动面板，然后选择“Azure 事件网格触发器”。

     >[!IMPORTANT]
    > “Azure 事件中心触发器”模板和“Azure 事件网格触发器”模板具有相似的名称。 请确保选择“Azure 事件网格触发器”模板。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="系统概述图。":::

1. 为函数指定一个名称。 在本教程中，你将使用 GetGeoFunction 这一名称，但通常你可以使用任何喜欢的名称。 选择“创建函数”。

1. 在左侧菜单中，选择“代码 + 测试”窗格。 将 [C# 脚本](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)复制并粘贴到代码窗口中。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="系统概述图。":::

1. 在 C# 代码中，替换以下参数：
    * 将“SUBSCRIPTION_KEY”替换为 Azure Maps 帐户主要订阅密钥。
    * 将“UDID”替换为在[上传地理围栏](#upload-a-geofence)中上传的地理围栏的 `udid`。
    * 脚本中的 `CreateBlobAsync` 函数将为数据存储帐户中的每个事件创建一个 Blob。 将 **ACCESS_KEY**、**ACCOUNT_NAME** 和 **STORAGE_CONTAINER_NAME** 替换为存储帐户的访问密钥、帐户名称和数据存储容器。 这些值在[创建 Azure 存储帐户](#create-an-azure-storage-account)中创建存储帐户时生成。

1. 在左侧菜单中，选择“集成”窗格。 选择关系图中的“事件网格触发器”。 键入触发器的名称 eventGridEvent，然后单击“创建事件网格订阅”。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="系统概述图。":::

1. 填写订阅详细信息。 为事件订阅指定一个名称。 对于“事件架构”，请选择“事件网格架构” 。 对于“主题类型”，请选择“Azure IoT 中心帐户”。 对于“资源组”，请选择在本教程开始时创建的资源组。 对于“资源”，请选择在“创建 Azure IoT 中心”中创建的 IoT 中心。 对于“筛选事件类型”，请选择“设备遥测”。

   选择这些选项后，你将看到“主题类型”更改为“IoT 中心”。 对于“系统主题名称”，可以使用与资源相同的名称。 最后，在“终结点详细信息”部分中，选择“选择终结点” 。 接受所有设置，然后选择“确认选择”。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="系统概述图。":::

1. 复查你的设置。 确保终结点指定在本部分开始时创建的函数。 选择“创建”。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="系统概述图。":::

1. 现在，你回到了“编辑触发器”面板。 选择“保存”。

## <a name="filter-events-by-using-iot-hub-message-routing"></a>使用 IoT 中心消息路由筛选事件

将事件网格订阅添加到 Azure 函数时，系统会在指定的 IoT 中心自动创建消息路由。 借助消息路由，你能够将不同的数据类型路由到各个终结点。 例如，可以路由设备遥测消息、设备生命周期事件和设备孪生更改事件。 有关详细信息，请参阅[使用 IoT 中心消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="系统概述图。":::

在你的示例场景中，你只希望在出租汽车移动时接收消息。 创建一个路由查询，以筛选 `Engine` 属性等于“ON”的事件。 创建路由查询时，请选择 RouteToEventGrid 路由，将 Routing query 替换为“Engine=‘ON’”  。 再选择“保存”。 现在，IoT 中心只会发布引擎处于打开状态时的设备遥测数据。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="系统概述图。":::

>[!TIP]
>可以通过多种方法查询 IoT 设备到云的消息。 若要详细了解消息路由语法，请参阅 [IoT 中心消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。

## <a name="send-telemetry-data-to-iot-hub"></a>将遥测数据发送到 IoT 中心

当 Azure 函数正在运行时，你现在可将遥测数据发送到 IoT 中心，而 IoT 中心会将其路由到事件网格。 使用一个 C# 应用程序来模拟出租汽车的车内设备的定位数据。 若要运行该应用程序，需要在开发计算机上安装 .NET Core SDK 2.1.0 或更高版本。 按照以下步骤将模拟的遥测数据发送到 IoT 中心：

1. 如果尚未这样做，请下载 [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 项目。

2. 在所选的文本编辑器中打开 `simulatedCar.cs` 文件，将 `connectionString` 的值替换为在注册设备时保存的值。 保存对文件的更改。

3. 确保已在计算机上安装 .NET Core。 在本地终端窗口中，转到 C# 项目的根文件夹，运行以下命令以安装模拟设备应用程序所需的包：

    ```cmd/sh
    dotnet restore
    ```

4. 在同一终端中运行以下命令，以生成并运行出租汽车模拟应用程序：

    ```cmd/sh
    dotnet run
    ```


  本地终端应如下所示。

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="系统概述图。":::

如果现在打开 blob 存储容器，则可以看到四个 blob，它们对应于车辆位于地理围栏以外时所处的位置。

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="系统概述图。":::

以下地图显示地理围栏之外的四个车辆位置点。 每个位置均按固定时间间隔进行记录。

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="系统概述图。":::

## <a name="explore-azure-maps-and-iot"></a>探索 Azure Maps 和 IoT

若要了解本教程中使用的 Azure Maps API，请参阅：

* [获取反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [获取地理围栏](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

有关 Azure Maps REST API 的完整列表，请参阅：

* [Azure Maps REST API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

* [IoT 即插即用](https://docs.microsoft.com/azure/iot-pnp)

若要获取 Azure 认证的 IoT 设备列表，请访问：

* [Azure 认证的设备](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>后续步骤

若要详细了解如何将设备发送到云遥测以及执行反向发送，请参阅：


> [!div class="nextstepaction"]
> [从设备发送遥测数据](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)