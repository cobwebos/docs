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
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 812ca3ba546112f54a76319fda853d441ce34f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303532"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT 中心的 Azure 事件网格事件架构

本文提供 Azure IoT 中心事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 

## <a name="available-event-types"></a>可用事件类型

Azure IoT 中心发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 | 

## <a name="example-event"></a>示例事件

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件属性

所有事件均包含相同的顶级数据： 

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| id | 字符串 | 事件的唯一标识符。 |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| 数据 | 对象 | IoT 中心事件数据。  |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

每个事件发布者的数据对象内容是不同的。 就 IoT 中心事件而言，数据对象包含以下属性：

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| hubName | 字符串 | 已创建或已删除设备的 IoT 中心的名称。 |
| deviceId | 字符串 | 设备的唯一标识符。 此区分大小写的字符串最多可长达 128 个字符，并支持 ASCII 7 位字母数字字符加上以下特殊字符：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |
| operationTimestamp | 字符串 | 操作的 ISO8601 时间戳。 |
| opType | 字符串 | IoT 中心为此操作指定的事件类型：`DeviceCreated` 或 `DeviceDeleted`。
| twin | 对象 | 有关设备孪生（即应用程序设备元数据的云表示形式）的信息。 | 
| deviceID | 字符串 | 设备孪生的唯一标识符。 | 
| etag | 字符串 | 一条说明设备孪生内容的信息。 每个 etag 保证对于每个设备孪生是唯一的。 | 
| status | 字符串 | 设备孪生是已启用还是已禁用。 | 
| statusUpdateTime | 字符串 | 上次设备孪生状态更新的 ISO8601 时间戳。 |
| connectionState | 字符串 | 设备是已连接还是已断开连接。 | 
| lastActivityTime | 字符串 | 上次活动的 ISO8601 时间戳。 | 
| cloudToDeviceMessageCount | integer | 发送到此设备的云到设备消息数。 | 
| authenticationType | 字符串 | 用于此设备的身份验证类型：`SAS`、`SelfSigned` 或 `CertificateAuthority`。 |
| x509Thumbprint | 字符串 | 指纹是 x509 证书的唯一值，通常用于在证书存储中查找特定证书。 指纹是使用 SHA1 算法动态生成的，并非在证书中实际存在。 | 
| primaryThumbprint | 字符串 | x509 证书的主要指纹。 |
| secondaryThumbprint | 字符串 | x509 证书的次要指纹。 | 
| 版本 | integer | 一个整数，每次更新设备孪生时递增 1。 |
| desired | 对象 | 只能由应用程序后端写入并且由设备读取的属性部分。 | 
| reported | 对象 | 只能由设备写入并且由应用程序后端读取的属性部分。 |
| lastUpdated | 字符串 | 上次设备孪生属性更新的 ISO8601 时间戳。 | 

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 若要了解 IoT 中心与事件网格如何协同工作，请参阅[使用事件网格触发操作对 IoT 中心事件做出响应](../iot-hub/iot-hub-event-grid.md)。