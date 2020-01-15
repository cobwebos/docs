---
title: 将 IoT 即插即用预览版示例设备代码连接到 IoT 中心 | Microsoft Docs
description: 使用 Java，生成并运行可连接到 IoT 中心的 IoT 即插即用预览版示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531303"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>快速入门：将示例 IoT 即插即用预览版设备应用程序连接到 IoT 中心 (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入门介绍如何生成 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 示例应用程序是用 Java 编写的，并作为用于 Java 集合的 Azure IoT 示例的一部分提供。 解决方案开发人员可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要在开发计算机上使用 Java SE 8。 还需要安装 Maven 3。

有关设置的详细信息，请参阅在适用于 Java 的 Microsoft Azure IoT 设备 SDK 中[准备开发环境](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md)。

### <a name="install-the-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

从 Azure IoT 资源管理器的[存储库](https://github.com/Azure/azure-iot-explorer/releases)页面，选择“资产”下的 .msi 文件以查找最近更新，下载并安装该工具的最新版本  。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 _IoT 中心连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，你将准备一个用于克隆和生成适用于 Java 的 Azure IoT 示例的开发环境。

在所选目录中打开一个终端窗口。 执行以下命令，将[适用于 Java 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub 存储库克隆到此位置：

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

此操作可能需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

使用克隆的示例代码来生成可模拟连接到 IoT 中心的设备的应用程序。 应用程序将发送遥测数据和属性，并接收命令。

1. 在本地终端窗口中，转到克隆的存储库所在的文件夹，然后导航到 /azure-iot-samples-java/digital-twin/Samples/device/JdkSample 文件夹  。 然后运行以下命令以安装所需的库，并生成模拟设备应用程序：

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. 配置设备连接字符串： 

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>运行设备示例

运行示例应用程序，以模拟将遥测发送到 IoT 中心的 IoT 即插即用设备。 要运行示例应用程序，请使用以下命令：

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

你会看到一些消息，分别表示设备已连接、正在执行各种设置步骤以及正在等待服务更新，之后会看到遥测日志。 这表明设备现在可以接收命令和属性更新，并且已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 若要确保此工具可从设备中读取接口模型定义，请选择“设置”  。 在“设置”菜单中，“在已连接设备上”可能已显示在即插即用配置中；如果未显示，请依次选择“+ 添加模块定义源”和“在连接设备上”以添加此项    。

1. 回到“设备”概述页上，找到前面创建的设备标识  。 如果设备应用程序仍在命令提示符中运行，请检查设备 Azure IoT 资源管理器中的“连接状态”是否为“已连接”（如果不是，请点击“刷新”直至状态为“已连接”）    。 选择该设备可查看更多详细信息。

1. 展开 ID 为 urn:java_sdk_sample:EnvironmentalSensor:1 的接口，以显示接口和 IoT 即插即用基元 - 属性、命令和遥测  。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到 IoT 即插即用预览版设备并与之交互](howto-develop-solution.md)
