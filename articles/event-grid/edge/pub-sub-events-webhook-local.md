---
title: 发布、在本地订阅事件-Azure 事件网格 IoT Edge |Microsoft Docs
description: 发布、使用 IoT Edge 上的事件网格在本地订阅事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b484306504af8f83a393feb0469fff5b524948ab
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992205"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>教程：在本地发布和订阅事件

本文将指导你完成使用 IoT Edge 上的事件网格发布和订阅事件所需的所有步骤。

> [!NOTE]
> 若要了解有关 Azure 事件网格主题和订阅的信息，请参阅[事件网格概念](concepts.md)。

## <a name="prerequisites"></a>必备组件 
为了完成本教程，你将需要：

* **Azure 订阅**-如果你还没有帐户，请创建一个[免费帐户](https://azure.microsoft.com/free)。 
* **Azure IoT 中心和 IoT Edge 设备**-按照适用于[Linux](../../iot-edge/quickstart-linux.md)或[Windows 设备](../../iot-edge/quickstart.md)的快速入门中的步骤（如果尚未安装）。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT Edge 模块

可以通过多种方式将模块部署到 IoT Edge 设备，并且所有这些模块都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍了在 Azure 门户中 IoT Edge 部署事件网格的步骤。

>[!NOTE]
> 在本教程中，您将部署不带持久性的事件网格模块。 这意味着，在您重新部署该模块时，将删除您在本教程中创建的任何主题和订阅。 有关如何设置持久性的详细信息，请参阅以下文章：[在 Linux 中持久保存状态](persist-state-linux.md)或[在 Windows 中保持状态](persist-state-windows.md)。 对于生产工作负荷，我们建议您安装带有持久性的事件网格模块。


### <a name="select-your-iot-edge-device"></a>选择 IoT Edge 设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从 "**自动设备管理**" 部分的菜单中选择 " **IoT Edge** "。 
1. 从设备列表中单击目标设备的 ID
1. 选择“设置模块”。 使页面保持打开状态。 你将继续执行下一节中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个向导，该向导将引导你创建部署清单，而不是手动生成 JSON 文档。  创建分为三步：添加模块、指定路由和评审部署。

### <a name="add-modules"></a>添加模块

1. 在 "**部署模块**" 部分，选择 "**添加**"
1. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
1. 提供容器的名称、图像、容器创建选项：

   * **名称**： eventgridmodule
   * **映像 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

    ```json
        {
          "Env": [
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. 单击“保存”
 1. 请继续阅读下一部分，在将其部署到一起之前添加 Azure Functions 模块。

    >[!IMPORTANT]
    > 在本教程中，将部署已禁用客户端身份验证并允许 HTTP 订阅服务器的事件网格模块。 对于生产工作负荷，建议启用客户端身份验证，并且仅允许 HTTPs 订阅服务器。 有关如何安全配置事件网格模块的详细信息，请参阅[安全性和身份验证](security-authentication.md)。
    

## <a name="deploy-azure-function-iot-edge-module"></a>部署 Azure Function IoT Edge 模块

本部分介绍如何部署 Azure Functions IoT 模块，该模块将充当可传递事件的事件网格订户。

>[!IMPORTANT]
>在本部分中，你将部署一个基于 Azure 功能的示例订阅模块。 当然，它也可以是可侦听 HTTP POST 请求的任何自定义 IoT 模块。


### <a name="add-modules"></a>添加模块

1. 在 "**部署模块**" 部分，再次选择 "**添加**"。 
1. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
1. 提供容器的 "名称"、"映像" 和 "容器" 创建选项：

   * **名称**：订阅服务器
   * **映像 URI**： `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **容器创建选项**：

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. 单击“保存”
1. 单击 "**下一步**" 以继续转到 "路由" 部分

 ### <a name="setup-routes"></a>安装路由

保留默认路由，然后选择 "**下一步**" 继续查看部分

### <a name="submit-the-deployment-request"></a>提交部署请求

1. "查看" 部分显示基于你在上一节中所做的选择创建的 JSON 部署清单。 确认你看到了 JSON 中列出的模块： **eventgridmodule**和**订阅服务器**。 
1. 审阅部署信息，然后选择“提交”。 提交部署后，返回到 "**设备**" 页。
1. 在 "**模块" 部分**，验证是否列出了 " **eventgrid** " 和 "**订户**" 模块。 然后，验证 "**部署中指定**的" 和 "**由设备报告**" 列设置为 **"是"** 。

    可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="create-a-topic"></a>创建主题

作为事件的发布者，需要创建事件网格主题。 在 Azure 事件网格中，主题指的是发布服务器可将事件发送到的终结点。

1. 创建包含以下内容的主题。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 运行以下命令以创建事件网格主题。 确认 `200 OK`显示 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 运行以下命令以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>创建事件订阅

订户可以注册发布到主题的事件。 若要接收任何事件，需要为感兴趣的主题创建事件网格订阅。

1. 创建具有以下内容的订阅。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **EndpointType**属性指定订阅服务器是**Webhook**。  **EndpointUrl**指定订阅服务器侦听事件的 URL。 此 URL 对应于之前部署的 Azure Function 示例。
2. 运行以下命令以创建主题的订阅。 确认 `200 OK`显示 HTTP 状态代码。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>发布事件

1. 创建包含以下内容的事件 json。 有关有效负载的详细信息，请参阅[API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. 运行以下命令以发布事件。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传递

1. SSH 或 RDP 连接到 IoT Edge VM。
1. 检查订阅服务器日志：

    在 Windows 上，运行以下命令：

    ```sh
    iotedge logs subscriber -f
    ```

   在 Linux 上，运行以下命令：

    ```sh
    sudo docker logs subscriber
    ```

    示例输出：

    ```sh
        Received event data [
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
          ]
    ```

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 从 IoT Edge 设备中删除订阅者模块。


## <a name="next-steps"></a>后续步骤
在本教程中，您创建了事件网格主题、订阅和已发布的事件。 现在，你已了解基本步骤，请参阅以下文章： 

- 创建/更新具有[筛选器](advanced-filtering.md)的订阅。
- 在[Linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上启用事件网格模块的持久性
- 按照[文档](configure-client-auth.md)配置客户端身份验证
- 遵循本[教程](pub-sub-events-webhook-cloud.md)将事件转发到云中 Azure Functions
- [响应 IoT Edge 上的 Blob 存储事件](react-blob-storage-events-locally.md)

