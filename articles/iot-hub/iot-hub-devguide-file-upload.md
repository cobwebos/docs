---
title: 了解 Azure IoT 中心文件上传 | Microsoft Docs
description: 开发人员指南 - 使用 IoT 中心的文件上传功能，可将文件从设备上传到 Azure 存储 blob 容器。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 217d348eacab30b90e06fe805d9cdb0cf32349ac
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050724"
---
# <a name="upload-files-with-iot-hub"></a>使用 IoT 中心上传文件

如 [IoT 中心终结点](iot-hub-devguide-endpoints.md)一文中详述，设备可以通过面向设备的终结点 (**/devices/{deviceId}/files**) 发送通知以启动文件上传。 当设备通知 IoT 中心已完成某个上传时，IoT 中心会通过面向服务的终结点 (**/messages/servicebound/filenotifications**) 发送文件上传通知消息。

IoT 中心本身不中转消息，而是充当关联 Azure 存储帐户的调度程序。 设备请求来自 IoT 中心的存储令牌，该令牌特定于设备要上传的文件。 设备使用 SAS URI 将文件上传到存储，上传完成后，设备将完成通知发送到 IoT 中心。 IoT 中心检查文件上传是否已完成，然后将文件上传通知消息添加到面向服务的文件通知终结点。

从设备将文件上传到 IoT 中心之前，必须通过为其[关联 Azure 存储](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)帐户来配置该中心。

然后，设备就能[初始化上传](iot-hub-devguide-file-upload.md#initialize-a-file-upload)，并在上传完成后[通知 IoT 中心](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)。 （可选）设备通知 IoT 中心上传完成以后，服务可以生成[通知消息](iot-hub-devguide-file-upload.md#file-upload-notifications)。

### <a name="when-to-use"></a>使用时机

使用文件上传，发送间歇性连接的设备上传的媒体文件和大型遥测批文件（或者是压缩后的文件，以节省带宽）。

如果在使用报告属性、设备到云消息或文件上传方面有任何疑问，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>将 Azure 存储帐户与 IoT 中心相关联

要使用文件上传功能，必须首先将 Azure 存储帐户链接到 IoT 中心。 可通过 Azure 门户完成此任务，或通过 [IoT 中心资源提供程序 REST API](/rest/api/iothub/iothubresource) 以编程方式完成此任务。 将 Azure 存储帐户与 IoT 中心关联后，当设备启动文件上传请求时，此服务将向该设备返回 SAS URI。

[使用 IoT 中心将文件从设备上传到云](iot-hub-csharp-csharp-file-upload.md)操作指南提供了文件上传过程的完整演练。 这些操作指南展示了如何使用 Azure 门户将存储帐户与 IoT 中心相关联。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 自动处理检索 SAS URI、上传文件和通知 IoT 中心已完成上传等操作。

## <a name="initialize-a-file-upload"></a>初始化文件上传
IoT 中心有一个终结点，专供设备在上传文件时请求用于存储的 SAS URI。 为了启动文件上传过程，设备会使用以下 JSON 正文向 `{iot hub}.azure-devices.net/devices/{deviceId}/files` 发送 POST 请求：

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT 中心返回以下数据，供设备用来上传文件：

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>已弃用：使用 GET 初始化文件上传

> [!NOTE]
> 本部分介绍已弃用的功能，这些功能用于从 IoT 中心接收 SAS URI。 使用前面所述的 POST 方法。

IoT 中心有两个 REST 终结点支持文件上传，一个用于获取存储的 SAS URI，另一个用于通知 IoT 中心已完成上传。 设备通过在 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` 向 IoT 中心发送 GET 来启动文件上传过程。 IoT 中心返回：

* 特定于要上传的文件的 SAS URI。

* 上传完成后要使用的相关 ID。

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>通知 IoT 中心已完成文件上传

设备使用 Azure 存储 SDK 将文件上传到存储。 上传完成后，设备会使用以下 JSON 正文向 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` 发送 POST 请求：

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 的值为布尔值，指示文件是否已成功上传。 `statusCode` 的状态代码表示将文件上传到存储时的状态，`statusDescription` 对应于 `statusCode`。

## <a name="reference-topics"></a>参考主题：

以下参考主题详细介绍了如何从设备上传文件。

## <a name="file-upload-notifications"></a>文件上传通知

（可选）当设备通知 IoT 中心某个上传完成后，IoT 中心将生成一条通知消息。 此消息包含文件的名称和存储位置。

如[终结点](iot-hub-devguide-endpoints.md)中所述，IoT 中心通过面向服务的终结点 (**/messages/servicebound/fileuploadnotifications**) 以消息的形式传递文件上传通知。 文件上传通知的接收语义与云到设备的消息的接收语义相同，并且具有相同的[消息生命周期](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle)。 从文件上传通知终结点检索到的每条消息都是具有以下属性的 JSON 记录：

| 属性 | 描述 |
| --- | --- |
| EnqueuedTimeUtc |指示通知创建时间的时间戳。 |
| DeviceId |上传文件的设备的 **DeviceId**。 |
| BlobUri |已上传文件的 URI。 |
| BlobName |已上传文件的名称。 |
| LastUpdatedTime |指示文件更新时间的时间戳。 |
| BlobSizeInBytes |已上传文件的大小。 |

**示例**。 此示例显示文件上传通知消息的正文。

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>文件上传通知配置选项

每个 IoT 中心都具有针对文件上传通知的以下配置选项：

| 属性 | 描述 | 范围和默认值 |
| --- | --- | --- |
| **enableFileUploadNotifications** |控制是否将文件上传通知写入文件通知终结点。 |布尔型。 默认值：True。 |
| **fileNotifications.ttlAsIso8601** |文件上传通知的默认 TTL。 |ISO_8601 间隔上限为 48 小时（下限为 1 分钟）。 默认值：1 小时。 |
| **fileNotifications.lockDuration** |文件上传通知队列的锁定持续时间。 |5 到 300 秒（最小为 5 秒）。 默认值：60 秒。 |
| **fileNotifications.maxDeliveryCount** |文件上传通知队列的最大传递计数。 |1 到 100。 默认值：100。 |

## <a name="additional-reference-material"></a>其他参考资料

IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)介绍了用于运行时和管理操作各种 IoT 中心终结点。

* [限制和配额](iot-hub-devguide-quotas-throttling.md)介绍了适用于 IoT 中心服务的配额和限制行为。

* [Azure IoT 设备和服务 SDK](iot-hub-devguide-sdks.md) 列出了开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。

* [IoT 中心查询语言](iot-hub-devguide-query-language.md)介绍了可用来从 IoT 中心检索设备孪生和作业相关信息的查询语言。

* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)提供了有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤

现在，你已了解了如何使用 IoT 中心从设备上传文件，接下来可以根据兴趣查看以下 IoT 中心开发人员指南主题：

* [管理 IoT 中心中的设备标识](iot-hub-devguide-identity-registry.md)

* [控制对 IoT 中心的访问](iot-hub-devguide-security.md)

* [使用设备孪生来同步状态和配置](iot-hub-devguide-device-twins.md)

* [在设备上调用直接方法](iot-hub-devguide-direct-methods.md)

* [在多个设备上计划作业](iot-hub-devguide-jobs.md)

要尝试本文中介绍的一些概念，请参阅以下 IoT 中心教程：

* [如何将文件从设备到 IoT 中心与云上传](iot-hub-csharp-csharp-file-upload.md)