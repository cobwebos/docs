---
title: 将 IoT 即插即用示例 C 设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成和运行使用了多个组件且连接到 IoT 中心的 IoT 即插即用示例 C 设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e1a2a45d0f5743874ce0c0b20190d7d396094e43
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046377"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>教程：将 Linux 或 Windows 上运行的 IoT 即插即用多组件设备应用程序连接到 IoT 中心 (C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教程介绍如何使用组件生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 C 编写，包含在适用于 C 的 Azure IoT 设备 SDK 中。解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

可以在 Linux 或 Windows 上完成本教程。 本教程中的 shell 命令遵循适用于路径分隔符“`/`”的 Linux 约定，如果你是在 Windows 上操作，请务必将这些分隔符替换为“`\`”。

先决条件根据操作系统而异：

### <a name="linux"></a>Linux

本教程假设你使用 Ubuntu Linux。 本教程中的步骤已使用 Ubuntu 18.04 进行了测试。

若要在 Linux 上完成本教程，需在本地 Linux 环境上安装以下软件：

使用 `apt-get` 命令安装“GCC”、“Git”、“cmake”和所有必要的依赖项：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

验证 `cmake` 的版本是否高于 2.8.12，GCC 的版本是否高于 4.4.7  。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负荷。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

## <a name="download-the-code"></a>下载代码

如果已完成[快速入门：将 Linux 或 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (C)](quickstart-connect-device-c.md)，则已下载了代码。

在本教程中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C SDK 的开发环境。

在所选文件夹中打开命令提示符。 执行以下命令将 [Azure IoT C SDK 和库](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库克隆到此位置：

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

应该预料到此操作需要几分钟才能完成。

## <a name="build-and-run-the-code"></a>生成并运行代码

可以使用 Visual Studio 或在命令行使用 `cmake` 生成并运行代码。

### <a name="use-visual-studio"></a>使用 Visual Studio

1. 打开克隆存储库的根文件夹。 几秒钟后，Visual Studio 中的 CMake 支持会创建运行和调试项目所需的所有内容。
1. Visual Studio 准备就绪后，在“解决方案资源管理器”中，导航到示例 iothub_client/samples/pnp/pnp_temperature_controller/。
1. 右键单击“pnp_temperature_controller.c”文件并选择“添加调试配置”。 选择“默认值”。
1. Visual Studio 将打开“launch.vs.json”文件。 按照以下代码片段编辑此文件，设置所需的环境变量。 完成[为 IoT 即插即用设置环境的快速入门和教程](set-up-environment.md)后，请记下范围 ID 和注册主密钥：

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. 右键单击“pnp_temperature_controller.c”文件并选择“设置为启动项”。
1. 若要在 Visual Studio 中跟踪代码执行，请在 pnp_temperature_controller.c 文件中向 `main` 函数添加一个断点。
1. 现在可以从“调试”菜单中运行并调试示例。

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

### <a name="use-cmake-at-the-command-line"></a>在命令行上使用 `cmake`

生成示例：

1. 在克隆设备 SDK 根文件夹中创建一个 cmake 子文件夹，并导航到该文件夹：

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令，为 SDK 和示例生成项目文件：

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)。

若要运行该示例：

1. 在 cmake 文件夹中，导航到包含可执行文件的文件夹，并运行该文件：

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现具有[多个组件](concepts-components.md)的模型。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

### <a name="iot-plug-and-play-helper-functions"></a>IoT 即插即用帮助程序函数

对于本示例，代码使用 /common 文件夹中的一些帮助程序函数：

pnp_device_client_ll 包含 IoT 即插即用连接方法，其中包含 `model-id` 作为参数 `PnP_CreateDeviceClientLLHandle`。

pnp_protocol 包含 IoT 即插即用帮助程序函数：

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

这些帮助程序函数是通用的，足以供你在自己的项目中使用。 此示例在与模型中每个组件对应的三个文件中使用：

* pnp_deviceinfo_component
* pnp_temperature_controller
* pnp_thermostat_component

例如，在 pnp_deviceinfo_component 文件中，`SendReportedPropertyForDeviceInformation` 函数使用两种帮助程序函数：

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

示例中的每个组件都遵循此模式。

### <a name="code-flow"></a>代码流

`main` 函数将初始化连接并发送模型 ID：

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

此代码使用 `PnP_CreateDeviceClientLLHandle` 连接到 IoT 中心，将 `modelId` 设置为一个选项，并设置设备方法和设备孪生回叫处理程序，以提供直接方法和设备孪生更新：

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` 也包含连接信息。 环境变量 IOTHUB_DEVICE_SECURITY_TYPE 确定示例是使用连接字符串还是使用设备预配服务来连接到 IoT 中心。

设备发送模型 ID 时，它会成为 IoT 即插即用设备。

回叫处理程序就绪后，设备会响应孪生更新和直接方法调用：

* 对于设备孪生回叫，`PnP_TempControlComponent_DeviceTwinCallback` 调用 `PnP_ProcessTwinData` 函数来处理数据。 `PnP_ProcessTwinData` 使用访问者模式分析 JSON，然后访问每个属性，并对每个元素调用 `PnP_TempControlComponent_ApplicationPropertyCallback`。

* 对于命令回叫，`PnP_TempControlComponent_DeviceMethodCallback` 函数使用帮助程序函数来分析命令和组件名称：

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    然后 `PnP_TempControlComponent_DeviceMethodCallback` 函数对该组件调用命令：

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main` 函数将初始化发送到 IoT 中心的只读属性：

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main` 函数进入一个循环，以更新每个组件的事件和遥测数据：

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry` 函数演示如何使用 `PNP_THERMOSTAT_COMPONENT` 结构。 该示例使用此结构来存储关于温度控制器中两个调温器的信息。 代码使用 `PnP_CreateTelemetryMessageHandle` 函数来准备并发送消息：

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main` 函数最终会销毁不同的组件，并关闭与中心的连接。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程已介绍如何将 IoT 即插即用设备和组件连接到 IoT 中心。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用建模开发人员指南](concepts-developer-guide-device-csharp.md)