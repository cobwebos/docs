---
title: 发布、订阅云中的事件-Azure 事件网格 IoT Edge |Microsoft Docs
description: 使用 IoT Edge 上的事件网格在云中发布和订阅事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5fb6cab4bfeea4308873210fb5f9122b37b61dcd
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100320"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>教程：在云中发布、订阅事件

本文逐步讲解在 IoT Edge 上使用事件网格发布和订阅事件所需的所有步骤。

在继续之前，请参阅[事件网格概念](concepts.md)，以了解什么是事件网格主题和订阅。

## <a name="prerequisites"></a>必备组件 
为了完成本教程，你将需要：

* **Azure 订阅**-如果你还没有帐户，请创建一个[免费帐户](https://azure.microsoft.com/free)。 
* **Azure IoT 中心和 IoT Edge 设备**-按照[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤进行操作（如果尚未安装）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>在 Azure 门户中创建 Azure 函数

按照[教程](../../azure-functions/functions-create-first-azure-function.md)中所述的步骤创建 Azure 功能。 

将代码片段替换为以下代码：

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

在新函数中，选择右上角的 "**获取函数 URL** "，选择 "默认（**函数密钥**）"，然后选择 "**复制**"。 您将在本教程的后面部分使用 "函数 URL" 值。

> [!NOTE]
> 有关使用 EventGrid 事件触发器响应事件的更多示例和教程，请参阅[Azure Functions](../../azure-functions/functions-overview.md)文档。

## <a name="create-a-topic"></a>创建主题

作为事件的发布者，需要创建事件网格主题。 主题指的是发布者可将事件发送到的终结点。

1. 创建具有以下内容的 topic2。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 运行以下命令以创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 运行以下命令以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

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

订户可以注册发布到主题的事件。 若要接收任何事件，订阅者需要在相关主题上创建事件网格订阅。

1. 创建具有以下内容的 subscription2。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

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
   > **EndpointType**指定订阅服务器是 Webhook。  **EndpointUrl**指定订阅服务器侦听事件的 URL。 此 URL 对应于你之前设置的 Azure Function 示例。
2. 运行以下命令以创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

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

1. 创建具有以下内容的 event2。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

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

可以在函数的 "**监视**" 选项下的 "Azure 门户中查看传递的事件。

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* 删除在 Azure 门户中创建的 Azure 函数。

## <a name="next-steps"></a>后续步骤

在本教程中，您创建了事件网格主题、订阅和已发布的事件。 现在，你已了解基本步骤，请参阅以下文章：

* 若要解决在 IoT Edge 上使用 Azure 事件网格时遇到的问题，请参阅[故障排除指南](troubleshoot.md)。
* 创建/更新具有[筛选器](advanced-filtering.md)的订阅。
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上设置事件网格模块的持久性
* 按照[文档](configure-client-auth.md)配置客户端身份验证
* 遵循本[教程](forward-events-event-grid-cloud.md)将事件转发到云中的 Azure 事件网格
