---
title: 通过 Azure IoT 中心更新设备固件 | Microsoft Docs
description: 使用作业和设备孪生实施设备固件更新过程。
services: iot-hub
author: dominicbetts
manager: timlt
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: bc1887ef3cdbc56732317aea15be7a618c35847e
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003570"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>教程：实施设备固件更新过程

可能需要更新连接到 IoT 中心的设备上的固件。 例如，可能需要向固件添加新功能，或者需要应用安全修补程序。 在许多 IoT 方案中，以物理方式访问设备并以手动方式对设备应用固件更新是不现实的。 本教程介绍如何通过连接到中心的后端应用程序以远程方式启动并监视固件更新过程。

为了创建并监视固件更新过程，本教程中的后端应用程序在 IoT 中心创建了一个配置。 IoT 中心[自动设备管理](iot-hub-auto-device-config.md)使用此配置在所有冷却器设备上更新一组设备孪生所需属性。 所需属性指定所需固件更新的详细信息。 冷却器设备在运行固件更新过程时，会使用设备孪生报告属性将其状态报告给后端应用程序。 后端应用程序可以使用此配置监视从设备发送的报告属性，并跟踪固件更新过程至完成：

![固件更新过程](media/tutorial-firmware-update/Process.png)

在本教程中，请完成以下任务：

> [!div class="checklist"]
> * 创建 IoT 中心并将测试设备添加到设备标识注册表。
> * 创建用于定义固件更新的配置。
> * 模拟设备上的固件更新过程。
> * 在固件更新进行过程中从设备接收状态更新。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门中运行的两个示例应用程序是使用 Node.js 编写的。 开发计算机上需要有 Node.js v4.x.x 或更高版本。

可从 [nodejs.org](https://nodejs.org) 为下载适用于多个平台的 Node.js。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

从 https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip 下载示例 Node.js 项目并提取 ZIP 存档。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

若要完成本教程，你的 Azure 订阅必须有一个 IoT 中心，其中的某个设备已添加到设备标识注册表。 在本教程中运行的模拟设备可以通过设备标识注册表中的条目连接到中心。

如果尚未在订阅中设置 IoT 中心，可使用以下 CLI 脚本设置一个。 此脚本使用 **tutorial-iot-hub** 作为 IoT 中心的名称。运行此脚本时，应将此名称替换为自己的唯一名称。 此脚本在“美国中部”区域创建资源组和中心。可以更改为更靠近自己的区域。 此脚本将检索 IoT 中心服务连接字符串。在后端示例应用程序中，请使用该连接字符串连接到 IoT 中心：

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --hub-name $hub-name -o table

```

本教程使用名为 **MyFirmwareUpdateDevice** 的模拟设备。 以下脚本将此设备添加到设备标识注册表、设置标记值，并检索其连接字符串：

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

如果在 Windows 命令提示符或 Powershell 提示符处运行这些命令，请查看 [azure-iot-cli-extension 提示](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
)页，了解如何引用 JSON 字符串。

## <a name="start-the-firmware-update"></a>启动固件更新

在后端应用程序中创建[自动设备管理配置](iot-hub-auto-device-config.md#create-a-configuration)即可在其 **devicetype** 标记为冷却器的所有设备上开始固件更新过程。 本部分介绍以下操作：

* 在后端应用程序中创建配置。
* 监视作业，直至其完成。

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>使用所需属性启动从后端应用程序进行的固件升级

若要查看用于创建配置的后端应用程序代码，请导航到已下载的示例 Node.js 项目中的 **iot-hub/Tutorials/FirmwareUpdate** 文件夹。 然后在文本编辑器中打开 ServiceClient.js 文件。

后端应用程序创建以下配置：

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

该配置包括以下节：

* `content` 指定发送到选定设备的固件所需属性。
* `metrics` 指定要运行的用于报告固件更新状态的查询。
* `targetCondition` 选择用于接收固件更新的设备。
* `priorty` 设置此配置相对于其他配置的优先级。

后端应用程序使用以下代码创建配置，以便设置所需的属性：

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

创建配置后，应用程序会监视固件更新：

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

配置报告两类指标：

* 一是系统指标，用于报告针对的设备数目以及应用了更新的设备数目。
* 二是自定义指标，由添加到配置的查询生成。 在本教程中，查询会报告在更新过程的每个阶段有多少设备。

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>响应设备上的固件升级请求

若要查看模拟设备代码（用于处理从后端应用程序发送的固件所需属性），请导航到已下载的示例 Node.js 项目中的 **iot-hub/Tutorials/FirmwareUpdate** 文件夹。 然后在文本编辑器中打开 SimulatedDevice.js 文件。

模拟设备应用程序为设备孪生中 **properties.desired.firmware** 所需属性的更新创建一个处理程序。 在处理程序中，它会在启动更新过程之前对所需属性执行一些基本的检查：

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>更新固件

**initiateFirmwareUpdateFlow** 函数运行更新。 此函数使用 **waterfall** 函数按顺序运行更新过程的各个阶段。 在此示例中，固件更新有四个阶段。 第一个阶段下载映像，第二个阶段使用校验和验证映像，第三个阶段应用映像，最后一个阶段重启设备：

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

在更新过程中，模拟设备使用报告属性报告进度：

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

以下代码片段演示下载阶段的实施。 在下载阶段，模拟设备使用报告属性将状态信息发送给后端应用程序：

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>运行示例

在此部分，请运行两个示例应用程序，以便在后端应用程序创建配置来管理模拟设备上的固件更新过程时对其进行观察。

若要运行模拟设备和后端应用程序，需要使用设备和服务连接字符串。 在本教程的开头部分创建资源时，我们已记下这些连接字符串。

若要运行模拟设备应用程序，请打开 shell 或命令提示符窗口，并导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/FirmwareUpdate** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

若要运行后端应用程序，请打开另一个 shell 或命令提示符窗口。 导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/FirmwareUpdate** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

以下屏幕截图显示模拟设备应用程序的输出，并显示它如何响应后端应用程序提供的固件所需属性更新：

![模拟设备](./media/tutorial-firmware-update/SimulatedDevice.png)

以下屏幕截图显示后端应用程序的输出，并突出显示它如何创建配置来更新固件所需属性：

![后端应用程序](./media/tutorial-firmware-update/BackEnd1.png)

以下屏幕截图显示后端应用程序的输出，并突出显示它如何监视模拟设备提供的固件更新指标：

![后端应用程序](./media/tutorial-firmware-update/BackEnd2.png)

由于 IoT 中心设备标识注册表中存在延迟，可能看不到发送到后端应用程序的每个状态更新。 也可在门户中查看这些指标，具体说来就是在 IoT 中心的“自动设备管理 -> IoT 设备配置”部分：

![在门户中查看配置](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>清理资源

如果你打算完成下一篇教程，请保留资源组和 IoT 中心，以便到时重复使用。

如果不再需要 IoT 中心，请在门户中删除该中心与资源组。 为此，请选择包含 IoT 中心的 **tutorial-iot-hub-rg** 资源组，然后单击“删除”。

或者使用 CLI：

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何针对连接的设备实施固件更新过程。 转到下一教程，了解如何使用 Azure IoT 中心门户工具和 Azure CLI 命令来测试设备连接性。

> [!div class="nextstepaction"]
[使用模拟设备测试 IoT 中心的连接](tutorial-connectivity.md)
