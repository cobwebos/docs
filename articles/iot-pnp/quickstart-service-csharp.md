---
title: 与已连接到 Azure IoT 解决方案的 IoT 即插即用设备交互 (C#) | Microsoft Docs
description: 使用 C# 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用设备并与之交互。
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b35268cd8d36901f750225713407c5392e5c429e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759163"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>快速入门：与已连接到解决方案的 IoT 即插即用设备交互 (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 即插即用简化了 IoT 的使用，使你无需了解底层设备实现，就能与某个设备的功能交互。 本快速入门介绍如何使用 C# 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成本快速入门教程，需要在开发计算机上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用示例代码克隆 SDK 存储库

如果已完成[快速入门：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (C#)](quickstart-connect-device-csharp.md)，则已克隆了存储库。

从用于 .NET 的 Microsoft Azure IoT SDK GitHub 存储库克隆示例。 在所选文件夹中打开命令提示符。 运行以下命令，以克隆[适用于 .NET 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，你将使用一个以 C# 编写的示例恒温器设备作为 IoT 即插即用设备。 运行示例设备：

1. 在 Visual Studio 2019 中打开“azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj”项目文件。

1. 在 Visual Studio 中，导航到“项目”>“恒温器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 完成[设置环境](set-up-environment.md)时记下的值 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 完成[设置环境](set-up-environment.md)时记下的值 |


1. 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请勿关闭此 Visual Studio 实例，你需要使用它确认服务示例正常工作。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在[为 IoT 即插即用快速入门和教程设置环境](set-up-environment.md)中，已创建了两个环境变量以将示例配置为连接到 IoT 中心和设备：

* **IOTHUB_CONNECTION_STRING**：之前记下的 IoT 中心连接字符串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入门中，你将使用以 C# 编写的示例 IoT 解决方案与刚设置的示例设备进行交互。

1. 在 Visual Studio 的另一个实例中，打开“azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj”项目。

1. 在 Visual Studio 中，导航到“项目”>“恒温器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | 完成[设置环境](set-up-environment.md)时记下的值 |

1. 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

### <a name="get-digital-twin"></a>获取数字孪生

以下代码片段演示了服务应用程序如何检索数字孪生体：

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> 此示例使用来自 IoT 中心服务客户端的“Microsoft.Azure.Devices.Client;”命名空间。 若要详细了解如何检索模型 ID，请参阅[开发人员指南](concepts-developer-guide-service.md)。

此代码生成以下输出：

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

以下代码片段演示了如何使用补丁通过数字孪生体来更新属性：

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

当服务更新 `targetTemperature` 属性时，此代码将从设备生成以下输出：

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>调用命令

下面的代码片段演示如何调用命令：

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

当服务调用 `getMaxMinReport` 命令时，此代码将从设备生成以下输出：

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用建模开发人员指南](concepts-developer-guide-device-csharp.md)
