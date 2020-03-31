---
title: 事件处理程序和目标 - Azure 事件网格 IoT 边缘 |微软文档
description: 边缘事件网格中的事件处理程序和目标
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849742"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>边缘事件网格中的事件处理程序和目标

事件处理程序是事件进行进一步操作或处理事件的位置。 使用"边缘事件网格"模块时，事件处理程序可以位于同一边缘设备、另一个设备或云中。 可以使用任何 WebHook 来处理事件，也可以将事件发送到 Azure 事件网格等本机处理程序之一。

本文提供有关如何配置每个产品的信息。

## <a name="webhook"></a>网络胡克

要发布到 WebHook 终结点，请设置为`endpointType``WebHook`和 提供：

* 终结点 URL：WebHook 终结点 URL

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

要发布到 Azure 事件网格云终结点，请设置为`endpointType``eventGrid`并提供：

* 终结点Url：云中的事件网格主题 URL
* sasKey：事件网格主题的 SAS 键
* 主题名称：名称以将所有传出事件标记到事件网格。 主题名称在发布到事件网格域主题时很有用。

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

## <a name="iot-edge-hub"></a>IoT 边缘中心

要发布到边缘中心模块，请设置为`endpointType`和`edgeHub`提供：

* 输出名称：事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与以下订阅匹配的事件将写入 /消息/模块/事件网格模块/输出/示例Sub4。

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

要发布到事件中心，请设置为`endpointType`并提供： `eventHub`

* 连接字符串：通过共享访问策略生成的特定事件中心的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 可以通过导航到要在 Azure 门户中发布到的特定事件中心，然后单击**共享访问策略**来生成新的实体特定连接字符串，从而生成实体特定的连接字符串。

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

要发布到服务总线队列，请设置为`endpointType`和`serviceBusQueue`提供：

* 连接字符串：通过共享访问策略生成的特定服务总线队列的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线队列，然后单击**共享访问策略**以生成新的实体特定连接字符串，生成实体特定的连接字符串。

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

要发布到服务总线主题，请设置为`endpointType`和`serviceBusTopic`提供：

* 连接字符串：通过共享访问策略生成的特定服务总线主题的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线主题，然后单击**共享访问策略**以生成新的实体特定连接字符串，生成实体特定的连接字符串。

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

要发布到存储队列，请设置为`endpointType`和`storageQueue`提供：

* 队列名称：要发布到的存储队列的名称。
* 连接字符串：存储队列中的存储帐户的连接字符串。

    >[!NOTE]
    > 取消行线事件中心、服务总线队列和服务总线主题，用于存储队列的连接字符串不是特定于实体的。 相反，它必须，但存储帐户的连接字符串。

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