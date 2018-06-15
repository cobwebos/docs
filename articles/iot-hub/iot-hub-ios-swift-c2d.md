---
title: 使用 Azure IoT 中心发送云到设备消息 (iOS) | Microsoft Docs
description: 如何使用用于 iOS 的 Azure IoT SDK 将云到设备消息从 Azure IoT 中心发送到设备。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 62647620f6bbeadecfa778f91855ef1eee5240dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634271"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>使用 IoT 中心发送云到设备消息 (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [将遥测数据从设备发送到 IoT 中心]一文介绍了如何创建 IoT 中心、在其中预配设备标识，以及编写模拟设备应用来发送设备到云的消息。

本文介绍以下操作：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。
* 在设备上接收云到设备的消息。
* 通过解决方案后端，请求确认收到从 IoT 中心发送到设备的消息（反馈）。

可以在 [IoT 中心开发人员指南][IoT Hub developer guide - C2D]中找到有关云到设备消息的详细信息。

在本文结束时，运行两个 Swift iOS 项目：

* **sample-device**：在[将遥测数据从设备发送到 IoT 中心]中创建的同一应用，可连接到 IoT 中心并接收云到设备的消息。
* **sample-service**：通过 IoT 中心将云到设备的消息发送到模拟设备应用，然后接收中心的传送确认。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 对许多设备平台和语言（包括 C、Java 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的逐步说明，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

- 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）
- Azure 中的活动 IoT 中心。 
- [Azure 示例](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)中的代码示例。
- 最新版本的 [XCode](https://developer.apple.com/xcode/)，运行最新版本的 iOS SDK。 本快速入门已使用 XCode 9.3 和 iOS 11.3 测试过。
- 最新版 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。


## <a name="simulate-an-iot-device"></a>模拟 IoT 设备
在本部分，我们将模拟一个运行 Swift 应用程序的 iOS 设备，以从 IoT 中心接收云到设备的消息。 

这是在[将遥测数据从设备发送到 IoT 中心]一文中创建的示例设备。 如果已运行该设备，则可跳过本部分。

### <a name="install-cocoapods"></a>安装 CocoaPods

CocoaPods 管理那些使用第三方库的 iOS 项目的依赖项。

在终端窗口中，导航到在先决条件部分下载的 Azure-IoT-Samples-iOS 文件夹。 然后，导航到示例项目：

```sh
cd quickstart/sample-device
```

确保 XCode 已关闭，运行以下命令，以便安装在 **podfile** 文件中声明的 CocoaPods：

```sh
pod install
```

除了为项目安装所需的 Pod，安装命令还创建了一个 XCode 工作区文件，该文件已配置为对依赖项使用 Pod。 

### <a name="run-the-sample-device-application"></a>运行示例设备应用程序 

1. 检索设备的连接字符串。 可以从 Azure 门户中的设备详细信息边栏选项卡复制此字符串，或者使用以下 CLI 命令检索它： 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. 在 XCode 中打开示例工作区。

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. 展开“MQTT 客户端示例”项目，然后展开同名的文件夹。  
3. 打开 **ViewController.swift**，以便在 XCode 中进行编辑。 
4. 搜索 **connectionString** 变量，并使用第一个步骤中复制的设备连接字符串更新其值。
5. 保存所做更改。 
6. 使用“生成并运行”按钮或“Command + R”组合键在设备模拟器中运行项目。 

   ![运行项目](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>模拟服务设备

在本部分，我们将模拟另一个 iOS 设备，该设备上安装了可以通过 IoT 中心发送云到设备的消息的 Swift 应用。 此配置可用于以下 IoT 场景：一台 iPhone 或 iPad 充当与 IoT 中心连接的其他 iOS 设备的控制器。 

### <a name="install-cocoapods"></a>安装 CocoaPods

CocoaPods 管理那些使用第三方库的 iOS 项目的依赖项。

导航到在先决条件部分下载的 Azure IoT iOS Samples 文件夹。 然后导航到示例服务项目：

```sh
cd quickstart/sample-service
```

确保 XCode 已关闭，运行以下命令，以便安装在 **podfile** 文件中声明的 CocoaPods：

```sh
pod install
```

除了为项目安装所需的 Pod，安装命令还创建了一个 XCode 工作区文件，该文件已配置为对依赖项使用 Pod。

### <a name="run-the-sample-service-application"></a>运行示例服务应用程序

1. 检索 IoT 中心的服务连接字符串。 可以从 Azure 门户上“共享访问策略”边栏选项卡中的“iothubowner”策略复制此字符串，或者使用以下 CLI 命令检索它：  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. 在 XCode 中打开示例工作区。

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. 展开“AzureIoTServiceSample”项目，然后展开同名的文件夹。  
4. 打开 **ViewController.swift**，以便在 XCode 中进行编辑。 
5. 搜索 **connectionString** 变量，并使用前面复制的服务连接字符串更新其值。
6. 保存所做更改。 
7. 在 Xcode 中，将模拟器设置更改为其他 iOS 设备，而不是用来运行 IoT 设备的 iOS 设备。 XCode 无法运行相同类型的多个模拟器。 

   ![更改模拟器设备](media/iot-hub-ios-swift-c2d/change-device.png)

8. 使用“生成并运行”按钮或“Command + R”组合键在设备模拟器中运行项目。 

   ![运行项目](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息
现在，可以使用这两个应用程序发送和接收云到设备的消息。

1. 在模拟的 IoT 设备上运行的“iOS 应用示例”应用中，单击“启动”。 应用程序开始发送设备到云的消息，但同时也会开始侦听云到设备的消息。 

   ![查看示例 IoT 设备应用](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. 在模拟的服务设备上运行的“IoT 中心服务客户端示例”应用中，输入要向其发送消息的 IoT 设备的 ID。 
3. 编写纯文本消息，然后单击“发送”。 

单击“发送”后，系统会执行多个操作。 服务示例会将消息发送到 IoT 中心，由于前面提供了服务连接字符串，应用可以访问该 IoT 中心。 IoT 中心会检查设备 ID，将消息发送到目标设备，并向源设备发送确认回执。 在模拟的 IoT 设备上运行的应用会检查来自 IoT 中心的消息，并在屏幕上列显最新消息的文本。

输出应类似于以下示例：

   ![查看云到设备的消息](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>后续步骤
在本教程中，已学习如何发送和接收云到设备的消息。 

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[将遥测数据从设备发送到 IoT 中心]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT 中心开发人员指南]: iot-hub-devguide.md
[Azure IoT 开发人员中心]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[Azure IoT 远程监视解决方案加速器]: https://azure.microsoft.com/documentation/suites/iot-suite/
