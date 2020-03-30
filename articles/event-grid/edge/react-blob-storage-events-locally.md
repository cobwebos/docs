---
title: 响应 Blob 存储模块事件 - Azure 事件网格 IoT 边缘 |微软文档
description: 响应 Blob 存储模块事件
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086608"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>教程：对 IoT 边缘上的 Blob 存储事件做出反应（预览）
本文介绍如何在 IoT 模块上部署 Azure Blob 存储，该模块将充当事件网格发布者，将有关 Blob 创建和 Blob 删除的事件发送到事件网格。  

有关 IoT Edge 上的 Azure Blob 存储的概述，请参阅[IoT 边缘上的 Azure Blob 存储](../../iot-edge/how-to-store-data-blob.md)及其功能。

> [!WARNING]
> IoT 边缘与事件网格集成上的 Azure Blob 存储处于预览状态

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

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
    > 在本教程中，您将学习部署事件网格模块，以允许同时禁用 HTTP/HTTP 请求，禁用客户端身份验证。 对于生产工作负载，我们建议您仅启用启用客户端身份验证的 HTTP 请求和订阅者。 有关如何安全地配置事件网格模块的详细信息，请参阅[安全性和身份验证](security-authentication.md)。
    

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
1. 继续下一节以添加 Azure Blob 存储模块

## <a name="deploy-azure-blob-storage-module"></a>部署 Azure Blob 存储模块

本节介绍如何部署 Azure Blob 存储模块，该模块将充当发布创建和删除的 Blob 的事件网格发布者。

### <a name="add-modules"></a>添加模块

1. 在 **"部署模块"** 部分中，选择 **"添加**
2. 从下拉列表中的模块类型中，选择**IoT 边缘模块**
3. 提供容器的名称、图像和容器创建选项：

   * **名称**： azure blob 存储oniotedge
   * **图像 URI**： mcr.microsoft.com/azure-blob-storage:latest
   * **容器创建选项**：

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob 存储模块可以使用 HTTPS 和 HTTP 发布事件。 
   > - 如果已为 EventGrid 启用基于客户端的身份验证，请确保更新EVENTGRID_ENDPOINT的值以允许 https，如下所示： `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`。
   > - 此外，向上述`AllowUnknownCertificateAuthority=true`Json 添加另一个环境变量。 通过 HTTPS 与 EventGrid 对话时，**允许未知证书颁发机构**允许存储模块信任自签名的 EventGrid 服务器证书。

4. 使用以下信息更新复制的 JSON：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - 替换为`<event grid module name>`事件网格模块的名称。
   - 根据容器操作系统替换 `<storage mount>`。
     - 对于 Linux 容器，**我的卷：/blobroot**
     - 对于 Windows 容器，**我的卷：C：/BlobRoot**

5. 单击 **“保存”**
6. 单击 **"下一步**"以继续到路径部分

    > [!NOTE]
    > 如果使用 Azure VM 作为边缘设备，请添加入站端口规则，以允许本教程中使用的主机端口上的入站流量：4438、5888、8080 和 11002。 有关添加规则的说明，请参阅[如何将端口打开到 VM](../../virtual-machines/windows/nsg-quickstart-portal.md)。

### <a name="setup-routes"></a>设置路由

保留默认路由，然后选择 **"下一步**"以继续查看部分

### <a name="review-deployment"></a>评审部署

1. 审核部分显示基于上一节中的选择创建的 JSON 部署清单。 确认您将看到以下四个模块 **：$edgeAgent、$edgeHub、****事件网格模块**、**订阅者**和**Azureblob 存储oniotedge，** 所有这些模块都已部署。 **$edgeHub**
2. 审阅部署信息，然后选择“提交”****。

## <a name="verify-your-deployment"></a>验证部署

1. 提交部署后，返回到 IoT 中心的“IoT Edge”页。
2. 选择部署时针对的**IoT Edge 设备**以打开其详细信息。
3. 在设备详细信息中，验证事件网格模块、订阅者和 Azureblobstorageonioedge 模块是否同时列为 **"部署中指定**"和 **"按设备报告**"。

   可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="publish-blobcreated-and-blobdeleted-events"></a>发布 Blob 创建和 Blob 删除事件

