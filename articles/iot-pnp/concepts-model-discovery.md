---
title: 在解决方案中使用 IoT 即插即用模型 |Microsoft Docs
description: 作为解决方案生成器，可以了解如何在 IoT 解决方案中使用 IoT 即插即用模型。
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 165f83e0f021d23c26333a294ffe992838bda6b0
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577707"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>在 IoT 解决方案中使用 IoT 即插即用模型

本文介绍如何在 IoT 解决方案中标识 IoT 即插即用设备的模型 ID，然后检索其模型定义。

IoT 解决方案分为两大类别：

- *专门构建的解决方案*适用于将连接到解决方案的 IoT 即插即用设备的一组已知模型。 在开发解决方案时，可以使用这些模型。

- *模型驱动*的解决方案可与任何 IoT 即插即用设备的型号一起使用。 构建模型驱动的解决方案更为复杂，但优点在于，您的解决方案适用于将来可能添加的任何设备。 模型驱动的 IoT 解决方案检索模型，并使用它来确定设备实现的遥测、属性和命令。

若要使用 IoT 即插即用模型，IoT 解决方案：

1. 标识由 IoT 即插即用设备、模块或连接到解决方案的 IoT Edge 模块实现的模型的模型 ID。

1. 使用模型 ID 从模型存储库或自定义存储中检索已连接设备的模型定义。

## <a name="identify-model-id"></a>标识模型 ID

当 IoT 即插即用设备连接到 IoT 中心时，它会将其实现的模型的模型 ID 注册到 IoT 中心。

IoT 中心将设备型号 ID 作为设备连接流的一部分，通知解决方案。

解决方案可以使用以下三种方法之一获取 IoT 即插即用设备的模型 ID：

### <a name="get-device-twin-api"></a>获取设备克隆 API

解决方案可以使用 [获取设备](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable&preserve-view=true) 克隆 API 来检索 IoT 即插即用设备的型号 ID。

> [!TIP]
> 对于模块和 IoT Edge 模块，请使用 [getTwin](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin?view=azure-java-stable&preserve-view=true)。

在以下设备克隆的响应代码段中， `modelId` 包含 IoT 即插即用设备的型号 ID：

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

### <a name="get-digital-twin-api"></a>获取数字克隆 API

解决方案可以使用 " [获取数字](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) 克隆 API" 来检索 IoT 即插即用设备实现的模型的模型 ID。

在下面的数字克隆响应代码段中， `$metadata.$model` 包含 IoT 即插即用设备的型号 ID：

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

### <a name="digital-twin-change-event-notification"></a>数字克隆更改事件通知

设备连接会导致数字克隆 [更改事件](concepts-digital-twin.md#digital-twin-change-events) 通知。 解决方案需要订阅此事件通知。 若要了解如何启用针对数字克隆事件的路由，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

解决方案可以使用以下代码片段中所示的事件了解连接的 IoT 即插即用设备并获取其型号 ID：

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

## <a name="retrieve-a-model-definition"></a>检索模型定义

解决方案使用上面标识的模型 ID 检索相应的模型定义。

解决方案可以通过使用下列选项之一来获取模型定义：

### <a name="model-repository"></a>模型存储库

解决方案可以使用 [模型存储库](concepts-model-repository.md) 来检索模型。 设备构建者或解决方案构建者必须事先将其模型上传到存储库，这样解决方案才能检索它们。

确定新设备连接的模型 ID 后，请执行以下步骤：

1. 使用模型存储库中的模型 ID 检索模型定义。 有关详细信息，请参阅 [获取模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)。

1. 使用已连接设备的模型定义，可以枚举设备的功能。

1. 使用设备的枚举功能，你可以允许用户与 [设备交互](quickstart-service-node.md)。

### <a name="custom-store"></a>自定义存储

解决方案可以将这些模型定义存储在本地文件系统、公共文件存储区中，也可以使用自定义实现。

确定新设备连接的模型 ID 后，请执行以下步骤：

1. 使用自定义存储区中的模型 ID 检索模型定义。

1. 使用已连接设备的模型定义，可以枚举设备的功能。 

1. 使用设备的枚举功能，你可以允许用户与 [设备交互](quickstart-service-node.md)。  

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 IoT 解决方案中集成 IoT 即插即用模型，以下是一些建议的后续步骤：

- [与解决方案中的设备交互](quickstart-service-node.md)
- [IoT 数字克隆 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](howto-use-iot-explorer.md)
