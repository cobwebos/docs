---
title: "IoT 中心操作监视"
description: "概述 IoT 中心操作监视如何让你实时监视 IoT 中心内的操作状态。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: 3c1ae13409c11ec49810209dd155e934b34c3a9b


---
# <a name="introduction-to-operations-monitoring"></a>操作监视简介
IoT 中心操作监视可让你实时监视其 IoT 中心上的操作状态。 IoT 中心可以跟踪多个操作类别的事件。 可以选择将一个或多个类别的事件发送到 IoT 中心的终结点进行处理。 你可以监视数据中是否有错误，或根据数据模式设置更复杂的处理行为。

IoT 中心监视 5 种类别的事件：

* 设备标识操作
* 设备遥测
* 云到设备的命令
* 连接
* 文件上载

## <a name="how-to-enable-operations-monitoring"></a>如何启用操作监视
1. 创建 IoT 中心。 有关如何创建 IoT 中心的说明，请参阅[入门][lnk-get-started]指南。
2. 打开 IoT 中心的边栏选项卡。 在此处单击“**操作监视**”。
   
    ![][1]
3. 选择要监视的监视类别，然后单击“**保存**”。 可以从“**监视设置**”中所列的与事件中心兼容的终结点读取事件。 IoT 中心终结点称为 `messages/operationsmonitoringevents`。
   
    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>事件类别及其用法
每种操作监视类别跟踪与 IoT 中心之间进行的不同类型的交互，每一种监视类别都有一个架构用于定义如何构建该类别的事件。

### <a name="device-identity-operations"></a>设备标识操作
设备标识操作类别跟踪你尝试在其 IoT 中心的标识注册表中创建、更新或删除条目时所发生的错误。 预配方案就很适合跟踪此类别。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>设备遥测
设备遥测类别跟踪在 IoT 中心发生的、与遥测管道相关的错误。 此类别包括发送遥测事件（例如限制）和接收遥测事件（例如未经授权的读取者）时发生的错误。 请注意，此类别无法捕捉设备本身运行的代码所造成的错误。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>云到设备的命令
云到设备的命令类别跟踪在 IoT 中心发生的、与设备命令管道相关的错误。 此类别包括发送命令（例如未经授权的发送者）、接收命令（例如超过传递计数）和接收命令反馈（例如反馈已过期）时所发生的错误。 此类别不捕捉未正确处理命令但却将其成功传递的设备所发生的错误。

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>连接
连接类别跟踪设备与 IoT 中心连接或断开连接时发生的错误。 若要识别未经授权的连接尝试，以及在连接质量不佳的区域中的设备断开连接时进行跟踪，就很适合跟踪此类别。

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>文件上载

文件上载类别跟踪在 IoT 中心发生的、与文件上载功能相关的错误。 此类别包括：

- SAS URI 发生的错误，例如，它在设备向中心通知某个完成的上载前失效。
- 由设备报告的失败上载。
- 创建 IoT 中心通知消息期间在存储中找不到文件时发生的错误。

请注意，此类别不能捕获在设备将文件上载到存储空间时直接发生的错误。


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>后续步骤
若要进一步探索 IoT 中心的功能，请参阅：

* [开发人员指南][lnk-devguide]
* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