1. 此模块会自动创建主题**微软存储**。 验证它是否存在
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    示例输出：

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - 对于 HTTPS 流，如果通过 SAS 密钥启用客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷曲请求将是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - 对于 HTTPS 流，如果通过证书启用客户端身份验证，则 curl 请求将为：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 订阅者可以注册发布到主题的事件。 要接收任何事件，您需要为**Microsoft 存储**主题创建事件网格订阅。
    1. 使用以下内容创建 blob 订阅.json。 有关有效负载的详细信息，请参阅我们的[API 文档](api.md)

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
       > **终结点类型**属性指定订阅者是**Webhook**。  **终结点 Url**指定订阅者侦听事件的 URL。 此 URL 对应于前面部署的 Azure 函数示例。

    2. 运行以下命令为主题创建订阅。 确认您看到 HTTP 状态代码为`200 OK`。

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - 对于 HTTPS 流，如果通过 SAS 密钥启用客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷曲请求将是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - 对于 HTTPS 流，如果通过证书启用客户端身份验证，则 curl 请求将为：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 运行以下命令以验证已成功创建订阅。 应返回 200 OK 的 HTTP 状态代码。

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       示例输出：

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - 对于 HTTPS 流，如果通过 SAS 密钥启用客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷曲请求将是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - 对于 HTTPS 流，如果通过证书启用客户端身份验证，则 curl 请求将为：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. 下载[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)[并将其连接到本地存储](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>验证事件传递

### <a name="verify-blobcreated-event-delivery"></a>验证 Blob 创建的事件传递

1. 将文件作为块 Blob 上载到 Azure 存储资源管理器的本地存储，模块将自动发布创建事件。 
2. 查看创建事件的订阅者日志。 按照步骤[验证事件传递](pub-sub-events-webhook-local.md#verify-event-delivery)

    示例输出：

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>验证 Blob 已删除的事件传递

1. 使用 Azure 存储资源管理器从本地存储中删除 blob，模块将自动发布删除事件。 
2. 查看删除事件的订阅者日志。 按照步骤[验证事件传递](pub-sub-events-webhook-local.md#verify-event-delivery)

    示例输出：
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

祝贺你！ 您已完成本教程。 以下各节提供有关事件属性的详细信息。

### <a name="event-properties"></a>事件属性

下面是支持的事件属性及其类型和说明的列表。 

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| data | 对象 (object) | Blob 存储事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| api | 字符串 | 触发事件的操作。 可以为下列值之一： <ul><li>Blob 创建 - 允许`PutBlob`的值是：`PutBlockList`</li><li>BlobDelete- 允许的值为`DeleteBlob``DeleteAfterUpload`和`AutoDelete`。 <p>当`DeleteAfterUpload`Blob 自动删除时，将生成该事件，因为 DeleteAfterUpload 所需的属性设置为 true。 </p><p>`AutoDelete`当 Blob 自动删除时生成事件，因为删除"删除后分钟"所需的属性值已过期。</p></li></ul>|
| ClientRequestId | 字符串 | 存储 API 操作的客户端提供的请求 ID。 此 ID 可用于使用日志中的"客户端-请求-id"字段与 Azure 存储诊断日志相关联，并且可以使用"x-ms-客户端-请求-id"标头在客户端请求中提供。 有关详细信息，请参阅[日志格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字符串 | 存储 API 操作的服务生成的请求 ID。 可用于通过 Azure 存储诊断日志中的“request-id-header”字段关联到这些日志，并且由“x-ms-request-id”标头中的初始化 API 调用返回。 请参阅[日志格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| eTag | 字符串 | 可用于根据条件执行操作的值。 |
| contentType | 字符串 | 为 Blob 指定的内容类型。 |
| contentLength | integer | Blob 大小，以字节为单位。 |
| blobType | 字符串 | Blob 的类型。 有效值为“BlockBlob”或“PageBlob”。 |
| url | 字符串 | Blob 的路径。 <br>如果客户端使用 Blob REST API，则 URL 具有此结构：*\<存储帐户名称\>\<.blob.core.windows.net/容器名称\>/\<文件名\>*。 <br>如果客户端使用 Data Lake 存储 REST API，则 URL 具有此结构：*\<存储\>帐户名称\<.dfs.core.windows.net/文件系统名称\>/\<文件名\>*。 |


## <a name="next-steps"></a>后续步骤

请参阅 Blob 存储文档中的以下文章：

- [筛选 Blob 存储事件](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [用于使用 Blob 存储事件的推荐做法](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

在本教程中，通过创建或删除 Azure Blob 存储中的 Blob 来发布事件。 请参阅其他教程，了解如何将事件转发到云（Azure 事件中心或 Azure IoT 中心）： 

- [将事件转发到 Azure 事件网格](forward-events-event-grid-cloud.md)
- [将事件转发到 Azure IoT 中心](forward-events-iothub.md)
