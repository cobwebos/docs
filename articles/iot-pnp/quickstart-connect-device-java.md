---
title: 将 IoT 即插即用示例 Java 设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成并运行可连接到 IoT 中心的 IoT 即插即用示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6dfd63f92b8c420a3c5281ee2962b211b8779dd1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045051"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>快速入门：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入门介绍如何生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 该示例应用程序以 Java 编写，包含在用于 Java 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

要完成本快速入门，需在本地 Windows 环境上安装以下软件：

* Java SE 开发工具包 8。 在[针对 Azure 和 Azure Stack 的 Java 长期支持](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)中的“长期支持”下选择“Java 8”。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

## <a name="download-the-code"></a>下载代码

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT 中心设备 Java SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT Java SDK 和库](https://github.com/Azure/azure-iot-sdk-java) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>生成代码

在 Windows 上，导航到克隆的 Java SDK 存储库的根文件夹。

运行以下命令以生成示例应用程序：

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>运行设备示例

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md)。

导航到“\device\iot-device-samples\pnp-device-sample\thermostat-device-sample”文件夹。

要运行示例应用程序，请运行以下命令：

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现了一个简单的 IoT 即插即用恒温器设备。 此示例实现的模型不使用 IoT 即插即用[组件](concepts-components.md)。 [调温器设备的 DTDL 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)定义了设备实现的遥测、属性和命令。

设备代码使用标准 `DeviceClient` 类连接到 IoT 中心。 设备发送在连接请求中实现的 DTDL 模型的模型 ID。 发送模型 ID 的设备是 IoT 即插即用设备：

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

模型 ID 存储在代码中，如以下代码片段所示：

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

更新属性、处理命令和发送遥测数据的代码与不使用 IoT 即插即用约定的设备的代码相同。

该示例使用 JSON 库来分析从 IoT 中心发送的有效负载中的 JSON 对象：

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](./quickstart-service-node.md)