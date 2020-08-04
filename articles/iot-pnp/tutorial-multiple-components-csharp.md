---
title: 将 IoT 即插即用预览版示例 C# 组件设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成和运行使用了多个组件且连接到 IoT 中心的 IoT 即插即用预览版示例 C# 设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cf2662bef194bc2b7afdb07f55809ba63f40fd6e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351726"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>教程：将 Windows 上运行的 IoT 即插即用多组件设备应用程序连接到 IoT 中心 (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教程介绍如何使用组件和根接口生成 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 C# 编写，包含在用于 C# 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

要在本教程的第二部分中与示例设备进行交互，请使用“Azure IoT 资源管理器”工具。 [下载并安装适用于你的操作系统的最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 记下此连接字符串，在本教程的稍后部分使用它：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 还可以使用 Azure IoT 资源管理器工具查找 IoT 中心连接字符串。

运行以下命令，获取已添加到中心的设备的设备连接字符串。 记下此连接字符串，在本教程的稍后部分使用它：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>下载代码

在本教程中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C# SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT C# SDK 和库](https://github.com/Azure/azure-iot-sdk-csharp) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-c.git
```

## <a name="build-the-code"></a>生成代码

在 Visual Studio 2019 中打开“azureiot.sln”解决方案文件，并将 TemperatureController 项目设置为启动项项目。 可以在“解决方案资源管理器”的“iothub > 设备 > 示例”中找到此项目文件。

现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

## <a name="run-the-device-sample"></a>运行设备示例

创建一个名为“IOTHUB_DEVICE_CONNECTION_STRING”的环境变量，以存储你之前记下的设备连接字符串。

若要在 Windows 上的 Visual Studio 中跟踪代码执行，请在 program.cs 文件中向 `main` 函数添加一个断点。

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现的模型使用[多个组件](concepts-components.md)。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

设备代码使用标准 `CreateFromConnectionString` 方法连接到 IoT 中心。 设备发送在连接请求中实现的 DTDL 模型的模型 ID。 发送模型 ID 的设备是 IoT 即插即用设备：

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

模型 ID 存储在代码中，如以下代码片段所示：

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

设备连接到 IoT 中心后，代码将注册命令处理程序。 `reboot` 命令是在根接口中定义的。 `getMaxMinReport` 命令分别定义在两个恒温器组件中：

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

对于两个恒温器组件上的所需属性更新，有单独的处理程序：

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

示例代码从每个恒温器组件发送遥测数据：

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

`SendTemperature` 方法使用 `PnpHhelper` 类为每个组件创建消息：

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` 类包含可以在多组件模型中使用的其他示例方法。

使用 Azure IoT 资源管理器工具查看两个恒温器组件中的遥测数据和属性：

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT 资源管理器中的多组件设备":::

还可以使用 Azure IoT 资源管理器工具在两个恒温器组件或根接口中调用命令。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程已介绍如何将 IoT 即插即用设备和组件连接到 IoT 中心。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用预览版建模开发人员指南](concepts-developer-guide.md)
