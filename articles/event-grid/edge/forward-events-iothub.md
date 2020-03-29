---
title: 将事件网格事件转发到 IoTHub - Azure 事件网格 IoT 边缘 |微软文档
description: 将事件网格事件转发到 IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844694"
---
# <a name="tutorial-forward-events-to-iothub"></a>教程：将事件转发到 IoTHub

本文介绍将事件网格事件转发到其他 IoT Edge 模块（使用路由的 IoTHub）所需的所有步骤。 您可能需要这样做的原因如下：

* 继续使用任何现有投资，并采用 EdgeHub 的路由
* 更喜欢仅通过 IoT 中心从设备路由所有事件

要完成本教程，您需要了解以下概念：

- [事件网格概念](concepts.md)
- [IoT Edge 中心](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* **Azure 订阅**- 如果尚未创建[免费帐户](https://azure.microsoft.com/free)，则创建免费帐户。 
* **Azure IoT 中心和 IoT 边缘设备**- 如果尚未启动[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)，请按照快速入门中的步骤操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>创建主题

作为事件的发布者，您需要创建事件网格主题。 该主题是指发布者随后可以将事件发送到的终点。

1. 使用以下内容创建主题4.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 运行以下命令以创建主题。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 运行以下命令以验证主题已成功创建。 应返回 200 OK 的 HTTP 状态代码。

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

订阅者可以注册发布到主题的事件。 要接收任何事件，他们需要根据感兴趣的主题创建事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下内容创建订阅4.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

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
   > 指定`endpointType`订阅者为`edgeHub`。 指定`outputName`事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与上述订阅匹配的事件将写入`/messages/modules/eventgridmodule/outputs/sampleSub4`。
2. 运行以下命令以创建订阅。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证已成功创建订阅。 应返回 200 OK 的 HTTP 状态代码。

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

更新边缘中心的路线，将事件订阅的事件转发到 IoTHub，如下所示：

1. 登录到 [Azure 门户](https://ms.portal.azure.com)
1. 导航到**IoT 中心**。
1. 从菜单中选择**IoT 边缘**
1. 从设备列表中选择目标设备的 ID。
1. 选择**设置模块**。
1. 选择 **"下一步**"和"路径"部分。
1. 在路由中，添加新路由

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
   > 上述路由将转发为此订阅匹配的任何事件，以转发到 IoT 中心。 您可以使用[边缘中心路由](../../iot-edge/module-composition.md)功能进一步筛选，并将事件网格事件路由到其他 IoT Edge 模块。

## <a name="setup-iot-hub-route"></a>设置 IoT 中心路由

请参阅[IoT 中心路由教程](../../iot-hub/tutorial-routing.md)，以设置来自 IoT 中心的路线，以便您可以查看从事件网格模块转发的事件。 用于`true`查询以保持教程简单。  



## <a name="publish-an-event"></a>发布事件

1. 使用以下内容创建事件4.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

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

有关查看事件的步骤，请参阅 IoT 中心[路由教程](../../iot-hub/tutorial-routing.md)。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其边缘的所有订阅：

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 删除在云中设置 IoTHub 路由时创建的任何资源。

## <a name="next-steps"></a>后续步骤

在本教程中，您创建了事件网格主题、边缘中心订阅和已发布的事件。 现在，您已经了解了转发到边缘中心的基本步骤，请参阅以下文章：

* 要解决在 IoT 边缘上使用 Azure 事件网格的问题，请参阅[故障排除指南](troubleshoot.md)。
* 使用[边缘中心](../../iot-edge/module-composition.md)路由筛选器对事件进行分区
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上设置事件网格模块的持久性
* 按照[文档](configure-client-auth.md)配置客户端身份验证
* 按照[本教程](forward-events-event-grid-cloud.md)将事件转发到云中的 Azure 事件网格
* [监视边缘的主题和订阅](monitor-topics-subscriptions.md)