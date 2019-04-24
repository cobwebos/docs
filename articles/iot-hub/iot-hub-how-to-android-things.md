---
title: 使用 Azure IoT SDK 针对 Android Things 平台进行开发 | Microsoft Docs
description: 开发人员指南 - 了解如何使用 Azure IoT 中心 SDK 对 Android Things 进行开发。
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: yizhon
ms.openlocfilehash: 8e36cee9857c00fcb618a8491595432fb0fd60fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400097"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>使用 Azure IoT SDK 针对 Android Things 平台进行开发

[Azure IoT 中心 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 为 Windows、Linux、OSX、MBED 等流行平台，以及 Android 和 iOS 等移动平台提供第一层支持。  作为我们在 IoT 部署中实现更多选择和灵活性的承诺的一部分，Java SDK 还支持 [Android Things](https://developer.android.com/things/) 平台。  使用 [Azure IoT 中心](about-iot-hub.md)作为可扩展到数百万个同时连接的设备的中央消息中心时，开发人员可以在设备端利用 Android Things 操作系统的好处。

本教程概述使用 Azure IoT Java SDK 在 Android Things 上构建设备端应用程序的步骤。

## <a name="prerequisites"></a>必备组件

* 受 Android Things 支持且运行 Android Things OS 的硬件。  可以按照有关如何刷新 Android Things OS 的 [Android Things 文档](https://developer.android.com/things/get-started/kits#flash-at)进行操作。  确保 Android Things 设备连接到 Internet 并连接了基本外围设备（例如键盘、显示器和鼠标）。  本教程使用 Raspberry Pi 3。

* 最新版本的 [Android Studio](https://developer.android.com/studio/)

* 最新版本的 [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以添加 IoT 中心 CLI 扩展并创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyAndroidThingsDevice**：这是为注册的设备提供的名称。 如图所示使用 MyAndroidThingsDevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. 运行以下命令在 Azure Cloud Shell 中获取*设备连接字符串*刚注册的设备。 替换为`YourIoTHubName`下面具有名称为选择 IoT 中心。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

## <a name="building-an-android-things-application"></a>构建 Android Things 应用程序

1. 构建 Android Things 应用程序的第一步是连接到 Android Things 设备。 将 Android Things 设备连接到一个显示器，并将它连接到 Internet。 Android Things 提供了有关如何连接到 WiFi 的[文档](https://developer.android.com/things/get-started/kits)。 连接到 Internet 之后，记下“网络”下列出的 IP 地址。

2. 使用 [adb](https://developer.android.com/studio/command-line/adb) 工具，通过上面记下的 IP 地址连接到 Android Things 设备。 从终端使用此命令仔细检查连接。 应会看到设备列为"已连接"。

   ```
   adb devices
   ```

3. 从此[存储库](https://github.com/Azure-Samples/azure-iot-samples-java)下载我们适用于 Android/Android Things 的示例，或使用 Git。

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. 在 Android Studio 中，打开位于“\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample”中的 Android 项目。

5. 打开 gradle.properties 文件，将“Device_connection_string”替换为前面记下的设备连接字符串。
 
6. 单击“运行 - 调试”，然后选择你的设备以将此代码部署到 Android Things 设备。

7. 成功启动了应用程序后，可以看到应用程序在 Android Things 设备上运行。 此示例应用程序会发送随机生成的温度读数。

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

可通过 IoT 中心查看接收的数据。 IoT 中心 CLI 扩展可以连接到 IoT 中心上的服务端**事件**终结点。 该扩展会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

在 Azure Cloud Shell 中运行以下命令，并将 `YourIoTHubName` 替换为 IoT 中心的名称：

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 了解如何使用 IoT 中心 SDK [管理连接和可靠的消息传送](iot-hub-reliability-features-in-sdks.md)。
* 了解如何[针对移动平台进行开发](iot-hub-how-to-develop-for-mobile-devices.md)，例如 iOS 和 Android。
* [Azure IoT SDK 平台支持](iot-hub-device-sdk-platform-support.md)
