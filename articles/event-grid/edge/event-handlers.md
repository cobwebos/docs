---
title: 事件处理程序和目标-Azure 事件网格 IoT Edge |Microsoft Docs
description: 事件处理程序和边缘上事件网格中的目标
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171578"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>事件处理程序和边缘上事件网格中的目标

事件处理程序是事件用于进一步操作或用于处理事件的位置。 在 Edge 模块上的事件网格中，事件处理程序可以在同一边缘设备上，也可以位于其他设备上，也可以在云中。 你可以使用任何 WebHook 来处理事件，或者将事件发送到某个本机处理程序，如 Azure 事件网格。

本文提供了有关如何配置每个的信息。

## <a name="webhook"></a>WebHook

若要发布到 WebHook 终结点，请将设置 `endpointType` 为 `WebHook` 并提供：

* endpointUrl： WebHook 终结点 URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure 事件网格

若要发布到 Azure 事件网格云终结点，请将设置 `endpointType` 为 `eventGrid` 并提供：

* endpointUrl：云中的事件网格主题 URL
* sasKey：事件网格主题的 SAS 密钥
* topicName：为事件网格标记所有传出事件的名称。 向事件网格域发送主题时，主题名称很有用。

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

## <a name="iot-edge-hub"></a>IoT Edge 中心

若要发布到 Edge 中心模块，请将设置 `endpointType` 为 `edgeHub` 并提供：

* outputName：事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与以下订阅匹配的事件将写入/messages/modules/eventgridmodule/outputs/sampleSub4。

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

## <a name="event-hubs"></a>事件中心

若要发布到事件中心，请将设置 `endpointType` 为 `eventHub` 并提供：

* connectionString：目标为通过共享访问策略生成的特定事件中心的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 可以通过导航到要在 Azure 门户中发布到的特定事件中心，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，来生成特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>服务总线队列

若要发布到服务总线队列，请将设置 `endpointType` 为 `serviceBusQueue` 并提供：

* connectionString：目标为通过共享访问策略生成的特定服务总线队列的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线队列，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，生成特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>服务总线主题

若要发布到服务总线主题，请将设置 `endpointType` 为 `serviceBusTopic` 并提供：

* connectionString：目标为通过共享访问策略生成的特定服务总线主题的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线主题，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，生成特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>存储队列

若要发布到存储队列，请将设置  `endpointType` 为 `storageQueue` 并提供：

* queueName：要发布到的存储队列的名称。
* connectionString：存储队列所在的存储帐户的连接字符串。

    >[!NOTE]
    > Unline 事件中心、服务总线队列和服务总线主题，用于存储队列的连接字符串不特定于实体。 它必须是存储帐户的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```