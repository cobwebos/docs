---
title: 监视模块孪生-Azure IoT Edge
description: 如何解释设备孪生和模块孪生，以确定连接和运行状况。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c24cef2cf9e4c54d16ebc75eb1a56273d8826355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221118"
---
# <a name="monitor-module-twins"></a>监视模块孪生

使用 Azure IoT 中心的模块孪生，可以监视 IoT Edge 部署的连接和运行状况。 模块孪生在 IoT 中心存储有关正在运行的模块的性能的有用信息。 每个[IoT Edge 代理](iot-edge-runtime.md#iot-edge-agent)和[IoT Edge 中心](iot-edge-runtime.md#iot-edge-hub)运行时模块分别维护它们的模块孪生 `$edgeAgent` 和 `$edgeHub` ：

* `$edgeAgent`包含有关 IoT Edge 代理和 IoT Edge 中心运行时模块以及自定义模块的运行状况和连接数据。 IoT Edge 代理负责部署模块，对其进行监视并向 Azure IoT 中心报告连接状态。
* `$edgeHub`包含有关在设备上运行的 IoT Edge 中心与 Azure IoT 中心之间的通信的数据。 这包括处理来自下游设备的传入消息。 IoT Edge 中心负责处理 Azure IoT 中心与 IoT Edge 设备和模块之间的通信。

数据组织为元数据、标记以及模块孪生的 JSON 结构中所需和报告的属性集。 在 deployment.js的文件中指定的所需属性将复制到模块孪生。 IoT Edge 代理和 IoT Edge 中心每个更新其模块的报告属性。

同样，在文件的 deployment.js中为自定义模块指定的所需属性将复制到其模块副本，但你的解决方案负责提供其报告的属性值。

本文介绍如何在 Visual Studio Code 中查看 Azure 门户、Azure CLI 和中的模块孪生。 有关监视设备接收部署的方式的信息，请参阅[监视 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。 有关 module 孪生概念的概述，请参阅[了解和使用 IoT 中心中的模块孪生](../iot-hub/iot-hub-devguide-module-twins.md)。

> [!TIP]
> 如果 IoT Edge 设备与其 IoT 中心断开连接，则运行时模块的报告属性可能会过时。 你可以[ping](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` 对模块进行 ping 操作，以确定连接是否丢失。

## <a name="monitor-runtime-module-twins"></a>监视运行时模块孪生

若要解决部署连接问题，请查看 IoT Edge 代理并 IoT Edge 中心运行时模块孪生，然后向下钻取到其他模块。

### <a name="monitor-iot-edge-agent-module-twin"></a>监视 IoT Edge 代理模块克隆

下面的 JSON 显示了 `$edgeAgent` 中的模块克隆，其中大部分 JSON 部分已折叠 Visual Studio Code。

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

* Metadata-包含连接数据。 有趣的是，IoT Edge 代理的连接状态始终处于断开连接状态： `"connectionState": "Disconnected"` 。 这是因为连接状态与设备到云（D2C）消息相关并且 IoT Edge 代理不发送 D2C 消息。
* Properties-包含 `desired` 和子 `reported` 节。
* Properties。所需的属性值（已折叠）所需的属性值由 deployment.json file。
* Properties。报告-IoT Edge 代理报告的最新属性值。

`properties.desired`和 `properties.reported` 部分都具有类似的结构，并包含架构、版本和运行时信息的其他元数据。 还包括 `modules` 所有自定义模块（如）的部分 `SimulatedTemperatureSensor` ，以及 `systemModules` `$edgeAgent` 和 `$edgeHub` 运行时模块的部分。

通过将报告的属性值与所需的值进行比较，可以确定矛盾并确定可帮助解决问题的断开。 在执行这些比较时，请在要 `$lastUpdated` `metadata` 调查的属性部分检查报告的值。

以下属性对于检查故障排除很重要：

* **exitcode** -零以外的任何值都表示该模块因失败而停止。 但是，如果模块被有意设置为 "已停止" 状态，则使用错误代码137或143。

* **lastStartTimeUtc** -显示上次启动容器的**日期时间**。 如果未启动容器，则此值为 0001-01-01T00：00：00Z。

* **lastExitTimeUtc** -显示容器的上次完成**日期时间**。 此值为 0001-01-01T00：00：00Z （如果容器正在运行且从未停止）。

* **runtimeStatus** -可以为下列值之一：

    | “值” | 描述 |
    | --- | --- |
    | 未知 | 默认状态，直到创建部署。 |
    | 回退 | 该模块计划为启动，但当前未运行。 此值适用于正在重新启动状态发生更改的模块。 当故障模块在冷却时间段内等待重新启动时，该模块将处于回退状态。 |
    | “正在运行” | 指示模块当前正在运行。 |
    | 不正常 | 指示运行状况探测检查失败或超时。 |
    | 已停止 | 指示已成功退出该模块（退出代码为零）。 |
    | 失败 | 指示模块退出，并显示失败退出代码（非零）。 根据有效的 "重新启动" 策略，该模块可从该状态过渡回回退。 此状态可能表示该模块发生了不可恢复的错误。 如果 Microsoft Monitoring Agent （MMA）无法再 resuscitate 模块，则会发生故障，需要新的部署。 |

有关详细信息，请参阅[EdgeAgent 报告的属性](module-edgeagent-edgehub.md#edgeagent-reported-properties)。

### <a name="monitor-iot-edge-hub-module-twin"></a>监视 IoT Edge 中心模块克隆

下面的 JSON 显示了 `$edgeHub` 中的模块克隆，其中大部分 JSON 部分已折叠 Visual Studio Code。

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

* Metadata-包含连接数据。

* Properties-包含 `desired` 和子 `reported` 节。
* Properties。所需的属性值（已折叠）所需的属性值由 deployment.json file。
* Properties。报告-IoT Edge 集线器报告的最新属性值。

如果你的下游设备遇到问题，请检查此数据是一个不错的开端。

## <a name="monitor-custom-module-twins"></a>监视自定义模块孪生

有关自定义模块连接性的信息保留在 IoT Edge 代理模块克隆中。 自定义模块的模块克隆主要用于维护解决方案的数据。 在文件 deployment.js中定义的所需属性将反映在模块中，并可根据需要更新报告的属性值。

你可以将首选的编程语言与[Azure IoT 中心设备 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks)结合使用，以根据模块的应用程序代码来更新模块克隆中的报告属性值。 以下过程使用[SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)模块中的代码，通过 Azure SDK for .net 执行此操作：

1. 使用[CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)方法创建[ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient)的实例。

1. 使用[GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet)方法获取模块克隆属性的集合。

1. 使用[SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet)方法创建侦听器（传递回调）来捕获对所需属性的更改。

1. 在回调方法中，使用[transport.updatereportedpropertiesasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient)方法更新模块中的报告属性，并传递要设置的属性值的[TwinCollection](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) 。

## <a name="access-the-module-twins"></a>访问模块孪生

可以在 Azure IoT 中心的 Visual Studio Code 和 Azure CLI 中查看模块孪生的 JSON。

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT 中心中的监视器

查看模块克隆的 JSON：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从左窗格的菜单中选择“IoT Edge”。
1. 在 " **IoT Edge 设备**" 选项卡上，选择包含要监视的模块的设备的**设备 ID** 。
1. 从 "**模块**" 选项卡中选择模块名称，然后从上部菜单栏中选择 "**模块标识**"。

  ![选择要在 Azure 门户中查看的模块克隆](./media/how-to-monitor-module-twins/select-module-twin.png)

如果看到消息 "此模块不存在模块标识"，则此错误表示后端解决方案不再提供最初创建标识的。

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code 中的监视模块孪生

查看和编辑模块克隆：

1. 如果尚未安装，请安装适用于 Visual Studio Code 的[Azure IoT 工具扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
1. 在**资源管理器**中，展开**Azure IoT 中心**，然后展开包含要监视的模块的设备。
1. 右键单击该模块，然后选择 "**编辑模块**克隆"。 模块克隆的临时文件会下载到计算机并显示在 Visual Studio Code 中。

  ![获取要在其中进行编辑的模块克隆 Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

如果进行了更改，请在编辑器中的代码上方选择 "**更新模块**"，以将更改保存到 IoT 中心。

  ![更新中的模块克隆 Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLI 中的监视模块孪生

若要查看 IoT Edge 是否正在运行，请使用[az IoT 中心调用 module-方法](how-to-edgeagent-direct-method.md#ping)对 IoT Edge 代理进行 ping 操作。

[Az iot 中心模块](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin)克隆结构提供以下命令：

* **az iot 中心模块-克隆显示**-显示模块克隆定义。
* **az iot 中心模块-克隆更新**-更新模块克隆定义。
* **az iot 中心模块-双子回替换**-将模块克隆定义替换为目标 JSON。

## <a name="next-steps"></a>后续步骤

了解如何[使用内置直接方法与 EdgeAgent 通信](how-to-edgeagent-direct-method.md)。
