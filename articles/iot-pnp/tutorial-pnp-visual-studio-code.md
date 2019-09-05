---
title: 创建和测试 IoT 即插即用预览版设备 | Microsoft Docs
description: 向设备开发人员介绍如何使用 VS Code 为 IoT 即插即用预览版设备创建和测试新的设备功能模型。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: baf694fbc3176732d43094c8d20ee112af927f16
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186473"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>教程：使用 Visual Studio Code 创建和测试设备功能模型

本教程向设备开发人员介绍如何使用 Visual Studio Code 创建设备功能模型。  可以使用该模型来生成要在连接到云中 Azure IoT 中心实例的设备上运行的主干代码。

本教程中介绍如何生成该主干代码的部分假设使用的是 Windows。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建设备功能模型
> * 基于模型生成主干设备代码
> * 在生成的代码中实现存根
> * 运行代码以测试与 IoT 中心之间的交互

## <a name="prerequisites"></a>先决条件

若要使用本教程所述的设备功能模型，需要：

* [Visual Studio Code](https://code.visualstudio.com/download)：VS Code 适用于多个平台
* VS Code 中的 Azure IoT Device Workbench 扩展。 使用以下步骤在 VS Code 中安装 Azure IoT Device Workbench 扩展：

    1. 在 VS Code 中选择“扩展”选项卡。 
    1. 搜索 **Azure IoT Device Workbench**。
    1. 选择“安装”  。

若要生成本教程所述的在 Windows 上生成的 C 代码，需要：

* [Visual Studio（社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/)- 安装 Visual Studio 时，请确保包括“NuGet 包管理器”组件和“使用 C++ 的桌面开发”工作负荷。  
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)
* Azure IoT C SDK 的本地副本：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

若要测试本教程所述的设备代码，需要：

* [Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer/releases)。
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>为设备建模

使用数字孪生定义语言创建设备功能模型。  模型通常包括多个接口定义文件和一个模型文件。  **适用于 VS Code 的 Azure IoT Device Workbench 扩展**包含一些可帮助你创建和编辑这些 JSON 文件的工具。

### <a name="create-the-interface-file"></a>创建接口文件

若要创建用于在 VS Code 中定义 IoT 设备功能的接口文件：

1. 创建名为 **devicemodel** 的文件夹。

1. 启动 VS Code，按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用:   创建接口”命令。

1. 浏览到并选择创建的 **devicemodel** 文件夹。

1. 输入 **EnvironmentalSensor** 作为接口名称，然后按 **Enter**。 VS Code 将创建名为 **EnvironmentalSensor.interface.json** 的示例接口文件。

