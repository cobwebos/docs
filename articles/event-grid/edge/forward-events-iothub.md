---
title: 将事件网格事件转发到 IoTHub-Azure 事件网格 IoT Edge |Microsoft Docs
description: 将事件网格事件转发到 IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 772fccc96f757c6165238e4041ce98c17c647880
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992296"
---
# <a name="tutorial-forward-events-to-iothub"></a>教程：将事件转发到 IoTHub

本文逐步讲解使用路由将事件网格事件转发到其他 IoT Edge 模块 IoTHub 所需的所有步骤。 出于以下原因，你可能需要执行此操作：

* 继续使用 edgeHub 的路由已有的现有投资
* 仅需通过 IoT 中心路由设备中的所有事件

若要完成本教程，您需要了解以下概念：

- [事件网格概念](concepts.md)
- [IoT Edge 中心](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>必备组件 
为了完成本教程，你将需要：

* **Azure 订阅**-如果你还没有帐户，请创建一个[免费帐户](https://azure.microsoft.com/free)。 
* **Azure IoT 中心和 IoT Edge 设备**-按照[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤进行操作（如果尚未安装）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>创建主题

作为事件的发布者，需要创建事件网格主题。 本主题引用发布服务器可以将事件发送到的终结点。

1. 创建具有以下内容的 topic4。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 运行以下命令以创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 运行以下命令以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>创建事件订阅

订户可以注册发布到主题的事件。 若要接收任何事件，需要在相关主题上创建事件网格订阅。

1. 创建具有以下内容的 subscription4。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType` 指定订阅服务器 `edgeHub`。 `outputName` 指定事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与上述订阅匹配的事件将写入 `/messages/modules/eventgridmodule/outputs/sampleSub4`。
2. 运行以下命令以创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>设置边缘中心路由

更新 edge 中心的路由，以将转发的事件转发到 IoTHub，如下所示：

1. 登录到 [Azure 门户](https://ms.portal.azure.com)
1. 导航到**IoT 中心**。
1. 从菜单中选择**IoT Edge**
1. 从设备列表中选择目标设备的 ID。
1. 选择“设置模块”。
1. 选择 "**下一步**" 和 "路由" 部分。
1. 在路由中，添加新的路由

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  例如，

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 上述路由将转发与此订阅匹配的任何事件，以便转发到 IoT 中心。 您可以使用[Edge 中心路由](../../iot-edge/module-composition.md)功能进一步筛选和将事件网格事件路由到其他 IoT Edge 模块。

## <a name="setup-iot-hub-route"></a>设置 IoT 中心路由

请参阅 IoT 中心[路由教程](../../iot-hub/tutorial-routing.md)，从 iot 中心设置路由，以便查看从事件网格模块转发的事件。

## <a name="publish-an-event"></a>发布事件

1. 创建具有以下内容的 event4。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. 运行以下命令以发布事件：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传递

请参阅 IoT 中心[路由教程](../../iot-hub/tutorial-routing.md)，了解查看事件的步骤。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令，在边缘删除主题及其所有订阅：

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 删除在云中设置 IoTHub 路由时创建的所有资源。

## <a name="next-steps"></a>后续步骤

在本教程中，您创建了一个事件网格主题、边缘中心订阅和已发布的事件。 现在，你已了解转发到边缘集线器的基本步骤，请参阅以下文章：

* 使用[边缘中心](../../iot-edge/module-composition.md)路由筛选器来分区事件
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上设置事件网格模块的持久性
* 按照[文档](configure-client-auth.md)配置客户端身份验证
* 遵循本[教程](forward-events-event-grid-cloud.md)将事件转发到云中的 Azure 事件网格
