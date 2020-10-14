---
title: 如何将在 Linux 或 Windows 上运行的 IoT 即插即用 bridge 示例连接到 IoT 中心 |Microsoft Docs
description: 在 Linux 或 Windows 上构建并运行 IoT 即插即用 bridge，以连接到 IoT 中心。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6670f654685f8d5cdcaf55d2b1679738a57ecab4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042790"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>如何将在 Linux 或 Windows 上运行的 IoT 即插即用 bridge 示例连接到 IoT 中心

本操作说明演示了如何构建 IoT 即插即用 bridge 的环境适配器，如何将其连接到 IoT 中心，以及如何使用 Azure IoT 资源管理器工具查看它发送的遥测数据。 IoT 即插即用 bridge 用 C 语言编写，包括适用于 C 语言的 Azure IoT 设备 SDK。在本教程结束时，你应该能够运行 IoT 即插即用 bridge，并在 Azure IoT 资源管理器中查看它的报表遥测： :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="显示 Azure iot 资源管理器的屏幕截图，其中包含报告的遥测 (湿度，温度) IoT 即插即用桥。":::

## <a name="prerequisites"></a>先决条件

可以在 Linux 或 Windows 上运行此快速入门。 本操作方法指南中的 shell 命令遵循适用于路径分隔符 "" 的 Windows 约定 `\` ，如果遵循 Linux，请确保为 "" 交换这些分隔符 `/` 。

先决条件根据操作系统而异：

### <a name="linux"></a>Linux

本快速入门假设你使用 Ubuntu Linux。 本快速入门中的步骤已使用 Ubuntu 18.04 进行了测试。

要在 Linux 上完成本快速入门，需在本地 Linux 环境上安装以下软件：

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

要在 Windows 上完成本快速入门，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负荷。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

要在本快速入门的第二部分中与示例设备进行交互，请使用 Azure IoT 资源管理器工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令以获取中心的 _IoT 中心连接字符串_ 。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 还可以使用 Azure IoT 资源管理器工具查找 IoT 中心连接字符串。

运行以下命令，获取已添加到中心的设备的设备连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>查看模型

稍后将使用 Azure IoT 浏览器在连接到 IoT 中心时查看设备。 Azure IoT 浏览器需要模型文件的本地副本，该副本与设备发送的 **模型 ID** 匹配。 通过模型文件，IoT 管理器可以显示设备实现的遥测、属性和命令。

下载下面的步骤中的代码时，它将在文件夹下包含示例模型文件 `pnpbridge/docs/schema` 。 准备 Azure IoT 浏览器：

1. 在本地计算机上创建名为 models 的文件夹。
1. 查看 [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) ，并将 JSON 文件保存到 *模型* 文件夹
1. 查看 [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) ，并将 JSON 文件保存到 " *模型* " 文件夹。

## <a name="download-the-code"></a>下载代码

在所选目录中打开命令提示符。 执行以下命令，将 [IoT 即插即用 bridge](https://aka.ms/iotplugandplaybridge) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

将 IoT 即插即用 bridge 存储库克隆到计算机后，请打开管理命令提示符，并导航到克隆的存储库的目录：

```cmd
cd pnpbridge
git submodule update --init --recursive
```

此操作需要几分钟才能完成。

>[!NOTE]
> 如果遇到 git 克隆子模块更新失败的问题，这是 Windows 文件路径和 git 的已知问题，请参阅： https://github.com/msysgit/git/pull/110 。 可以尝试以下命令来解决问题： `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>设置配置 JSON

将 IoT 即插即用桥存储库克隆到计算机后，请导航到 `pnpbridge/docs/schema` 克隆的存储库的目录，在该目录中可以找到网桥的 [配置 JSON](https://aka.ms/iot-pnp-bridge-env-config) 或 `config.json` 环境传感器示例。 可以在 [IoT 即插即用 bridge 概念文档](concepts-iot-pnp-bridge.md)中了解有关配置文件的详细信息。

对于 `root-_interface_model_id` 字段，需要复制用于标识设备型号的 IoT 即插即用型号 ID。 在此示例中，它是 `dtmi:com:example:SampleDevice;1`。 修改 "" 中的文件 **pnp_bridge_parameters** "节点下的以下参数 `config.json` ：

  使用连接字符串 (注意： symmetric_key 必须与连接字符串中的 SAS 密钥匹配) ：

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 填写完成后， `config.json` 文件应类似于：

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 生成桥后，需要将其放 `config.json` 在与网桥相同的目录中，或在运行时指定其路径。

## <a name="build-the-iot-plug-and-play-bridge"></a>构建 IoT 即插即用 bridge

导航到存储库目录中的 *pnpbridge* 文件夹。

对于 Windows，请在 [适用于 Visual Studio 的开发人员命令提示](/dotnet/framework/tools/developer-command-prompt-for-vs)中运行以下内容：

```cmd
cd scripts\windows
build.cmd
```

同样，对于 Linux，请运行以下内容：

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>在 Windows 上，可以在 Visual Studio 2019 中打开 cmake 命令生成的解决方案。 在 cmake 目录中打开 *azure_iot_pnp_bridge .sln* 项目文件，并将 *pnpbridge_bin* 项目设置为解决方案的启动项目。 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

## <a name="start-the-iot-plug-and-play-bridge"></a>启动 IoT 即插即用 bridge

 通过导航到 *pnpbridge* 文件夹并在命令提示符下运行以下命令，启动环境传感器的 IoT 即插即用 bridge 示例：

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

* [什么是 IoT 即插即用 bridge](./concepts-iot-pnp-bridge.md)
* [请参阅适用于 IoT 即插即用 bridge 的 GitHub 开发人员参考](https://aka.ms/iot-pnp-bridge-dev-doc)
* [GitHub 上的 IoT 即插即用桥](https://aka.ms/iotplugandplaybridge)