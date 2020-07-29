---
title: 实现 IoT 即插即用预览模型发现 |Microsoft Docs
description: 作为解决方案生成器，可以了解如何在解决方案中实现 IoT 即插即用模型发现。
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337375"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>在 IoT 解决方案中实现 IoT 即插即用预览模型发现

本文介绍了如何作为解决方案生成器在 IoT 解决方案中实现 IoT 即插即用预览模型发现。 模型发现介绍了如何：

- IoT 即插即用设备注册其模型 ID。
- IoT 解决方案检索设备实现的接口。

IoT 解决方案分为两大类别：

- *专门构建的 iot 解决方案*适用于一组已知的 iot 即插即用设备型号。

- *模型驱动的 iot 解决方案*可与任何 IoT 即插即用设备配合使用。 构建模型驱动的解决方案更为复杂，但优点在于您的解决方案适用于将来添加的任何设备。

    若要生成模型驱动的 IoT 解决方案，需要根据 IoT 即插即用接口基元构建逻辑：遥测、属性和命令。 解决方案的逻辑通过合并多个遥测、属性和命令功能来表示设备。

本文介绍如何在这两种类型的解决方案中实现模型发现。

## <a name="model-discovery"></a>模型发现

若要发现设备实现的模型，解决方案可以通过使用基于事件的发现或基于克隆的发现来获取模型 ID：

### <a name="event-based-discovery"></a>基于事件的发现

当 IoT 即插即用设备连接到 IoT 中心时，它将注册其实现的模型。 此注册将导致[数字克隆更改事件](concepts-digital-twin.md#digital-twin-change-events)通知。 若要了解如何启用针对数字克隆事件的路由，请参阅[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

解决方案可以使用以下代码片段中所示的事件来了解正在连接的 IoT 即插即用设备并获取其型号 ID：

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

添加或更新设备模型 ID 时，触发此事件。

### <a name="twin-based-discovery"></a>基于克隆的发现

如果解决方案想要了解给定设备的功能，则可以使用[获取数字](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin)克隆 API 来检索信息。

在以下数字设备代码段中， `$metadata.$model` 包含 IoT 即插即用设备的型号 ID：

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

此解决方案还可以使用 "**获取**克隆" 从设备克隆检索模型 ID，如以下代码片段所示：

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>模型解析

解决方案使用模型解析获取从模型 ID 中构成模型的接口的访问权限。 

- 解决方案可以选择将这些接口作为文件存储在本地文件夹中。 
- 解决方案可以使用[模型存储库](concepts-model-repository.md)。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关使用 IoT 解决方案的模型发现的详细信息，请详细了解[Azure iot 平台](overview-iot-plug-and-play.md)，以使用解决方案的其他功能。

- [从解决方案与设备交互](quickstart-service-node.md)
- [IoT 数字克隆 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](howto-use-iot-explorer.md)
