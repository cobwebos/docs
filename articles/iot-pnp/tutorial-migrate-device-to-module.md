---
title: 将 IoT 即插即用设备转换为泛型模块 | Microsoft Docs
description: 使用 C# PnP 设备代码，将其转换为模块。
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ccc450242c50f82d4215f6b172f72d8eceab7c52
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046330"
---
# <a name="tutorial-how-to-convert-an-iot-plug-and-play-device-to-a-module-c"></a>教程：如何将 IoT 即插即用设备转换为模块 (C#)

本教程演示了如何转换 IoT 即插即用设备代码以将其作为泛型模块运行。

如果设备在连接到 IoT 中心并实现由模型 ID 标识的数字孪生定义语言 (DTDL) 模型中描述的属性和方法时发布其模型 ID，则该设备为 IoT 即插即用设备。 若要详细了解设备如何使用 DTDL 和模型 ID，请参阅 [IoT 即插即用开发人员指南](./concepts-developer-guide-device-csharp.md)。 模块以相同方式使用模型 ID 和 DTDL 模型。

为了演示如何实现 IoT 即插即用模块，本教程介绍了如何将恒温器 C# 设备示例转换为泛型模块。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

使用 Azure IoT 资源管理器工具将名为 **my-module-device** 的新设备添加到 IoT 中心。

将名为 **my-module** 的模块添加到 **my-module-device**：

1. 在 Azure IoT 资源管理器工具中，导航到 **my-module-device** 设备。

1. 选择“模块标识”，然后选择“+ 添加”。

1. 输入 **my-module** 作为模块标识名称，然后选择“保存”。

1. 在模块标识列表中，选择“my-module”。 然后复制主连接字符串。 本教程后面的步骤将使用此模块连接字符串。

1. 选择“模块孪生”选项卡，并请注意，没有所需的或报告的属性：

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
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
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>下载代码

如果尚未这样做，请将 Azure IoT 中心设备 C# SDK GitHub 存储库克隆到你的本地计算机：

在所选文件夹中打开命令提示符。 使用以下命令，将 [Azure IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>准备项目

若要打开并准备示例项目，请执行以下操作：

1. 在 Visual Studio 2019 中打开“azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj”项目文件。

1. 在 Visual Studio 中，导航到“项目”>“恒温器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | 你之前记下的模块连接字符串 |

    若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md)。

## <a name="modify-the-code"></a>修改代码

若要修改代码，使之作为模块工作而非作为设备工作，请执行以下操作：

1. 在 Visual Studio 中打开 Parameter.cs，修改设置 **PrimaryConnectionString** 变量的行，如下所示：

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. 在 Visual Studio 中打开 Program.cs，将 `DeviceClient` 类的七个实例替换为 `ModuleClient` 类。

    > [!TIP]
    > 使用 Visual Studio 搜索和替换功能并启用“匹配大小写”和“全字匹配”，以将 `DeviceClient` 替换为 `ModuleClient`。

1. 在 Visual Studio 中打开 Thermostat.cs，将 `DeviceClient` 类的两个实例替换为 `ModuleClient` 类，如下所示。

1. 保存对修改的文件所做的更改。

如果你运行代码，然后使用 Azure IoT 资源管理器来查看更新的模块孪生，则会看到更新的设备孪生，以及模型 ID 和模块报告的属性：

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
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
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>与设备模块交互

服务 SDK 允许你检索所连接的 IoT 即插即用设备和模块的模型 ID。 可以使用服务 SDK 设置可写属性并调用命令：

1. 在 Visual Studio 的另一个实例中，打开“azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj”项目。

1. 在 Visual Studio 中，导航到“项目”>“恒温器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | 在完成[设置环境](set-up-environment.md)时记下的值 |

    > [!TIP]
    > 还可以在 Azure IoT 资源管理器工具中查找 IoT 中心连接字符串。

1. 打开 Program.cs 文件，修改调用某个命令的行，如下所示：

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. 在 Program.cs 文件中，修改检索设备孪生的行，如下所示：

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. 请确保模块客户端示例仍在运行，然后运行此服务示例。 服务示例的输出显示了来自设备孪生和命令调用的模型 ID：

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    模块客户端的输出显示命令处理程序的响应：

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>转换为 IoT Edge 模块

若要转换此示例，将其用作 IoT 即插即用 IoT Edge 模块，你必须容器化此应用程序。 无需进一步更改代码。 连接字符串环境变量是由 IoT Edge 运行时在启动时注入的。 若要了解详细信息，请参阅[使用 Visual Studio 2019 开发和调试适用于 Azure IoT Edge 的模块](../iot-edge/how-to-visual-studio-develop-module.md)。

若要了解如何部署容器化模块，请参阅：

* [在 Ubuntu 虚拟机上运行 Azure IoT Edge](../iot-edge/how-to-install-iot-edge-ubuntuvm.md)。
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md)。

你可以使用 Azure IoT 资源管理器工具来查看：

* 你的 IoT Edge 设备在模块孪生中的模型 ID。
* 来自 IoT Edge 设备的遥测数据。
* IoT Edge 模块孪生属性更新（触发 IoT 即插即用通知）。
* IoT Edge 模块对 IoT 即插即用命令做出的反应。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 IoT 即插即用设备和模块连接到 IoT 中心。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用建模开发人员指南](./concepts-developer-guide-device-csharp.md)