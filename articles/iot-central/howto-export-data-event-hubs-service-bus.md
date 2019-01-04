---
title: 将数据导出到 Azure 事件中心和 Azure 服务总线 | Microsoft Docs
description: 如何将数据从 Azure IoT Central 应用程序导出到 Azure 事件中心和 Azure 服务总线
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318370"
---
# <a name="export-your-data-in-azure-iot-central"></a>导出 Azure IoT Central 中的数据

*本主题适用于管理员。*

本文更深入地介绍如何使用 Azure IoT Central 中的连续数据导出功能将数据导出到你自己的 **Azure 事件中心**和 **Azure 服务总线**实例。 可以将“度量”、“设备”和“设备模板”导出至自己的目标，以便执行暖路径见解和分析操作。 这包括在 Azure 流分析中触发自定义规则、在 Azure 逻辑应用中触发自定义工作流，或者通过 Azure Functions 转换和传递数据。 

> [!Note]
> 同样，启用连续数据导出时，只能获得从那时之后的数据。 目前，关闭连续数据导出后将暂时无法检索数据。 若要保留更多的历史数据，请及早启用连续数据导出。


## <a name="prerequisites"></a>先决条件

- 在 IoT Central 应用程序中，你必须是管理员

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>导出到 Azure 事件中心和 Azure 服务总线

度量、设备和设备模板数据以近实时方式导出到事件中心或服务总线队列或主题。 导出的度量数据包含设备发送到 IoT Central 的消息的全部内容，不只是度量值本身。 导出的设备数据包含对所有设备的属性和设置所做的更改，而导出的设备模板则包含对所有设备模板的更改。 导出的数据位于“body”属性中，采用 JSON 格式。

> [!NOTE]
> 选择服务总线作为导出目标时，队列和主题**不得启用“会话”或“重复检测”**。 如果启用了其中任一选项，则某些消息不会到达队列或主题中。

### <a name="measurements"></a>度量

当 IoT Central 从设备收到新消息后，该消息会快速导出。 事件中心和服务总线中的每个导出的消息都包含设备在“body”属性中以 JSON 格式发送的完整消息。 

> [!NOTE]
> 发送度量的设备由设备 ID 表示（请参阅以下部分）。 若要获取设备的名称，请导出设备数据并使用与设备消息的 deviceId 匹配的 connectionDeviceId 来关联每条消息。

以下示例显示了一条消息，该消息描述在事件中心或服务总线队列或主题中接收的度量数据。

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>设备

每隔数分钟，包含设备数据的消息就会发送到事件中心或服务总线队列或主题。 这意味着每隔数分钟就会有一批消息到达，其中的数据涉及：
- 已添加的新设备
- 其属性和设置值已更改的设备

每条消息表示自上次导出消息以来对设备所做的一项或多项更改。 将要在每条消息中发送的信息包括：
- IoT Central 中设备的 `id`
- 设备的 `name`
- [设备预配服务](https://aka.ms/iotcentraldocsdps)中的 `deviceId`
- 设备模板信息
- 属性值
- 设置值

> [!NOTE]
> 上一批次导出后删除的设备不会导出。 目前，在导出的消息中没有针对已删除设备的指示器。
>
> 每台设备所属的设备模板由设备模板 ID 表示。 若要获取设备模板的名称，请务必也导出设备模板数据。

以下示例显示了一条消息，该消息描述在事件中心或服务总线的队列或主题中的设备数据：


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>设备模板

每隔数分钟，包含设备模板数据的消息就会发送到事件中心或服务总线队列或主题。 这意味着每隔数分钟就会有一批消息到达，其中的数据涉及：
- 已添加的新设备模板
- 其度量、属性和设置定义已更改的设备模板

每条消息表示自上次导出消息以来对设备模板所做的一项或多项更改。 将要在每条消息中发送的信息包括：
- 设备模板的 `id`
- 设备模板的 `name`
- 设备模板的 `version`
- 度量数据类型和最小/最大值
- 属性数据类型和默认值
- 设置数据类型和默认值

> [!NOTE]
> 上一批次导出后删除的设备模板不会导出。 目前，在导出的消息中没有针对已删除设备模板的指示器。

以下示例显示了一条消息，该消息描述在事件中心或服务总线的队列或主题中的设备模板数据：

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>后续步骤

了解如何将数据导出到 Azure 事件中心和 Azure 服务总线后，请继续进行下一步：

> [!div class="nextstepaction"]
> [如何触发 Azure Functions](howto-trigger-azure-functions.md)
