---
title: IoT 中心的 Azure 事件网格架构 | Microsoft Docs
description: IoT 中心的事件架构格式和属性参考页
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393190"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT 中心的 Azure 事件网格事件架构

本文提供 Azure IoT 中心事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 

有关示例脚本和教程的列表，请参阅 [IoT 中心事件源](event-sources.md#iot-hub)。

## <a name="available-event-types"></a>可用事件类型

Azure IoT 中心发出以下事件类型：

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 | 
| Microsoft.Devices.DeviceConnected | 当设备连接到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDisconnected | 当设备与 IoT 中心断开连接时发布。 | 
| Microsoft.Devices.DeviceTelemetry | 遥测消息发送到 IoT 中心时发布。 |

设备遥测事件的所有设备事件通过事件网格支持的所有区域中都已公开上市。 设备遥测事件处于公共预览状态，在除美国东部、 美国西部、 欧洲西部的所有区域均提供[Azure 政府版](/azure-government/documentation-government-welcome.md)， [Azure 中国 21Vianet](/azure/china/china-welcome.md)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>示例事件

DeviceConnected 和 DeviceDisconnected 事件的架构具有相同结构。 此示例事件显示设备连接到 IoT 中心时引发的事件的架构：

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

遥测事件发送到 IoT 中心时，将引发 devicetelemetry 事件。 此事件的示例架构如下所示。

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

DeviceCreated 和 DeviceDeleted 事件的架构具有相同结构。 此示例事件显示设备注册到 IoT 中心时引发的事件的架构：

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件属性

所有事件均包含相同的顶级数据： 

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| id | string | 事件的唯一标识符。 |
| topic | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| eventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| data | 对象 | IoT 中心事件数据。  |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

对于所有 IoT 中心事件，数据对象包含以下属性：

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| hubName | string | 已创建或已删除设备的 IoT 中心的名称。 |
| deviceId | string | 设备的唯一标识符。 此区分大小写的字符串最多可长达 128 个字符，并支持 ASCII 7 位字母数字字符加上以下特殊字符：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |

每个事件发布者的数据对象内容是不同的。 

对于**设备已连接**和**设备已断开连接** IoT 中心事件，数据对象包含以下属性：

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| moduleId | string | 模块的唯一标识符。 此字段是仅适用于模块设备的输出。 此区分大小写的字符串最多可长达 128 个字符，并支持 ASCII 7 位字母数字字符加上以下特殊字符：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |
| deviceConnectionStateEventInfo | 对象 | 设备连接状态事件信息
| sequenceNumber | string | 一个数字，有助于指示设备已连接或设备已断开连接事件的顺序。 最新事件的序列号将大于上一个事件。 此数字可能会变化超过 1，但严格地说，是在增加。 请参阅[如何使用序列号](../iot-hub/iot-hub-how-to-order-connection-state-events.md)。 |

有关**设备遥测**IoT 中心事件数据对象包含中的设备到云消息[IoT 中心消息格式](../iot-hub/iot-hub-devguide-messages-construct.md)并具有以下属性：

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| body | string | 来自设备的消息的内容。 |
| properties | string | 应用程序属性是用户定义的字符串，可以添加到消息。 这些字段是可选的。 |
| 系统属性 | string | [系统属性](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)帮助识别内容和消息的源。 设备的遥测消息必须采用有效的 JSON 格式与将 contentType 设置为 JSON 和 contentEncoding 中的消息系统属性设置为 utf-8。 如果未设置，IoT 中心将在基的 64 编码格式中写入消息。  |

对于**设备已创建**和**设备已删除** IoT 中心事件，数据对象包含以下属性：

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| twin | 对象 | 有关设备孪生（即应用程序设备元数据的云表示形式）的信息。 | 
| deviceID | string | 设备孪生的唯一标识符。 | 
| etag | string | 用于确保设备孪生更新一致性的验证程序。 每个 etag 保证对于每个设备孪生是唯一的。 |  
| deviceEtag| string | 用于确保设备注册表更新一致性的验证程序。 每个 deviceEtag 保证对于每个设备注册表是唯一的。 |
| status | string | 设备孪生是已启用还是已禁用。 | 
| statusUpdateTime | string | 上次设备孪生状态更新的 ISO8601 时间戳。 |
| connectionState | string | 设备是已连接还是已断开连接。 | 
| lastActivityTime | string | 上次活动的 ISO8601 时间戳。 | 
| cloudToDeviceMessageCount | integer | 发送到此设备的云到设备消息数。 | 
| authenticationType | string | 用于此设备的身份验证类型：`SAS`、`SelfSigned` 或 `CertificateAuthority`。 |
| x509Thumbprint | string | 指纹是 x509 证书的唯一值，通常用于在证书存储中查找特定证书。 指纹是使用 SHA1 算法动态生成的，并非在证书中实际存在。 | 
| primaryThumbprint | string | x509 证书的主要指纹。 |
| secondaryThumbprint | string | x509 证书的次要指纹。 | 
| version | integer | 一个整数，每次更新设备孪生时递增 1。 |
| desired | 对象 | 只能由应用程序后端写入并且由设备读取的属性部分。 | 
| reported | 对象 | 只能由设备写入并且由应用程序后端读取的属性部分。 |
| lastUpdated | string | 上次设备孪生属性更新的 ISO8601 时间戳。 | 

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 若要了解 IoT 中心与事件网格如何协同工作，请参阅[使用事件网格触发操作对 IoT 中心事件做出响应](../iot-hub/iot-hub-event-grid.md)。