---
title: 将 IoT 即插即用预览示例设备代码连接到 IoT 中心 (Linux) | Microsoft Docs
description: 在连接到 IoT 中心的 Linux 上生成并运行 IoT 即插即用预览示例设备代码。 使用 Azure CLI 查看由设备发送到中心的信息。
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531243"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>快速入门：将 Linux 上运行的示例 IoT 即插即用预览设备应用程序连接到 IoT 中心 (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入门介绍如何在 Linux 上生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心并使用 Azure CLI 查看其发送到中心的信息。 该示例应用程序以 C 编写，包含在适用于 C 的 Azure IoT 设备 SDK 中。解决方案开发人员可以使用 Azure CLI 来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必备条件

本快速入门假设你使用 Ubuntu Linux。 本教程中的步骤已使用 Ubuntu 18.04 进行了测试。

要完成本快速入门，需在本地 Linux 计算机上安装以下软件：

使用 `apt-get` 命令安装“GCC”、“Git”、“cmake”和所有依赖项    ：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

验证 `cmake` 的版本是否高于 2.8.12，GCC 的版本是否高于 4.4.7    。

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT C SDK 和库](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库克隆到此位置：

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

应该预料到此操作需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

使用设备 SDK 生成包含的示例代码。 生成的应用程序将模拟连接到 IoT 中心的设备。 应用程序将发送遥测数据和属性，并接收命令。

1. 在设备 SDK 根文件夹中创建一个 `cmake` 子目录，并导航到该文件夹：

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以生成设备 SDK 和代码存根：

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>更新模型存储库

在运行示例之前，请将设备功能模型和接口定义添加到公司模型存储库中：

1. 使用 Microsoft 工作或学校帐户或 Microsoft 合作伙伴 ID（若有）登录 [Azure IoT 认证门户网站](https://preview.catalog.azureiotsolutions.com)。

1. 依次选择“公司存储库”、“功能模型”   。

1. 依次选择“新建”、“上传”   。

1. 在设备 SDK 根文件夹中的 `digitaltwin_client/samples` 文件夹中，选择文件 `SampleDevice.capabilitymodel.json`。 选择“打开”，然后选择“保存”，将模型文件上传到存储库   。

1. 依次选择“公司存储库”、“接口”   。

1. 依次选择“新建”、“上传”   。

1. 在设备 SDK 根文件夹中的 `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` 文件夹中，选择文件 `EnvironmentalSensor.interface.json`。 选择“打开”，然后选择“保存”，将接口文件上传到存储库   。

1. 依次选择“公司存储库”、“连接字符串”。   请记下第一个公司模型存储库连接字符串，稍后将在本快速入门中使用该字符串  。

## <a name="run-the-device-sample"></a>运行设备示例

在 SDK 中运行示例应用程序，以模拟将遥测发送到 IoT 中心的 IoT 即插即用设备。 运行示例应用程序：

1. 在 `cmake` 文件夹中，导航到包含可执行文件的文件夹：

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. 运行可执行文件：

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>使用 Azure IoT CLI 验证代码

设备客户端示例启动后，请验证是否可通过 Azure CLI 使用它。

使用以下命令查看示例设备正在发送的遥测。 可能需要等待一两分钟，然后才能在输出中看到遥测：

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

使用以下命令查看设备发送的属性：

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