1. 将此文件的内容替换为以下 JSON，并将 `@id` 字段中的 `{your name}` 替换为唯一值。 请仅使用字符 a-z、A-Z、0-9 和下划线。 有关详细信息，请参阅[数字孪生标识符格式](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)。 要将接口保存到存储库中，其接口 ID 必须唯一：

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/Interface.json"
    }
    ```

    此接口定义了设备属性（例如“客户名称”）、遥测类型（例如“温度”）和命令（例如 **turnon**）。  

1. 在此接口文件的末尾添加名为 **blink** 的命令功能。 添加命令之前请务必添加一个逗号。 尝试键入定义，以了解 Intellisense、自动完成和验证功能如何帮助你编辑接口定义：

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. 保存文件。

### <a name="create-the-model-file"></a>创建模型文件

模型文件指定 IoT 即插即用设备实现的接口。 一个模型通常至少包含两个接口 - 用于定义设备特定功能的一个或多个接口，以及所有 IoT 即插即用设备必须实现的一个标准接口。

若要在 VS Code 中创建用于指定 IoT 即插即用设备实现的接口的模型文件：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用:   创建功能模型”命令。 输入 **SensorboxModel** 作为模型名称。 VS Code 将创建名为 **SensorboxModel.capabilitymodel.json** 的示例接口文件。

1. 请将此文件的内容替换为以下 JSON，并将 `@id` 字段和 `EnvironmentalSensor` 接口中的 `{your name}` 替换为在 **EnvironmentalSensor.interface.json** 文件中使用的相同值。 要将接口保存到存储库中，其接口 ID 必须唯一：

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    该模型定义一个设备用于实现 **EnvironmentalSensor** 接口和标准的 **DeviceInformation** 接口。

1. 保存文件。

### <a name="download-the-deviceinformation-interface"></a>下载 DeviceInformation 接口

在基于模型生成主干代码之前，必须先创建公共模型存储库中 **DeviceInformation** 的本地副本。  该公共模型存储库已包含 **DeviceInformation** 接口。

若要使用 VS Code 从公共模型存储库下载 **DeviceInformation** 接口：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，选择“打开模型存储库”命令，然后选择“打开公共模型存储库”。   

1. 选择“接口”，选择 ID 为 `urn:azureiot:DeviceManagement:DeviceInformation:1` 的设备信息接口，然后选择“下载”。  

现已获得构成设备功能模型的三个文件：

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>发布模型

要使 Azure IoT 资源管理器工具能够读取你的设备功能模型，需要将其发布到公司存储库中。 若要从 VS Code 发布，需要获取公司存储库的连接字符串：

1. 导航到 [Azure IoT 认证门户](https://aka.ms/ACFI)。

1. 使用 Microsoft 工作帐户登录到该门户。 

1. 依次选择“公司存储库”、“连接字符串”。  

1. 复制该连接字符串。

若要在 VS Code 中打开公司存储库：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用:   打开模型存储库”命令。

1. 选择“打开组织模型存储库”，并粘贴连接字符串。 

1. 按 **Enter** 打开公司存储库。

若要将设备功能模型和接口发布到公司存储库：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用:   将文件提交到模型存储库”命令。

1. 选择“EnvironmentalSensor.interface.json”和“SensorboxModel.capabilitymodel.json”文件，然后选择“确定”。   

现在，你的文件已存储到公司存储库中。

## <a name="generate-code"></a>生成代码

可以使用**适用于 VS Code 的 Azure IoT Device Workbench 扩展**基于模型生成主干 C 代码。 若要在 VS Code 中生成主干代码：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用:   生成设备代码存根”命令。

1. 选择“SensorboxModel.capabilitymodel.json”功能模型文件。 

1. 输入 **sensorbox_app** 作为项目名称。

1. 选择“ANSI C”作为语言。 

1. 选择“CMake 项目”作为目标。 

1. 选择“通过 IoT 中心设备连接字符串”作为连接方式。 

VS Code 将生成主干 C 代码，并将 **sensorbox_app** 文件夹中的文件保存到 **modelcode** 文件夹中。 VS Code 将打开一个新窗口，其中包含生成的代码文件。

## <a name="update-the-generated-code"></a>更新生成的代码

在生成并运行代码之前，需要实现存根属性、遥测和命令。

若要在 VS Code 中提供存根代码的实现：

1. 打开 **SensorboxModel_impl.c** 文件。

1. 添加用于实现存根函数的代码。

1. 保存所做更改。

## <a name="build-the-code"></a>生成代码

运行用于在 Azure IoT 中心测试 IoT 即插即用设备的代码之前，需要编译该代码。

在 Windows 上，请遵照 **sensorbox_app** 文件夹中的 **Readme.md** 文件中的说明生成并运行代码。 以下部分说明了如何检索运行设备代码时要使用的设备连接字符串。

## <a name="test-the-code"></a>测试代码

运行该代码时，它会连接到 IoT 中心并开始发送示例遥测和属性值。 设备还会响应从 IoT 中心发送的命令。 若要验证此行为：

1. 若要创建 IoT 中心：

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. 将设备连接到 IoT 中心并检索其连接字符串：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    记下连接字符串。

1. 在命令提示符下，导航到在其中生成了 SDK 和示例的 **azure-iot-sdk-c** 文件夹。 然后导航到 **cmake\\sensorbox_app\\Release** 文件夹。

1. 运行以下命令：

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. 使用 Azure IoT 资源管理器工具来与连接到 IoT 中心的 IoT 即插即用设备交互。 有关详细信息，请参阅[安装和使用 Azure IoT 资源管理器](./howto-install-iot-explorer.md)。

## <a name="next-steps"></a>后续步骤

了解如何构建可供认证的 IoT 即插即用设备后，接下来请了解：

> [!div class="nextstepaction"]
> [构建可供认证的设备](tutorial-build-device-certification.md)
