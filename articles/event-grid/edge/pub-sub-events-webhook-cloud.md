---
title: 发布、订阅云中的事件 - Azure 事件网格 IoT 边缘 |微软文档
description: 使用 Webhook 在 IoT 边缘使用事件网格发布、订阅云中的事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844577"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>教程：发布、订阅云中的事件

本文介绍使用 IoT Edge 上的事件网格发布和订阅事件所需的所有步骤。 本教程使用 Azure 函数作为事件处理程序。 有关其他目标类型，请参阅[事件处理程序](event-handlers.md)。

请参阅[事件网格概念](concepts.md)，以了解事件网格主题和订阅是什么，然后再继续。

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* **Azure 订阅**- 如果尚未创建[免费帐户](https://azure.microsoft.com/free)，则创建免费帐户。 
* **Azure IoT 中心和 IoT 边缘设备**- 如果尚未启动[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)，请按照快速入门中的步骤操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>在 Azure 门户中创建 Azure 函数

按照[本教程](../../azure-functions/functions-create-first-azure-function.md)中概述的步骤创建 Azure 函数。 

将代码段替换为以下代码：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

在新功能中，选择右上角**的获取函数 URL，** 选择默认（**函数键**），然后选择 **"复制**"。 您将在本教程的后面部分使用函数 URL 值。

> [!NOTE]
> 有关对事件网格事件触发器的事件做出反应的更多示例和教程，请参阅[Azure 函数](../../azure-functions/functions-overview.md)文档。

## <a name="create-a-topic"></a>创建主题

作为事件的发布者，您需要创建事件网格主题。 主题是指发布者可以向其发送事件的结束点。

1. 使用以下内容创建主题2.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 运行以下命令以创建主题。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 运行以下命令以验证主题已成功创建。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>创建事件订阅

订阅者可以注册发布到主题的事件。 要接收任何事件，订阅者将需要创建感兴趣的主题的事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下内容创建订阅2.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **终结点类型**指定订阅者是 Webhook。  **终结点 Url**指定订阅者侦听事件的 URL。 此 URL 对应于之前设置的 Azure 函数示例。
2. 运行以下命令以创建订阅。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证已成功创建订阅。 应返回 200 OK 的 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>发布事件

1. 使用以下内容创建 event2.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. 运行以下命令以发布事件

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传递

您可以在函数的 **"监视器"** 选项下查看在 Azure 门户中传递的事件。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* 删除在 Azure 门户中创建的 Azure 函数。

## <a name="next-steps"></a>后续步骤

在本教程中，您创建了事件网格主题、订阅和已发布的事件。 现在您已经了解了基本步骤，请参阅以下文章：

* 要解决在 IoT 边缘上使用 Azure 事件网格的问题，请参阅[故障排除指南](troubleshoot.md)。
* 使用筛选器创建/更新[订阅](advanced-filtering.md)。
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上设置事件网格模块的持久性
* 按照[文档](configure-client-auth.md)配置客户端身份验证
* 按照[本教程](forward-events-event-grid-cloud.md)将事件转发到云中的 Azure 事件网格
* [监视边缘的主题和订阅](monitor-topics-subscriptions.md)
