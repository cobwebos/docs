---
title: 在本地发布、订阅事件 - Azure 事件网格 IoT 边缘 |微软文档
description: 使用 IoT 边缘的事件网格在本地发布、订阅事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280997"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>教程：在本地发布、订阅事件

本文将引导您完成使用 IoT Edge 上的事件网格发布和订阅事件所需的所有步骤。

> [!NOTE]
> 要了解 Azure 事件网格主题和订阅，请参阅[事件网格概念](concepts.md)。

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* **Azure 订阅**- 如果尚未创建[免费帐户](https://azure.microsoft.com/free)，则创建免费帐户。 
* **Azure IoT 中心和 IoT 边缘设备**- 如果尚未运行 Linux[或](../../iot-edge/quickstart-linux.md)Windows 设备，请按照 Linux 或[Windows 设备](../../iot-edge/quickstart.md)的快速入门步骤操作。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT 边缘模块

有几种方法可以将模块部署到 IoT 边缘设备，并且所有这些模块都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍从 Azure 门户在 IoT 边缘上部署事件网格的步骤。

>[!NOTE]
> 在本教程中，您将部署事件网格模块，而不进行持久性。 这意味着在重新部署模块时，您在本教程中创建的任何主题和订阅都将被删除。 有关如何设置持久性的详细信息，请参阅以下文章[：Linux 中的持久状态](persist-state-linux.md)或[Windows 中的持久状态](persist-state-windows.md)。 对于生产工作负载，我们建议您安装具有持久性的事件网格模块。


### <a name="select-your-iot-edge-device"></a>选择 IoT 边缘设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从 **"自动设备管理**"部分中的菜单中选择**IoT 边缘**。 
1. 从设备列表中单击目标设备的 ID
1. 选择**设置模块**。 保持页面打开。 您将继续下一节中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。  创建分为三步：添加模块、指定路由和评审部署************。

### <a name="add-modules"></a>添加模块

1. 在 **"部署模块"** 部分中，选择 **"添加**
1. 从下拉列表中的模块类型中，选择**IoT 边缘模块**
1. 提供容器的名称、图像、容器创建选项：

   * **名称**： 事件网格模块
   * **图像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
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
 1. 单击 **“保存”**
 1. 继续下一节以添加 Azure 事件网格订阅服务器模块，然后再将它们一起部署。

    >[!IMPORTANT]
    > 在本教程中，您将部署禁用客户端身份验证的事件网格模块。 对于生产工作负载，我们建议您启用客户端身份验证。 有关如何安全地配置事件网格模块的详细信息，请参阅[安全性和身份验证](security-authentication.md)。
    > 
    > 如果使用 Azure VM 作为边缘设备，请添加入站端口规则以允许端口 4438 上的入站流量。 有关添加规则的说明，请参阅[如何将端口打开到 VM](../../virtual-machines/windows/nsg-quickstart-portal.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>部署事件网格订阅者 IoT 边缘模块

本节介绍如何部署另一个 IoT 模块，该模块将作为事件处理程序，将事件传递到该模块。

### <a name="add-modules"></a>添加模块

1. 在 **"部署模块"** 部分中，选择"再次**添加**"。 
1. 从下拉列表中的模块类型中，选择**IoT 边缘模块**
1. 提供容器的名称、图像和容器创建选项：

   * **名称**： 订阅者
   * **图像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器创建选项**： 无
1. 单击 **“保存”**
1. 单击 **"下一步**"以继续到路径部分

 ### <a name="setup-routes"></a>设置路由

保留默认路由，然后选择 **"下一步**"以继续查看部分

### <a name="submit-the-deployment-request"></a>提交部署请求

1. 审核部分显示基于上一节中的选择创建的 JSON 部署清单。 确认您同时看到 JSON 中列出的模块：**事件网格模块**和**订阅者**。 
1. 审阅部署信息，然后选择“提交”****。 提交部署后，您将返回到**设备**页面。
1. 在 **"模块"部分**中，验证**是否同时列出了事件网格**和**订阅服务器**模块。 此外，请验证**部署中的指定**和**按设备列报告的**设置为 **"是**"。

    可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="create-a-topic"></a>创建主题

作为事件的发布者，您需要创建事件网格主题。 在 Azure 事件网格中，主题是指发布者可以向其发送事件的终结点。

1. 使用以下内容创建主题.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 运行以下命令以创建事件网格主题。 确认您看到 HTTP 状态代码为`200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 运行以下命令以验证主题已成功创建。 应返回 200 OK 的 HTTP 状态代码。

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

订阅者可以注册发布到主题的事件。 要接收任何事件，您需要为感兴趣的主题创建事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下内容创建订阅.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **终结点类型**属性指定订阅者是**Webhook**。  **终结点 Url**指定订阅者侦听事件的 URL。 此 URL 对应于前面部署的 Azure 订阅服务器示例。
2. 运行以下命令为主题创建订阅。 确认您看到 HTTP 状态代码为`200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证已成功创建订阅。 应返回 200 OK 的 HTTP 状态代码。

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
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>发布事件

1. 使用以下内容创建 event.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)。

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

1. SSH 或 RDP 进入 IoT 边缘 VM。
1. 检查订阅者日志：

    在 Windows 上，运行以下命令：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   在 Linux 上，运行以下命令：

    ```sh
    sudo docker logs subscriber
    ```

    示例输出：

    ```sh
        Received Event:
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
    ```

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 从 IoT 边缘设备中删除订阅者模块。


## <a name="next-steps"></a>后续步骤
在本教程中，您创建了事件网格主题、订阅和已发布的事件。 现在您已经了解了基本步骤，请参阅以下文章： 

- 要解决在 IoT 边缘上使用 Azure 事件网格的问题，请参阅[故障排除指南](troubleshoot.md)。
- 使用筛选器创建/更新[订阅](advanced-filtering.md)。
- 在[Linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上启用事件网格模块的持久性
- 按照[文档](configure-client-auth.md)配置客户端身份验证
- 按照[本教程](pub-sub-events-webhook-cloud.md)将事件转发到云中的 Azure 函数
- [响应 IoT 边缘的 Blob 存储事件](react-blob-storage-events-locally.md)
- [监视边缘的主题和订阅](monitor-topics-subscriptions.md)

