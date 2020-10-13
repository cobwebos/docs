---
title: 使用 MQTT 创建 IoT 即插即用设备客户端 | Microsoft Docs
description: 直接使用 MQTT 协议创建 IoT 即插即用设备客户端，而不使用 Azure IoT 设备 SDK
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2e05165a78a54d6aaa49c28a649a97235891f927
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577911"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>使用 MQTT 开发 IoT 即插即用设备客户端

如果可能，应该使用一种 Azure IoT 设备 SDK 来构建 IoT 即插即用设备客户端。 但是，在某些情形下（如使用内存有限的设备），可能需要使用 MQTT 库与 IoT 中心进行通信。

本教程中的示例使用 [Eclipse Mosquitto](http://mosquitto.org/) MQTT 库和 Visual Studio。 本教程中的步骤假设在开发计算机上使用 Windows。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负载
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

使用 Azure IoT 资源管理器工具将新设备添加到 IoT 中心。 你在完成[为 IoT 即插即用设置环境的快速入门和教程](set-up-environment.md)后已配置 IoT 中心和 Azure IoT 资源管理器工具：

1. 启动 Azure IoT 资源管理器工具。
1. 在“IoT 中心”页上，选择“查看此中心内的设备”。
1. 在“设备”页上，选择“+ 新建” 。
1. 创建一个使用自动生成的对称密钥的名为“my-mqtt-device”的设备。
1. 在“设备标识”页上，展开“带有 SAS 令牌的连接字符串”。
1. 选择用作对称密钥 的主密钥，将到期时间设置为 60 分钟，然后选择“生成”。
1. 复制生成的 SAS 令牌连接字符串，稍后将在本教程中使用此值。

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

* 将 `IOTHUBNAME` 标识符替换为 IoT 中心的名称。
* 将 `DEVICEID` 标识符替换为 `my-mqtt-device`。
* 将 `PWD` 标识符替换为为设备生成的 SAS 令牌连接字符串的相应部分。 使用从 `SharedAccessSignature sr=` 到末尾的连接字符串部分。

通过启动 Azure IoT 资源管理器，开始侦听遥测，来验证代码是否正常工作。

运行应用程序 (Ctrl+F5)，几秒钟后会看到如下所示的输出：

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT 示例应用程序的输出":::

在 Azure IoT 资源管理器中，可以看到设备不是 IoT 即插即用设备：

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="MQTT 示例应用程序的输出":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>将设备设为 IoT 即插即用设备

IoT 即插即用设备必须遵循一组简单约定。 如果设备在连接时发送模型 ID，则它会成为 IoT 即插即用设备。

在此示例中，将模型 ID 添加到 MQTT 连接数据包。 在 `USERNAME` 中将模型 ID 作为查询字符串参数进行传递，并将 `api-version` 更改为 `2020-09-30`：

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

重新生成并运行示例。

设备孪生现在包含模型 ID：

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="MQTT 示例应用程序的输出":::

现在可以在 IoT 即插即用组件中导航：

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="MQTT 示例应用程序的输出":::

现在可以修改设备代码以实现模型中定义的遥测、属性和命令。 若要查看使用 Mosquitto 库的恒温器设备的示例实现，请参阅 GitHub 上的[在 Windows 上使用 MQTT PnP 与 Azure IoTHub，而不使用 IoT SDK](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32)。

> [!NOTE]
>客户端使用 `IoTHubRootCA_Baltimore.pem` 根证书文件验证它连接到的 IoT 中心的标识。

### <a name="mqtt-topics"></a>MQTT 主题

以下定义适用于设备用于将信息发送到 IoT 中心的 MQTT 主题。 若要了解详细信息，请曾参阅[使用 MQTT 协议与 IoT 中心通信](../iot-hub/iot-hub-mqtt-support.md)：

* `DEVICE_CAPABILITIES_MESSAGE` 定义设备用于报告它所实现的接口的主题。
* `DEVICETWIN_PATCH_MESSAGE` 定义设备用于向 IoT 中心报告属性更新的主题。
* `DEVICE_TELEMETRY_MESSAGE` 定义设备用于向 IoT 中心发送遥测的主题。

有关 MQTT 的详细信息，请访问[适用于 Azure IoT 的 MQTT 示例](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub 存储库。
  
## <a name="next-steps"></a>后续步骤

在本教程中，学习了如何修改 MQTT 设备客户端，以遵循 IoT 即插即用约定。 若要详细了解 IoT 即插即用，请参阅：

> [!div class="nextstepaction"]
> [体系结构](concepts-architecture.md)

若要详细了解 IoT 中心对 MQTT 协议的支持，请参阅：

> [!div class="nextstepaction"]
> [使用 MQTT 协议与 IoT 中心通信](../iot-hub/iot-hub-mqtt-support.md)
