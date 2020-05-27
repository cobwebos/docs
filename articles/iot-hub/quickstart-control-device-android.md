---
title: 快速入门：从 Azure IoT 中心控制设备 (Android) | Microsoft Docs
description: 本快速入门会运行两个示例 Java 应用程序。 一个应用程序为服务应用程序，可远程控制连接到中心的设备。 另一个应用程序在物理或模拟设备上运行，该设备连接到可以进行远程控制的中心。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: b9afd1c4ca5e4c652a03bc2ef652b8e43ea12787
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727193"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>快速入门：控制连接到 IoT 中心的设备 (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

在本快速入门中，会使用直接方法来控制连接到 Azure IoT 中心的模拟设备。 IoT 中心是一项 Azure 服务，使你可以从云管理 IoT 设备，并将大量设备遥测引入云进行存储或处理。 可使用直接方法远程更改连接到 IoT 中心的设备的行为。 本快速入门使用两个应用程序：一个是模拟设备应用程序，用于响应从后端服务应用程序调用的直接方法；另一个是服务应用程序，用于在 Android 设备上调用直接方法。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [带 Android SDK 27 的 Android Studio](https://developer.android.com/studio/)。 有关详细信息，请参阅[安装 Android Studio](https://developer.android.com/studio/install)。

* [Git](https://git-scm.com/download/)。

* [设备 SDK 示例 Android 应用程序](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)，包括在 [Azure IoT 示例 (Java)](https://github.com/Azure-Samples/azure-iot-samples-java) 中。

* [服务 SDK 示例 Android 应用程序](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)，包括在 Azure IoT 示例 (Java) 中。

* 端口 8883 在防火墙中处于打开状态。 本快速入门中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>添加 Azure IoT 扩展

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IoT 扩展会将特定于 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-android.md)，则可跳过此步骤，使用已创建的 IoT 中心。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-android.md)，则可跳过此步骤，使用在上一快速入门中注册的设备。

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyAndroidDevice**：这是所注册的设备的名称。 建议使用 **MyAndroidDevice**，如图所示。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

## <a name="retrieve-the-service-connection-string"></a>检索服务连接字符串

还需一个服务连接字符串  ，以便后端服务应用程序能够连接到 IoT 中心以执行方法并检索消息。 以下命令检索 IoT 中心的服务连接字符串：

**YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

记下如下所示的服务连接字符串：

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

稍后会在快速入门中用到此值。 此服务连接字符串与你在上一步中记录的设备连接字符串不同。

## <a name="listen-for-direct-method-calls"></a>侦听直接方法调用

本快速入门的这两个示例都是 GitHub 上 azure-iot-samples-java 存储库的一部分。 请下载或克隆 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存储库。

设备 SDK 示例应用程序可以在物理 Android 设备或 Android 模拟器上运行。 示例会连接到 IoT 中心的特定于设备的终结点，发送模拟遥测数据，并侦听中心的直接方法调用。 在本快速入门中，中心的直接方法调用告知设备对其发送遥测的间隔进行更改。 执行直接方法后，模拟设备会将确认发送回中心。

1. 在 Android Studio 中打开 GitHub 示例 Android 项目。 此项目位于克隆的或下载的 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存储库副本的以下目录中。

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. 在 Android Studio 中打开示例项目的 *gradle.properties*，将 **Device_Connection_String** 占位符替换为此前记下的设备连接字符串。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. 在 Android Studio 中，单击“文件”   >   “将项目与 Gradle 文件同步”。 验证生成是否已完成。

   > [!NOTE]
   > 如果项目同步失败，可能是由于以下某个原因：
   >
   > * 项目中引用的 Android Gradle 插件和 Gradle 的版本对于 Android Studio 版本来说已经过时。 请按照[这些说明](https://developer.android.com/studio/releases/gradle-plugin)操作，以引用并安装正确版本的插件和 Gradle 以进行安装。
   > * Android SDK 的许可协议尚未签署。 请按照 Build 输出中的说明签署许可协议并下载 SDK。

4. 生成完成以后，请单击“运行”   >   “运行‘应用’”。 将应用配置为在物理 Android 设备或 Android 模拟器上运行。 若要详细了解如何在物理设备或模拟器上运行 Android 应用，请参阅[运行您的应用](https://developer.android.com/training/basics/firstapp/running-app)。

5. 待应用加载以后，请单击“启动”按钮，开始将遥测数据发送到 IoT 中心： 

    ![客户端设备 android 应用的示例屏幕截图](media/quickstart-control-device-android/sample-screenshot.png)

在运行时期间执行服务 SDK 示例以更新遥测时间间隔时，需要让此应用在物理设备或模拟器上运行。

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

在本部分中，将使用具有 [IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)的 Azure Cloud Shell 监视 Android 设备发送的消息。

1. 通过 Azure Cloud Shell 运行以下命令以建立连接并从 IoT 中心读取消息：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    以下屏幕截图显示了 IoT 中心接收 Android 设备发送的遥测数据后的输出：

      ![使用 Azure CLI 读取设备消息](media/quickstart-control-device-android/read-data.png)

默认情况下，遥测应用每 5 秒钟从 Android 设备发送一次遥测数据。 在下一部分，将使用直接方法调用更新 Android IoT 设备的遥测时间间隔。

## <a name="call-the-direct-method"></a>调用直接方法

服务应用程序会连接到 IoT 中心的服务端终结点。 应用程序通过 IoT 中心对设备进行直接方法调用，并侦听确认。

请在单独的物理 Android 设备或 Android 模拟器上运行此应用。

IoT 中心后端服务应用程序通常在云中运行，这样可以更轻松地减轻与敏感的连接字符串相关联的风险。该字符串控制某个 IoT 中心的所有设备。 在此示例中，我们将它作为 Android 应用运行，仅仅是为了演示。 其他语言版本的本快速入门提供与典型后端服务应用程序更匹配的示例。

1. 在 Android Studio 中打开 GitHub 服务示例 Android 项目。 此项目位于克隆的或下载的 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存储库副本的以下目录中。

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. 在 Android Studio 中，打开示例项目的 *gradle.properties*。 将 **ConnectionString** 和 **DeviceId** 属性的值更新为此前记下的服务连接字符串和已注册的 Android 设备 ID。

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. 在 Android Studio 中，单击“文件”   >   “将项目与 Gradle 文件同步”。 验证生成是否已完成。

   > [!NOTE]
   > 如果项目同步失败，可能是由于以下某个原因：
   >
   > * 项目中引用的 Android Gradle 插件和 Gradle 的版本对于 Android Studio 版本来说已经过时。 请按照[这些说明](https://developer.android.com/studio/releases/gradle-plugin)操作，以引用并安装正确版本的插件和 Gradle 以进行安装。
   > * Android SDK 的许可协议尚未签署。 请按照 Build 输出中的说明签署许可协议并下载 SDK。

4. 生成完成以后，请单击“运行”   >   “运行‘应用’”。 将应用配置为在单独的物理 Android 设备或 Android 模拟器上运行。 若要详细了解如何在物理设备或模拟器上运行 Android 应用，请参阅[运行您的应用](https://developer.android.com/training/basics/firstapp/running-app)。

5. 待应用加载以后，将“设置消息传送时间间隔”值更新为  **1000**，然后单击“调用”。 

    遥测消息传送时间间隔以毫秒为单位。 设备示例的默认遥测时间间隔设置为 5 秒钟。 此更改会更新 Android IoT 设备，使遥测数据每秒发送一次。

    ![输入遥测时间间隔](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. 应用会收到一个表明方法是否已成功执行的确认。

    ![直接方法确认](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，已从后端应用程序调用了设备上的直接方法，并在模拟设备应用程序中响应了直接方法调用。

若要了解如何将设备到云的消息路由到云中的不同目标，请继续学习下一教程。

> [!div class="nextstepaction"]
> [教程：将遥测路由到不同的终结点进行处理](tutorial-routing.md)
