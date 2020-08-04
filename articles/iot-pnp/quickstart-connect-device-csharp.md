---
title: 将 IoT 即插即用预览版示例 C# 设备代码连接到 IoT 中心 | Microsoft Docs
description: 在连接到 IoT 中心的 Windows 上生成并运行 IoT 即插即用预览示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 015e20fa975563fee8ac2d61f9bad1f9f03738ce
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352549"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c"></a>快速入门：将 Windows 上运行的示例 IoT 即插即用预览设备应用程序连接到 IoT 中心 (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入门介绍如何生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 该示例应用程序以 CSharp 编写，包含在用于 C# 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

要在 Windows 上完成本快速入门，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

要在本快速入门的第二部分中与示例设备进行交互，请使用 Azure IoT 资源管理器工具。 [下载并安装适用于你的操作系统的最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 还可以使用 Azure IoT 资源管理器工具查找 IoT 中心连接字符串。

运行以下命令，获取已添加到中心的设备的设备连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>下载代码

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C# SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT C# SDK 和库](https://github.com/Azure/azure-iot-sdk-csharp) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>生成代码

在 Visual Studio 2019 中打开“azure-iot-sdk-csharp/iothub/device/samples/PnpDeviceSamples/Thermostat/Thermostat.csproj”项目文件。

现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

## <a name="run-the-device-sample"></a>运行设备示例

创建一个名为“IOTHUB_DEVICE_CONNECTION_STRING”的环境变量，以存储你之前记下的设备连接字符串。

若要在 Windows 上的 Visual Studio 中跟踪代码执行，请在 program.cs 文件中向 `main` 函数添加一个断点。

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现了一个简单的 IoT 即插即用恒温器设备。 此示例实现的模型不使用 IoT 即插即用[组件](concepts-components.md)。 [恒温器设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)定义了设备实现的遥测、属性和命令。

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

更新属性、处理命令和发送遥测数据的代码与不使用 IoT 即插即用约定的设备的代码相同。

该示例使用 JSON 库来分析从 IoT 中心发送的有效负载中的 JSON 对象：

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
