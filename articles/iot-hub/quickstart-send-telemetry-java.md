---
title: 快速入门：向 Azure IoT 中心发送遥测数据 (Java) | Microsoft Docs
description: 本快速入门将运行两个示例 Java 应用程序，从而向 IoT 中心发送模拟遥测数据，并读取 IoT 中心的遥测数据，在云中进行处理。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: fd610af3d1b29b78ef89b8f523ef880696031bf2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637644"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>快速入门：将遥测数据从设备发送到 IoT 中心并使用后端应用程序从中心读取遥测数据 ( Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门会将模拟设备应用程序的遥测数据通过 IoT 中心发送到后端应用程序进行处理。

本快速入门使用两个预先编写的 Java 应用程序，一个用于发送遥测数据，一个用于读取中心的遥测数据。 运行这两个应用程序前，请先创建 IoT 中心并在中心注册设备。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门中运行的两个示例应用程序是使用 Java 编写的。 开发计算机上需要有 Java SE 8 或更高版本。

可从 [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 为多个平台下载 Java。

可以使用以下命令验证开发计算机上 Java 的当前版本：

```cmd/sh
java --version
```

若要生成示例，需安装 Maven 3。 可从 [Apache Maven](https://maven.apache.org/download.cgi) 为多个平台下载 Maven。

可以使用以下命令验证开发计算机上 Maven 的当前版本：

```cmd/sh
mvn --version
```

从 https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip 下载示例 Java 项目并提取 ZIP 存档。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，请使用 Azure CLI 来注册模拟设备。

1. 添加 IoT 中心 CLI 扩展并创建设备标识。 将 `{YourIoTHubName}` 替换为 IoT 中心选择的名称：

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    如果为设备选择不同名称，则在运行示例应用程序之前，请在其中更新设备名称。

1. 运行以下命令，获取刚注册设备的设备连接字符串：

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    记下看起来类似于 `Hostname=...=` 的设备连接字符串。 稍后会在快速入门中用到此值。

1. 还需要来自 IoT 中心的与事件中心兼容的终结点、与事件中心兼容的路径和 iothubowner 主键，确保后端应用程序能连接到 IoT 中心并检索消息。 以下命令可检索 IoT 中心的这些值：

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    记下这三个值，稍后会在快速入门中用到这些值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 在终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

1. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java 文件。

    将 `connString` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 SimulatedDevice.java 文件。

1. 在终端窗口中，运行以下命令，安装所需的库，并生成模拟设备应用程序：

    ```cmd/sh
    mvn clean package
    ```

1. 在终端窗口中，运行以下命令，运行模拟设备应用程序：

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

后端应用程序会连接到 IoT 中心上的服务端“事件”终结点。 应用程序会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

1. 在另一个终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 iot-hub\Quickstarts\read-d2c-messages 文件夹。

1. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java 文件。

    将 `eventHubsCompatibleEndpoint` 变量的值替换为之前记下的与事件中心兼容的终结点。

    将 `eventHubsCompatiblePath` 变量的值替换为之前记下的与事件中心兼容的路径。

    将 `iotHubSasKey` 变量的值替换为之前记下的 iothubowner 主键。 然后将更改保存到 ReadDeviceToCloudMessages.js 文件。

1. 在终端窗口中，运行以下命令，安装所需的库，并生成后端应用程序：

    ```cmd/sh
    mvn clean package
    ```

1. 在终端窗口中，运行以下命令，运行终端应用程序：

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了后端应用程序接收模拟设备发送到 IoT 中心的遥测数据后的输出：

    ![运行后端应用程序](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清理资源

如果打算完成下一快速入门教程，请保留资源组和 IoT 中心，稍后再进行使用。

如果不再需要 IoT 中心，请在门户中删除该中心与资源组。 为此，请选择包含 IoT 中心的 qs-iot-hub-rg 资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 Java 应用程序发送了模拟遥测数据到中心，并使用简单的后端应用程序读取中心的遥测数据。

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-java.md)