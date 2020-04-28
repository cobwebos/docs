---
title: 作为事件网格源的 Azure IoT 中心
description: 本文提供 Azure IoT 中心事件的属性和架构。 它列出了可用的事件类型、示例事件和事件属性。
services: iot-hub
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f9bf807884ab5592fa320532f3ca10a223081263
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393329"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>作为事件网格源的 Azure IoT 中心
本文提供 Azure IoT 中心事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

Azure IoT 中心发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 | 
| Microsoft.Devices.DeviceConnected | 当设备连接到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDisconnected | 当设备与 IoT 中心断开连接时发布。 | 
| Microsoft.Devices.DeviceTelemetry | 当遥测消息发送到 IoT 中心时发布。 |

除设备遥测事件外的所有设备事件在事件网格支持的所有区域中都已正式发布。 设备遥测事件处于公共预览阶段，在美国东部、美国西部、西欧、 [Azure 政府](../azure-government/documentation-government-welcome.md)版、 [Azure 中国世纪互联](/azure/china/china-welcome)和[azure 德国](https://azure.microsoft.com/global-infrastructure/germany/)以外的所有区域提供。

### <a name="example-event"></a>示例事件

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

将遥测事件发送到 IoT 中心时，将引发 DeviceTelemetry 事件。 此事件的示例架构如下所示。

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

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| ID | 字符串 | 事件的唯一标识符。 |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| data | 对象 (object) | IoT 中心事件数据。  |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

对于所有 IoT 中心事件，数据对象包含以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| hubName | 字符串 | 已创建或已删除设备的 IoT 中心的名称。 |
| deviceId | 字符串 | 设备的唯一标识符。 此区分大小写的字符串最多可长达 128 个字符，并支持 ASCII 7 位字母数字字符加上以下特殊字符：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |

每个事件发布者的数据对象内容是不同的。 

对于**设备已连接**和**设备已断开连接** IoT 中心事件，数据对象包含以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| moduleId | 字符串 | 模块的唯一标识符。 此字段是仅适用于模块设备的输出。 此区分大小写的字符串最多可长达 128 个字符，并支持 ASCII 7 位字母数字字符加上以下特殊字符：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |
| deviceConnectionStateEventInfo | 对象 (object) | 设备连接状态事件信息
| sequenceNumber | 字符串 | 一个数字，有助于指示设备已连接或设备已断开连接事件的顺序。 最新事件的序列号将大于上一个事件。 此数字可能会变化超过 1，但严格地说，是在增加。 请参阅[如何使用序列号](../iot-hub/iot-hub-how-to-order-connection-state-events.md)。 |

对于**设备遥测** IoT 中心事件，数据对象包含 [IoT 中心消息格式](../iot-hub/iot-hub-devguide-messages-construct.md)的设备到云消息，并具有以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| body | 字符串 | 来自设备的消息内容。 |
| properties | 字符串 | 应用程序属性是用户定义的字符串，可以添加到消息。 这些字段是可选的。 |
| 系统属性 | 字符串 | [系统属性](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)有助于识别消息的内容和源。 设备遥测消息必须采用有效的 JSON 格式，并且在消息系统属性中将 contentType 设置为 JSON，将 contentEncoding 设置为 UTF-8。 如果未设置此项，则 IoT 中心将以 base 64 编码格式写入消息。  |

对于**设备已创建**和**设备已删除** IoT 中心事件，数据对象包含以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| twin | 对象 (object) | 有关设备孪生（即应用程序设备元数据的云表示形式）的信息。 | 
| deviceID | 字符串 | 设备孪生的唯一标识符。 | 
| etag | 字符串 | 用于确保设备孪生更新一致性的验证程序。 每个 etag 保证对于每个设备孪生是唯一的。 |  
| deviceEtag| 字符串 | 用于确保设备注册表更新一致性的验证程序。 每个 deviceEtag 保证对于每个设备注册表是唯一的。 |
| status | 字符串 | 设备孪生是已启用还是已禁用。 | 
| statusUpdateTime | 字符串 | 上次设备孪生状态更新的 ISO8601 时间戳。 |
| connectionState | 字符串 | 设备是已连接还是已断开连接。 | 
| lastActivityTime | 字符串 | 上次活动的 ISO8601 时间戳。 | 
| cloudToDeviceMessageCount | 整数 | 发送到此设备的云到设备消息数。 | 
| authenticationType | 字符串 | 用于此设备的身份验证类型：`SAS`、`SelfSigned` 或 `CertificateAuthority`。 |
| x509Thumbprint | 字符串 | 指纹是 x509 证书的唯一值，通常用于在证书存储中查找特定证书。 指纹是使用 SHA1 算法动态生成的，并非在证书中实际存在。 | 
| primaryThumbprint | 字符串 | x509 证书的主要指纹。 |
| secondaryThumbprint | 字符串 | x509 证书的次要指纹。 | 
| version | 整数 | 一个整数，每次更新设备孪生时递增 1。 |
| desired | 对象 (object) | 只能由应用程序后端写入并且由设备读取的属性部分。 | 
| reported | 对象 (object) | 只能由设备写入并且由应用程序后端读取的属性部分。 |
| lastUpdated | 字符串 | 上次设备孪生属性更新的 ISO8601 时间戳。 | 

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|Title  |说明  |
|---------|---------|
| [使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [通过使用事件网格触发操作来响应 IoT 中心事件](../iot-hub/iot-hub-event-grid.md) | 概述 IoT 中心与事件网格的集成。 |
| [订阅设备已连接和设备已断开连接事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | 显示如何订阅设备连接状态事件。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 若要了解 IoT 中心与事件网格如何协同工作，请参阅[使用事件网格触发操作对 IoT 中心事件做出响应](../iot-hub/iot-hub-event-grid.md)。