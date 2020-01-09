---
title: 生成可供认证的 IoT 即插即用预览版设备 | Microsoft Docs
description: 本教程向设备开发人员介绍如何生成可供认证的 IoT 即插即用预览版设备。
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 43fc928b1274159839dc0df395e86d065f84b4c7
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550259"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>生成可供认证的 IoT 即插即用预览版设备

本教程介绍设备开发人员如何生成可供认证的 IoT 即插即用预览版设备。

认证测试将会检查：

- IoT 即插即用设备代码是否已安装在设备上。
- IoT 即插即用设备代码是否是使用 Azure IoT SDK 生成的。
- 设备代码是否支持 [Azure IoT 中心设备预配服务](../iot-dps/about-iot-dps.md)。
- 设备代码是否实现设备信息接口。
- 功能模型和设备代码可在 IoT Central 正常运行。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

- [Visual Studio Code](https://code.visualstudio.com/download)
- [适用于 VS Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 扩展包

还需要使用在以下教程中创建的 IoT 即插即用设备：[快速入门：使用设备功能模型创建设备](quickstart-create-pnp-device-windows.md)。

## <a name="store-a-capability-model-and-interfaces"></a>存储功能模型和接口

对于 IoT 即插即用设备，必须创建一个功能模型，以及用于以 JSON 文件形式定义设备功能的接口。

可将这些 JSON 文件存储到三个不同的位置：

- 公共模型存储库。
- 公司模型存储库。
- 设备。

目前，若要认证设备，必须将这些文件存储到公司模型存储库或公共模型存储库。

## <a name="include-the-required-interfaces"></a>包含所需的接口

若要通过认证，必须在功能模型中包含并实现**设备信息**接口。 此接口具有以下标识：

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> 如果已完成[快速入门：使用设备功能模型创建设备](quickstart-create-pnp-device-windows.md)，则已在模型中包含**设备信息**接口。

若要在设备模型中包括**设备信息**接口，请将接口 ID 添加到功能模型的 `implements` 属性：

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

若要在 VS Code 中查看**设备信息**接口：

1. 按 **Ctrl+Shift+P** 打开命令面板。

1. 输入“即插即用”，然后选择“IoT 即插即用: 打开模型存储库”命令。   选择“打开公共模型存储库”。  公共模型存储库将在 VS Code 中打开。

1. 在公共模型存储库中选择“接口”选项卡，选择筛选器图标，然后在筛选器字段中输入“设备信息”。  

1. 若要创建**设备信息**接口的本地副本，请在筛选列表中选择该接口，然后选择“下载”。  VS Code 将显示接口文件。

若要使用 Azure CLI 查看**设备信息**接口，请执行以下操作：

1. [安装 Azure CLI 扩展](howto-install-pnp-cli.md)。

1. 使用以下 Azure CLI 命令显示设备信息接口 ID 所对应的接口：

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

有关详细信息，请参阅[安装和使用适用于 Azure CLI 的 Azure IoT 扩展](howto-install-pnp-cli.md)。

## <a name="update-device-code"></a>更新设备代码

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>通过 Azure IoT 设备预配服务 (DPS) 启用设备预配

若要认证设备，它必须支持通过 [Azure IoT 设备预配服务 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) 进行预配。 若要添加 DPS 功能，可以在 VS Code 中生成 C 代码存根。 执行以下步骤:

1. 在 VS Code 中打开包含 DCM 文件的文件夹，按 **Ctrl+Shift+P** 打开命令面板，输入“IoT 即插即用”，然后选择“生成设备代码存根”。  

1. 选择用于生成设备代码存根的 DCM 文件。

1. 输入项目名称，即设备应用程序的名称。

1. 选择“ANSI C”作为语言。 

1. 选择“通过 DPS (设备预配服务)对称密钥”作为连接方法。 

1. 选择“Windows 上的 CMake 项目”  或“Linux 上的 CMake 项目”  作为项目模板，具体取决于设备的操作系统。

1. VS Code 将打开一个新窗口，其中包含生成的设备代码存根文件。

1. 生成代码后，输入 DPS 凭据（**DPS ID 范围**、**DPS 对称密钥**、**设备 Id**）作为应用程序的参数。 若要从证书门户获取凭据，请参阅[连接并测试 IoT 即插即用设备](tutorial-certification-test.md#connect-and-discover-interfaces)。

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>实现标准接口

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>实现模型信息和 SDK 信息接口

Azure IoT 设备 SDK 将实现模型信息和 SDK 信息接口。 如果在 VS Code 中使用代码生成函数，则设备代码将使用 IoT 即插即用设备 SDK。

如果你选择不使用 Azure IoT 设备 SDK，则可以使用 SDK 源代码作为自己的实现的参考。

#### <a name="implement-the-device-information-interface"></a>实现设备信息接口

在设备上实现**设备信息**接口，并在运行时提供设备特定的信息。

可以使用适用于 [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) 的**设备信息**接口示例实现作为参考。

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>实现模型中定义的所有功能

在认证过程中，将以编程方式测试设备，以确保它实现其接口中定义的功能。 如果设备未实现这些功能，系统将使用 HTTP 状态代码 501 对读写属性和命令请求做出响应。

## <a name="next-steps"></a>后续步骤

生成可供认证的 IoT 即插即用设备后，接下来：

> [!div class="nextstepaction"]
> [了解如何认证设备](tutorial-certification-test.md)
