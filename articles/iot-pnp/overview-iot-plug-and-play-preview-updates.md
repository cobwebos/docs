---
title: 新增功能 IoT 即插即用预览刷新版本 | Microsoft Docs
description: 了解 IoT 即插即用预览刷新版本的新增功能。
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c415ffdaa2eb3ad6a76cd48c3a895b6618dd3986
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208064"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT 即插即用预览刷新版本

本文介绍了 2020 年 7 月 IoT 即插即用预览刷新版本中 SDKS、库、工具和服务中的关键更改。 上一个 IoT 即插即用预览版本发布于 2019 年 8 月。

## <a name="digital-twins-definition-language-dtdl"></a>数字孪生定义语言 (DTDL)

此版本增加了对 [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) 的支持，并弃用了 [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview)。

下表显示了 DTDL v1 和 DTDL v2 之间的主要区别。 在 DTDL v2 中：

- 模型 ID 的前缀为 `dtmi`，而不是 `urn`。 数字孪生模型标识符 (DTMI) 替换了 DTDL v1 中使用的以 `urn` 为前缀的数字孪生 ID。 目前，该版本的前缀为 `;`。 例如，以前使用 `urn:CompanyName:Model:1`，现在使用 `dtmi:CompanyName:Model;1`。
- 将 `@context` 设置为 `dtmi:dtdl:context;2`，而不是 `http://azureiot.com/v1/contexts/IoTModel.json`。
- 使用“接口”类型（而不是 CapabilityModel 类型）对设备建模 。
- “组件”替换“接口”实例 。 可以将“关系”和“组件”定义为“接口”内容的一部分  。
- 接口可以从其他接口继承 。
- 可以使用可扩展语义类型扩展 DTDL。 相较于 DTDL v1 中的温度和湿度等硬编码语义类型，该可扩展类型系统具有更大的灵活性。
- 已删除 displayUnit 属性。
- 不能在名称中使用前置或后置下划线。 保留名称中的前置下划线，供系统使用。

若要使用 DTDL v1，需要使用早期版本的 SDK 和 Azure IoT 浏览器 0.10.x。 若要使用 DTDL v2，需要使用最新版本的 SDK 和 Azure IoT 浏览器 0.11.x。

## <a name="no-component-and-multiple-component-implementations"></a>无组件和多组件实现

可以在单一接口中描述使用少量遥测、命令或属性的简单设备，而无需使用组件。 通过公布 ID 模型，任何现有设备都可以转变为 IoT 即插即用，而无需对现有设备实现进行任何更改。

较复杂的设备可能会在不同的接口上对遥测、命令和属性进行分组，以管理复杂性并实现跨设备重用。 必须更新这些设备，以遵循 [IoT 即插即用预览消息约定](concepts-convention.md)中定义的一系列简单规则。

## <a name="registration-and-discovery"></a>注册和发现

在此版本中，设备公布其模型 ID 以及每个连接上的 IoT 中心。 IoT 中心缓存模型 ID，从而使后端解决方案能够使用设备孪生 `modelId` 属性检索模型 ID 。 此外，可以从数字孪生中的 `$metadata.$model` 检索模型 ID。

## <a name="microsoft-defined-interfaces"></a>Microsoft 定义的接口

Microsoft 定义的以下接口已不再推荐使用，并且不会在新的模型存储库中发布：

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

新的模型存储库中发布了以下接口：URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview) 中可用的 `dtmi:azure:DeviceManagement:DeviceInformation;1` 。

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

DigitalTwinChangeEvents [事件源](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)的事件结构已更改为使用 JSON-Patch 格式 。 此更改是一项中断性变更，不具有后向兼容性支持。

## <a name="message-routing"></a>消息路由

遥测消息在 [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) 集合中进行了以下更改。

它现在包含 dt-dataschema 属性，该属性是设备注册的模型 ID 。

dt-subject 属性表示发送遥测消息的组件。

iothub-interface-name 属性已被弃用。

## <a name="device-and-service-sdks"></a>设备和服务 SDK

早期预览版 SDK 不具有后向兼容性。 如果改用最新预览版 SDK，则需要更改代码。

采用基于约定的方法，则无需使用独立的设备客户端 SDK。 从此预览版本开始，弃用所有语言的现有 DigitalTwinClient 库。 已更新 IoT 中心设备客户端 SDK，加入了用于公布模型 ID 的选项。

不使用组件的设备只需最小程度地更改代码 - 只需公布模型 ID。 使用多个组件的较复杂设备可能需要使用一些可重用的函数来实现[约定](concepts-convention.md)。 设备示例包含一系列可以在设备实现中重用的函数。

### <a name="service-sdks"></a>服务 SDK

服务 SDK 在 [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) 和 [Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/README.md) 中可用。

## <a name="vs-code-extension"></a>VS Code 扩展

[Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 扩展提供了对 DTDL v1 的创作支持、与早期版本的模型存储库的集成以及代码生成。

如果在 VS Code 中需要 DTDL v2 创作支持，请在 VS Code 中安装新的 [DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。 此扩展不提供与模型存储库的集成或代码生成。 现在可以使用 [Web UI](https://aka.ms/iotmodelrepo) 或 [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest) 管理存储库中的模型。

## <a name="digital-twin-service-side-rest-apis"></a>数字孪生服务端 REST API

已更改[数字孪生服务端 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) 和有效负载。 受支持的 API 包括：

- 检索数字孪生。
- 调用命令。
- 使用 JSON-Patch 有效负载更新数字孪生。

此版本将继续支持现有的 REST API。

## <a name="model-repository"></a>模型存储库

现在有一个模型存储库，其中包含公开发布的模型和受 RBAC 保护的企业专用模型。 所有模型都具有唯一的标识符，且创建后不可改变。

此版本不支持早期版本中的现有企业模型存储库。 可以继续使用 [Azure IoT 认证](https://preview.catalog.azureiotsolutions.com/products)网站管理旧的 DTDL v1 模型。 但不可以再使用该网站注册、测试和认证设备。

## <a name="azure-iot-central"></a>Azure IoT Central

目前正在更新 Azure IoT Central，以支持 IoT 即插即用预览刷新版。
