---
title: 将边缘事件转发到事件网格云-Azure 事件网格 IoT Edge |Microsoft Docs
description: 向事件网格云转发边缘事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844711"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>教程：向事件网格云转发事件

本文逐步讲解将边缘事件转发到 Azure 云中的事件网格所需的所有步骤。 出于以下原因，你可能需要执行此操作：

* 响应云中的边缘事件。
* 将事件转发到云中的事件网格，并使用 Azure 事件中心或 Azure 存储队列来缓冲事件，然后在云中处理事件。

 若要完成本教程，你需要了解有关[边缘](concepts.md)和[Azure](../concepts.md)的事件网格概念。 有关其他目标类型，请参阅[事件处理程序](event-handlers.md)。 

## <a name="prerequisites"></a>必备组件 
为了完成本教程，你将需要：

* **Azure 订阅**-如果你还没有帐户，请创建一个[免费帐户](https://azure.microsoft.com/free)。 
* **Azure IoT 中心和 IoT Edge 设备**-按照[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤进行操作（如果尚未安装）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>在云中创建事件网格主题和订阅

按照[本教程](../custom-event-quickstart-portal.md)的说明，在云中创建事件网格主题和订阅。 记下新创建主题的 `topicURL`、`sasKey`和 `topicName`，稍后将在本教程中使用该主题。

例如，如果在美国西部创建了名为 `testegcloudtopic` 的主题，则这些值将如下所示：

* **TopicUrl**： `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**： `testegcloudtopic`
* **SasKey**：在主题的**AccessKey**下提供。 使用**key1**。

## <a name="create-event-grid-topic-at-the-edge"></a>在边缘创建事件网格主题

1. 创建具有以下内容的 topic3。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 运行以下命令以创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 运行以下命令以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>在边缘创建事件网格订阅

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 创建具有以下内容的 subscription3。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **EndpointUrl**指定云中的事件网格主题 URL。 **SasKey**是指事件网格云主题的键。 **TopicName**中的值将用于将所有传出事件标记到事件网格。 在发布到事件网格域主题时，这可能很有用。 有关事件网格域主题的详细信息，请参阅[事件域](../event-domains.md)

    例如，
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. 运行以下命令以创建订阅。 应返回 HTTP 状态代码 200 OK。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    示例输出：

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>在边缘发布事件

1. 创建具有以下内容的 event3。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. 运行以下命令：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>验证云中的边缘事件

有关查看由云提供的事件的信息，请参阅[教程](../custom-event-quickstart-portal.md)。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 删除在云（Azure 事件网格）中创建的主题和订阅。

## <a name="next-steps"></a>后续步骤

在本教程中，已在边缘上发布事件，并将其转发到 Azure 云中的事件网格。 现在，你已经了解了要前进到云中的事件网格的基本步骤：

* 若要解决在 IoT Edge 上使用 Azure 事件网格时遇到的问题，请参阅[故障排除指南](troubleshoot.md)。
* 遵循本[教程](forward-events-iothub.md)将事件转发到 IoTHub
* 遵循本[教程](pub-sub-events-webhook-cloud.md)将事件转发到云中的 Webhook
* [监视边缘上的主题和订阅](monitor-topics-subscriptions.md)
