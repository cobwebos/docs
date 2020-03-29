---
title: 将边缘事件转发到事件网格云 - Azure 事件网格 IoT 边缘 |微软文档
description: 将边缘事件转发到事件网格云
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844711"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>教程：将事件转发到事件网格云

本文介绍将边缘事件转发到 Azure 云中的事件网格所需的所有步骤。 您可能需要这样做的原因如下：

* 响应云中的边缘事件。
* 将事件转发到云中的事件网格，并使用 Azure 事件中心或 Azure 存储队列在云中处理事件之前缓冲事件。

 要完成本教程，您需要了解[边缘](concepts.md)和[Azure](../concepts.md)上的事件网格概念。 有关其他目标类型，请参阅[事件处理程序](event-handlers.md)。 

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* **Azure 订阅**- 如果尚未创建[免费帐户](https://azure.microsoft.com/free)，则创建免费帐户。 
* **Azure IoT 中心和 IoT 边缘设备**- 如果尚未启动[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)，请按照快速入门中的步骤操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>在云中创建事件网格主题和订阅

通过按照[本教程](../custom-event-quickstart-portal.md)创建事件网格主题和在云中的订阅。 记下`topicURL`，`sasKey`以及`topicName`本教程稍后将使用的新创建主题。

例如，如果您创建了在美国西部命名的`testegcloudtopic`主题，则这些值将如下所示：

* **主题Url**：`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **主题名称**：`testegcloudtopic`
* **SasKey**： 可在主题**的访问键**下提供。 使用**键1**。

## <a name="create-event-grid-topic-at-the-edge"></a>在边缘创建事件网格主题

1. 使用以下内容创建主题3.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 运行以下命令以创建主题。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 运行以下命令以验证主题已成功创建。 应返回 200 OK 的 HTTP 状态代码。

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

1. 使用以下内容创建订阅3.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

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
   > **终结点 Url**指定云中的事件网格主题 URL。 **sasKey**是指事件网格云主题的键。 **主题名称**中的值将用于将所有传出事件标记到事件网格。 这在发布到事件网格域主题时非常有用。 有关事件网格域主题的详细信息，请参阅[事件域](../event-domains.md)

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

2. 运行以下命令以创建订阅。 应返回 200 OK 的 HTTP 状态代码。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 运行以下命令以验证已成功创建订阅。 应返回 200 OK 的 HTTP 状态代码。

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

1. 使用以下内容创建 event3.json。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

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

## <a name="verify-edge-event-in-cloud"></a>在云中验证边缘事件

有关查看云主题传递的事件的信息，请参阅[教程](../custom-event-quickstart-portal.md)。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 删除在云（Azure 事件网格）中创建的主题和订阅。

## <a name="next-steps"></a>后续步骤

在本教程中，您将在边缘上发布一个事件，并转发到 Azure 云中的事件网格。 现在您已经了解了在云中转发到事件网格的基本步骤：

* 要解决在 IoT 边缘上使用 Azure 事件网格的问题，请参阅[故障排除指南](troubleshoot.md)。
* 按照[本教程](forward-events-iothub.md)将事件转发到 IoTHub
* 按照[本教程](pub-sub-events-webhook-cloud.md)将事件转发到云中的 Webhook
* [监视边缘的主题和订阅](monitor-topics-subscriptions.md)
