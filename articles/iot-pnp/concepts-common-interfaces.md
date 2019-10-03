---
title: 常见接口-IoT 即插即用预览版 |Microsoft Docs
description: 适用于 IoT 即插即用开发人员的常见界面的说明
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2eae778230fa5fce1be095106a02b2b643ff436e
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935326"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 即插即用预览通用接口

所有 IoT 即插即用设备都应该实现一些公共接口。 常见接口会使 IoT 解决方案受益，因为它们提供了一致的功能。 [证书](tutorial-build-device-certification.md)要求设备实现多个通用接口。 你可以从公共模型存储库检索公共接口定义。

## <a name="summary-of-common-interfaces"></a>公共接口摘要

| 姓名 | id | 描述 | 由 Azure IoT SDK 实现 | 必须在功能模型中声明 |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 模型信息 | urn： azureiot： ModelDiscovery： ModelInformation：1 | 用于设备声明功能模型 ID 和接口。 所有 IoT 即插即用设备都是必需的。 | 是 | 否 |
| 数字克隆客户端 SDK 信息 | urn： azureiot：客户端： SDKInformation：1 | 用于将设备连接到 Azure 的客户端 SDK。 [认证](tutorial-build-device-certification.md)所必需 | 是 | 否 |
| 设备信息 | urn： azureiot：设备： DeviceInformation：1 | 有关设备的硬件和操作系统信息。 [认证](tutorial-build-device-certification.md)所必需 | 否 | 是 |
| 模型定义 | urn： azureiot： ModelDiscovery： ModelDefinition：1 | 设备用于声明其功能模型和接口的完整定义。 当模型定义不托管在模型存储库中时，必须实现。 | 否 | 是 |
| 数字输出 | urn： azureiot： ModelDiscovery： DigitalTwin：1 | 供解决方案开发人员检索数字克隆的功能模型 ID 和接口 Id。 IoT 即插即用设备不声明或实现此接口。 | 否 | 否 |

- 由 Azure IoT SDK 实现-Azure IoT SDK 是否实现了在接口中声明的功能。 使用 Azure IoT SDK 的 IoT 即插即用设备无需实现此接口。
- 必须在功能模型中声明-如果是 "yes"，则必须在此 IoT 即插即用`"implements":`设备的设备功能模型的部分中声明此接口。

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>从公共存储库检索接口定义

### <a name="cli"></a>CLI

可以使用适用于 Azure CLI 的 Azure IoT 扩展从公共模型存储库检索公共接口。

```cmd/sh
az iot pnp interface show --interface {InterfaceID}
```

```cmd/sh
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入**即插即用**，然后选择**IoT 即插即用：** 打开模型存储库”命令。 选择 "**公共存储库**"。 公共模型存储库将在 VS Code 中打开。

1. 在 "公共模型存储库" 中，在搜索字段中输入接口名称。

1. 若要创建接口的本地副本，请在搜索结果中选择它，然后选择 "**下载**"。

## <a name="next-steps"></a>后续步骤

现在，你已了解常见接口，以下是一些其他资源：

- [数字克隆定义语言（DTDL）](https://aka.ms/DTDL)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
