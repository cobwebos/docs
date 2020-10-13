---
title: 监视模块孪生 - Azure IoT Edge
description: 如何解释设备孪生和模块孪生，以确定连接性和运行状况。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a11d3a9a972188af4cf8f054349da98d69691a3
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876152"
---
# <a name="monitor-module-twins"></a>监视模块孪生

Azure IoT 中心中的模块孪生可以监视 IoT Edge 部署的连接性和运行状况。 模块孪生用于存储 IoT 中心中的有用信息，这些信息与运行的模块的性能有关。 [IoT Edge 代理](iot-edge-runtime.md#iot-edge-agent)和 [IoT Edge 中心](iot-edge-runtime.md#iot-edge-hub)运行时模块分别维护它们的模块孪生 `$edgeAgent` 和 `$edgeHub`：

* `$edgeAgent` 包含有关 IoT Edge 代理和 IoT Edge 中心运行时模块以及自定义模块的运行状况和连接数据。 IoT Edge 代理负责部署模块，对其进行监视并向 Azure IoT 中心报告连接状态。
* `$edgeHub` 包含有关在设备上运行的 IoT Edge 中心与 Azure IoT 中心之间的通信数据。 这包括处理来自下游设备的传入消息。 IoT Edge 中心负责处理 Azure IoT 中心与 IoT Edge 设备和模块之间的通信。

数据被组织为元数据、标记以及模块孪生的 JSON 结构中所需的和报告的属性集。 在 deployment.json 文件中指定的所需属性将被复制到模块孪生。 IoT Edge 代理和 IoT Edge 中心各自更新其模块的报告属性。

同样，deployment.json 中为自定义模块所指定的所需属性会复制到其模块孪生中，但你的解决方案负责提供其报告的属性值。

本文介绍如何在 Azure 门户、Azure CLI 和 Visual Studio Code 中查看模块孪生。 有关监视设备接收部署的方式的信息，请参阅[监视 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。 有关模块孪生概念的概述，请参阅[在 IoT 中心内了解并使用模块孪生](../iot-hub/iot-hub-devguide-module-twins.md)。

> [!TIP]
> 如果 IoT Edge 设备与其 IoT 中心断开连接，则运行时模块的报告的属性可能会过时。 可以对 `$edgeAgent` 模块使用 [ping](how-to-edgeagent-direct-method.md#ping) 方法来确定连接是否丢失。

## <a name="monitor-runtime-module-twins"></a>监视运行时模块孪生

若要排查部署连接性问题，请查看 IoT Edge 代理和 IoT Edge 中心运行时模块孪生，然后深入到其他模块。

### <a name="monitor-iot-edge-agent-module-twin"></a>监视 IoT Edge 代理模块孪生

以下 JSON 显示了 Visual Studio Code 中的 `$edgeAgent` 模块孪生，其中大部分 JSON 部分已折叠。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

从顶部开始，可在以下部分中描述 JSON：

* Metadata - 包含连接性数据。 有趣的是，IoT Edge 代理的连接状态始终为断开连接状态：`"connectionState": "Disconnected"`。 这是因为连接状态与设备到云 (D2C) 消息有关，且 IoT Edge 代理不发送 D2C 消息。
* Properties - 包含 `desired` 和 `reported` 子节。
* Properties.desired -（显示为已折叠）运算符在 deployment.json 文件中设置的期望属性值。
* Properties.reported - IoT Edge 代理报告的最新属性值。

`properties.desired` 和 `properties.reported` 部分都具有类似的结构，且都包含架构、版本和运行时信息的其他元数据。 此外，任何自定义模块（如 `SimulatedTemperatureSensor`）的 `modules` 部分以及 `$edgeAgent` 和 `$edgeHub` 运行时模块的 `systemModules` 部分也包含在内。

通过将报告的属性值与所需值进行比较，可以确定差异并标识断开连接，以帮助排查问题。 执行这些比较时，请检查所调查属性的 `metadata` 部分中 `$lastUpdated` 报告的值。

若要进行故障排除，则检查以下属性很有必要：

* **exitcode** - 零以外的任何其他值都表示模块因失败而停止。 但是，如果有意将模块设置为已停止状态，则使用错误代码 137 或 143。

* **lastStartTimeUtc** - 显示上次启动容器的日期/时间。 如果未启动容器，则此值为 0001-01-01T00:00:00Z。

* **lastExitTimeUtc** - 显示容器最近完成的日期/时间。 如果容器正在运行且从未停止过，则此值为 0001-01-01T00:00:00Z。

* **runtimeStatus** - 可以是以下值之一：

    | Value | 说明 |
    | --- | --- |
    | 未知 | 默认状态，直到部署创建完成。 |
    | 回退 | 该模块已计划启动，但当前未运行。 此值对于在重启时经历状态更改的模块很有用。 当失败模块在冷却期间等待重启时，该模块将处于回退状态。 |
    | “正在运行” | 指示模块当前正在运行。 |
    | 不正常 | 指示运行状况探测检查失败或超时。 |
    | 已停止 | 指示已成功退出该模块（退出代码为零）。 |
    | “失败” | 指示已退出该模块，并显示失败退出代码（非零）。 根据生效的重启策略，模块可以从此状态转换回回退状态。 此状态可能表示该模块发生了不可恢复的错误。 如果 Microsoft Monitoring Agent (MMA) 无法再恢复模块，则会发生失败，并需要新的部署。 |

有关详细信息，请参阅 [EdgeAgent 报告的属性](module-edgeagent-edgehub.md#edgeagent-reported-properties)。

### <a name="monitor-iot-edge-hub-module-twin"></a>监视 IoT Edge 中心模块孪生

以下 JSON 显示了 Visual Studio Code 中的 `$edgeHub` 模块孪生，其中大部分 JSON 部分已折叠。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

从顶部开始，可在以下部分中描述 JSON：

* Metadata - 包含连接性数据。

* Properties - 包含 `desired` 和 `reported` 子节。
* Properties.desired -（显示为已折叠）运算符在 deployment.json 文件中设置的期望属性值。
* Properties.reported - IoT Edge 中心报告的最新属性值。

如果你的下游设备遇到问题，则检查此数据是一个不错的开端。

## <a name="monitor-custom-module-twins"></a>监视自定义模块孪生

有关自定义模块连接性的信息在 IoT Edge 代理模块孪生中进行维护。 自定义模块的模块孪生主要用于维护解决方案的数据。 你在 deployment.json 文件中定义的所需属性将反映在模块孪生中，模块可根据需要更新报告的属性值。

可以将首选的编程语言与 [Azure IoT 中心设备 SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 结合使用，以基于模块的应用程序代码来更新模块孪生中报告的属性值。 以下过程使用适用于 .NET 的 Azure SDK 完成此操作，方法是使用来自 [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) 模块的代码：

1. 使用 [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) 方法创建 [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) 的实例。

1. 使用 [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) 方法获取模块克隆属性的集合。

1. 使用 [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) 方法创建侦听程序（用于传递回调）来捕获对所需属性的更改。

1. 在回调方法中，使用 [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) 方法更新模块孪生中报告的属性，传递要设置的 [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) 属性值。

## <a name="access-the-module-twins"></a>访问模块孪生

可在 Azure IoT 中心和 Visual Studio Code 中以及使用 Azure CLI 查看模块孪生的 JSON。

### <a name="monitor-in-azure-iot-hub"></a>在 Azure IoT 中心中进行监视

查看模块孪生的 JSON，方法如下：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从左窗格菜单中选择“IoT Edge”。
1. 在“IoT Edge 设备”选项卡上，选择包含要监视模块的设备的“设备 ID” 。
1. 从“模块”选项卡中选择模块名称，然后从上部菜单栏中选择“模块标识孪生” 。

  ![选择要在 Azure 门户中查看的模块孪生](./media/how-to-monitor-module-twins/select-module-twin.png)

如果看到消息“此模块不存在模块标识”，则此错误表示最初创建该标识的后端解决方案不再可用。

### <a name="monitor-module-twins-in-visual-studio-code"></a>在 Visual Studio Code 中监视模块孪生

查看并编辑模块孪生，方法如下：

1. 安装适用于 Visual Studio Code 的 [Azure IoT 工具扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)（如果尚未安装）。
1. 在资源管理器中，展开“Azure IoT 中心”，然后使用要监视的模块来展开设备 。
1. 右键单击该模块，然后选择“编辑模块孪生”。 模块孪生的临时文件会下载到计算机并显示在 Visual Studio Code 中。

  ![获取模块孪生以在 Visual Studio Code 中进行编辑](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

如果进行了更改，请在编辑器中的代码上方选择“更新模块孪生”，以将更改保存到 IoT 中心。

  ![在 Visual Studio Code 中更新模块孪生](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>在 Azure CLI 中监视模块孪生

若要查看 IoT Edge 是否正在运行，请使用 [az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping) 来对 IoT Edge 代理使用 ping 方法。

[az iot hub module-twin](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin) 结构提供以下命令：

* **az iot hub module-twin show** - 显示模块孪生定义。
* **az iot hub module-twin update** - 更新模块孪生定义。
* **az iot hub module-twin replace** - 将模块孪生定义替换为目标 JSON。

## <a name="next-steps"></a>后续步骤

了解如何[使用内置直接方法与 EdgeAgent 通信](how-to-edgeagent-direct-method.md)。
