---
title: 通用接口 - IoT 即插即用预览 |微软文档
description: 物联网即插即用开发人员通用接口的说明
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770493"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 即插即用预览通用界面

所有 IoT 即插即用设备都有望实现一些通用接口。 通用接口使 IoT 解决方案受益，因为它们提供了一致的功能。 [认证](tutorial-build-device-certification.md)要求您的设备实现多个通用接口。 可以从公共模型存储库检索通用接口定义。

## <a name="summary-of-common-interfaces"></a>常见接口摘要

| 名称 | ID | 说明 | 由 Azure IoT SDK 实现 | 必须在功能模型中声明 |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 模型信息 | urn：azureiot：模型发现：模型信息：1 | 供设备声明功能型号 ID 和接口。 所有 IoT 即插即用设备都需要。 | 是 | 否 |
| 数字双客户端 SDK 信息 | urn：azureiot：客户端：SDK信息：1 | 用于将设备与 Azure 连接的客户端 SDK。 [认证](tutorial-build-device-certification.md)所需的 | 是 | 否 |
| 设备信息 | urn：azureiot：设备管理：设备信息：1 | 有关设备的硬件和操作系统信息。 [认证](tutorial-build-device-certification.md)所需的 | 否 | 是 |
| 模型定义 | urn：azureiot：模型发现：模型定义：1 | 供设备声明其功能模型和接口的完整定义。 当模型定义未托管在模型存储库中时，必须实现。 | 否 | 是 |
| 数字孪生 | urn：azureiot：模型发现：数字孪生：1 | 供解决方案开发人员检索数字孪生的功能模型 ID 和接口 ID。 此接口不是由 IoT 即插即用设备声明或实现的。 | 否 | 否 |

- 由 Azure IoT SDK 实现 - Azure IoT SDK 是否实现在接口中声明的功能。 使用 Azure IoT SDK 的 IoT 即插即用设备不需要实现此接口。
- 必须在功能模型中声明 - 如果为"是"，则必须在此 IoT`"implements":`即插即用设备的设备功能模型部分中声明此接口。

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>从公共存储库检索接口定义

### <a name="cli"></a>CLI

可以使用 Azure CLI 的 Azure IoT 扩展从公共模型存储库检索公共接口。

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入**即插即用**，然后选择**IoT 即插即用：打开模型存储库**命令。 选择**公共存储库**。 公共模型存储库将在 VS Code 中打开。

1. 在公共模型存储库中，在搜索字段中输入接口名称。

1. 要创建界面的本地副本，请在搜索结果中选择它，然后选择 **"下载**"。

## <a name="next-steps"></a>后续步骤

现在，您已经了解了通用接口，下面是一些其他资源：

- [数字双定义语言 （DTDL）](https://aka.ms/DTDL)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [物联网 REST API](https://docs.microsoft.com/rest/api/iothub/device)
