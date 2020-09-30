---
title: 服务开发人员指南-IoT 即插即用 |Microsoft Docs
description: 面向服务开发人员的 IoT 即插即用说明
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7728af831b26bff19f347e5b85db6420e7966ed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579754"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 即插即用服务开发人员指南

IoT 即插即用允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

IoT 即插即用允许你使用已通过 IoT 中心宣布其模型 ID 的设备。 例如，你可以直接访问设备的属性和命令。

若要使用已连接到 IoT 中心的 IoT 即插即用设备，请使用其中一个 IoT 服务 Sdk 或 IoT 中心 REST API：

## <a name="service-sdks"></a>服务 SDK

使用解决方案中的 Azure IoT 服务 Sdk 与设备和模块交互。 例如，你可以使用服务 Sdk 来读取和更新克隆属性和调用命令。 支持的语言包括 c #、Java、Node.js 和 Python。

服务 Sdk 允许您从解决方案（如桌面或 web 应用程序）访问设备信息。 服务 Sdk 包括两个可用于检索模型 ID 的命名空间和对象模型：

- Iot 中心服务客户端。
- 数字孪生服务客户端。

| 语言 | IoT 中心服务客户端 | 数字孪生服务客户端 |
| -------- | ---------------------- | ---------------------------- |
| C#       | [文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> 示例 | 示例  |
| Java     | [文档](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> 示例 | 示例  |
| Node.js  | [文档](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> 示例 | [文档](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) |
| Python   | [文档](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> 示例 | [文档](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) | 

## <a name="rest-api"></a>REST API

以下示例使用 IoT 中心 REST API 与连接 IoT 即插即用设备交互。 该 API 的当前版本为 `2020-09-30` 。 追加 `?api-version=2020-05-31` 到 REST PI 调用。

> [!NOTE]
> API 目前不支持 Module 孪生 `digitalTwins` 。

如果调用恒温器设备 `t-123` ，你将获得设备使用 REST API get 调用实现的所有接口的所有属性：

```REST
GET /digitalTwins/t-123
```

此调用将包含 `$metadata.$model` 包含设备所公布的模型 ID 的 Json 属性。

所有接口上的所有属性都是通过 `GET /DigitalTwin/{device-id}` REST API 模板访问的，其中 `{device-id}` ，是设备的标识符：

```REST
GET /digitalTwins/{device-id}
```

可以直接调用 IoT 即插即用设备命令。 如果 `Thermostat` 设备中的组件 `t-123` 有 `restart` 命令，则可以使用 REST API POST 调用来调用它：

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

通常，可以通过此 REST API 模板调用命令：

- `device-id`：设备的标识符。
- `component-name`：设备功能模型的实现部分中的接口的名称。
- `command-name`：命令的名称。

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
- [安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)
