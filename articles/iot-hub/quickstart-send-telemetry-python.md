---
title: 向 Azure IoT 中心发送遥测数据 (Python) | Microsoft Docs
description: 在本快速入门中，将会运行一个示例 Python 应用程序，向 IoT 中心发送模拟遥测数据，并从 IoT 中心使用实用工具读取遥测数据。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: fc303a0ab53c80c91bb29c36a1a7e0f04c4a89e6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006872"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>快速入门：将遥测数据从设备发送到 IoT 中心并使用后端应用程序读取该数据 (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门会将模拟设备应用程序的遥测数据通过 IoT 中心发送到后端应用程序进行处理。

此快速入门使用预先编写的 Python 应用程序来发送遥测数据，使用 CLI 实用程序从中心读取遥测数据。 运行这两个应用程序前，请先创建 IoT 中心并在中心注册设备。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门中运行的示例应用程序是使用 Python 编写的。 目前，用于 Python 的 Microsoft Azure IoT SDK 仅支持每个平台的特定 Python 版本。 若要了解详细信息，请参阅 [Python SDK 自述文件](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)。

本快速入门假定你使用的是 Windows 开发计算机。 对于 Windows 系统，仅支持 [Python 3.6.x](https://www.python.org/downloads/release/python-368/)。 所选的 Python 安装程序应基于所使用的系统体系结构。 如果系统 CPU 体系结构是 32 位，则下载 x86 安装程序；对于 64 位体系结构，则下载 x86-64 安装程序。 此外，请确保为你的体系结构（x86 或 x64）安装了 [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)。

可以从 [Python.org](https://www.python.org/downloads/) 为其他平台下载 Python。

可以使用以下命令之一验证开发计算机上 Python 的当前版本：

```python
python --version
```

```python
python3 --version
```

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

从 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip 下载示例 Python 项目并提取 ZIP 存档。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    **MyPythonDevice**：这是为注册的设备提供的名称。 请按显示的方法使用 MyPythonDevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 在本地终端窗口中，导航到示例 Python 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

1. 在所选文本编辑器中打开 SimulatedDevice.py 文件。

    将 `CONNECTION_STRING` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 SimulatedDevice.py 文件。

1. 在本地终端窗口中，运行以下命令，为模拟设备应用程序安装所需的库：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 在本地终端窗口中，运行以下命令，以便运行模拟设备应用程序：

    ```cmd/sh
    python SimulatedDevice.py
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

IoT 中心 CLI 扩展可以连接到 IoT 中心上的服务端**事件**终结点。 该扩展会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

在 Azure Cloud Shell 中运行以下命令，并将 `YourIoTHubName` 替换为 IoT 中心的名称：

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

以下屏幕截图显示了在扩展接收模拟设备发送到 IoT 中心的遥测数据后的输出：

![运行后端应用程序](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，已设置 IoT 中心、注册设备、使用 Python 应用程序发送模拟遥测数据到中心并使用简单的后端应用程序读取中心的遥测数据。

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-python.md)