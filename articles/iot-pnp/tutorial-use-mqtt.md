---
title: 使用 MQTT 创建 IoT 即插即用预览版设备客户端 | Microsoft Docs
description: 直接使用 MQTT 协议创建 IoT 即插即用预览版设备客户端，而不使用 Azure IoT 设备 SDK
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535166"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>使用 MQTT 开发 IoT 即插即用预览版设备客户端

如果可能，应该使用一种 Azure IoT 设备 SDK 来构建 IoT 即插即用设备客户端。 但是，在某些情形下（如使用内存有限的设备），可能需要使用 MQTT 库与 IoT 中心进行通信。

本教程中的示例使用 [Eclipse Mosquitto](http://mosquitto.org/) MQTT 库和 Visual Studio。 本教程中的步骤假设在开发计算机上使用 Windows。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负载
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Azure IoT 资源管理器](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，以获取设备用于连接到中心的共享访问签名。 记下此字符串，在本教程的稍后部分使用它：

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

使用 IoT 中心连接字符串配置 Azure IoT 资源管理器工具：

1. 启动 Azure IoT 资源管理器工具。
1. 在“设置”页上，将 IoT 中心连接字符串粘贴到“应用配置”设置中。
1. 选择“保存并连接”。
1. 以前添加的设备会处于主页上的设备列表中。

## <a name="clone-sample-repo"></a>克隆示例存储库

使用以下命令将示例存储库克隆到本地计算机上的合适位置：

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>安装 MQTT 库

使用 `vcpkg` 工具下载并生成 Eclipse Mosquitto 库。

使用以下命令将 Vcpkg 存储库克隆到本地计算机上的合适位置：

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

使用以下命令准备 `vcpkg` 环境。 运行 `vcpkg integrate install` 时需要提升的命令提示符：

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

使用以下命令下载并生成 Eclipse Mosquitto 库：

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>将示例迁移到 IoT 即插即用

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>查看非 IoT 即插即用示例代码

生成并运行代码之前，使用 IoT 中心和设备的详细信息更新代码。

若要在 Visual Studio 中查看示例代码，请打开 IoTMQTTSample\src\Windows 文件夹中的 MQTTWin32.sln 解决方案文件。

在“解决方案资源管理器”中，右键单击 TelemetryMQTTWin32 项目，然后选择“设为启动项目”。

在 TelemetryMQTTWin32 项目中，打开 MQTT_Mosquitto.cpp 源文件。 使用之前记下的设备详细信息更新连接信息定义。 替换以下内容的标记字符串占位符：

* `IOTHUBNAME` 标识符，替换为所创建的 IoT 中心的名称。
* `DEVICEID` 标识符，替换为所创建的设备的名称。
* `PWD` 标识符，替换为对设备生成的共享访问签名值。

通过启动 Azure IoT 资源管理器，开始侦听遥测，来验证代码是否正常工作。

运行应用程序 (Ctrl+F5)，几秒钟后会看到如下所示的输出：

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT 示例应用程序的输出":::

在 Azure IoT 资源管理器中，可以看到设备不是 IoT 即插即用设备：

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Azure IoT 资源管理器中的非 IoT 即插即用设备":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>将设备设为 IoT 即插即用设备

IoT 即插即用设备必须遵循一组简单约定。 如果设备在连接时发送模型 ID，则它会成为 IoT 即插即用设备。

在此示例中，会将模型 ID** 添加到 MQTT 连接数据包。 在 `USERNAME` 中将模型 ID 作为查询字符串参数进行传递，并将 `api-version` 更改为 `2020-05-31-preview`：

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

重新生成并运行示例。

设备孪生现在包含模型 ID：

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="在 Azure IoT 资源管理器中查看模型 ID":::

现在可以在 IoT 即插即用组件中导航：

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="在 Azure IoT 资源管理器中查看组件":::

现在可以修改设备代码以实现模型中定义的遥测、属性和命令。 若要查看使用 Mosquitto 库的恒温器设备的示例实现，请参阅 GitHub 上的[在 Windows 上使用 MQTT PnP 与 Azure IoTHub，而不使用 IoT SDK](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32)。

> [!NOTE]
> 默认情况下，共享访问签名仅在 60 分钟内有效。

> [!NOTE]
>客户端使用 `IoTHubRootCA_Baltimore.pem` 根证书文件验证它连接到的 IoT 中心的标识。

### <a name="mqtt-topics"></a>MQTT 主题

以下定义适用于设备用于将信息发送到 IoT 中心的 MQTT 主题。 若要了解详细信息，请曾参阅[使用 MQTT 协议与 IoT 中心通信](../iot-hub/iot-hub-mqtt-support.md)：

* `DEVICE_CAPABILITIES_MESSAGE` 定义设备用于报告它所实现的接口的主题。
* `DEVICETWIN_PATCH_MESSAGE` 定义设备用于向 IoT 中心报告属性更新的主题。
* `DEVICE_TELEMETRY_MESSAGE` 定义设备用于向 IoT 中心发送遥测的主题。

有关 MQTT 的详细信息，请访问[适用于 Azure IoT 的 MQTT 示例](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub 存储库。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，学习了如何修改 MQTT 设备客户端，以遵循 IoT 即插即用约定。 若要详细了解 IoT 即插即用，请参阅：

> [!div class="nextstepaction"]
> [体系结构](concepts-architecture.md)

若要详细了解 IoT 中心对 MQTT 协议的支持，请参阅：

> [!div class="nextstepaction"]
> [使用 MQTT 协议与 IoT 中心通信](../iot-hub/iot-hub-mqtt-support.md)
