---
title: 从 Azure IoT 中心同步设备状态 | Microsoft Docs
description: 使用设备孪生在设备与 IoT 中心之间同步状态
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: 808cfd1f4d47f49be60c3f45278d2334ba720c49
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865176"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>教程：从后端服务配置设备

在从设备接收遥测数据的同时，你可能需要从后端服务配置设备。 将所需的配置发送到设备时，还可能需要从这些设备接收状态与符合性更新。 例如，可以设置设备的目标工作温度范围，或者从设备收集固件版本信息。

若要在设备与 IoT 中心之间同步状态信息，请使用设备孪生。 [设备孪生](iot-hub-devguide-device-twins.md)是与特定设备关联的 JSON 文档，由云中的 IoT 中心存储，可在 IoT 中心对其进行[查询](iot-hub-devguide-query-language.md)。 设备孪生包含所需属性、报告属性和标记。 所需属性由后端应用程序设置，由设备读取。 报告属性由设备设置，由后端应用程序读取。 标记由后端应用程序设置，永远不会发送到设备。 使用标记可以组织设备。 本教程介绍如何使用所需属性和报告属性来同步状态信息：

![孪生摘要](media/tutorial-device-twins/DeviceTwins.png)

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 创建 IoT 中心并将测试设备添加到标识注册表。
> * 使用所需属性将状态信息发送到模拟设备。
> * 使用报告属性从模拟设备接收状态信息。

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

若要完成本教程，你的 Azure 订阅必须包含一个 IoT 中心，其中的某个设备已添加到设备标识注册表。 在本教程中运行的模拟设备可以通过设备标识注册表中的条目连接到中心。

如果尚未在订阅中设置 IoT 中心，可使用以下 CLI 脚本设置一个。 此脚本使用 **tutorial-iot-hub** 作为 IoT 中心的名称。运行此脚本时，应将此名称替换为自己的唯一名称。 此脚本在“美国中部”区域创建资源组和中心。可以更改为更靠近自己的区域。 此脚本将检索 IoT 中心服务连接字符串，在后端示例中，我们将使用该连接字符串连接到 IoT 中心：

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku S1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --hub-name $hubname -o table

```

本教程使用名为 **MyTwinDevice** 的模拟设备。 以下脚本将此设备添加到标识注册表，并检索其连接字符串：

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>发送状态信息

使用所需属性将状态信息从后端应用程序发送到设备。 本部分介绍以下操作：

* 在设备上接收并处理所需属性。
* 从后端应用程序发送所需属性。

若要查看接收所需属性的模拟设备示例代码，请导航到下载的示例 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后在文本编辑器中打开 SimulatedDevice.js 文件。

以下部分描述了在模拟设备上运行的、对发送自后端应用程序的所需属性更改做出响应的代码：

### <a name="retrieve-the-device-twin-object"></a>检索设备孪生对象

以下代码使用设备连接字符串连接到 IoT 中心：

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

以下代码从客户端对象中获取孪生：

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>所需属性示例

可以使用对应用程序有利的任何方式来构建所需属性。 本示例使用一个名为 **fanOn** 的顶级属性，并将剩余的属性分组到单独的**组件**中。 以下 JSON 片段显示了本教程所用的所需属性的结构：

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>创建处理程序

可针对所需属性更新创建处理程序，用于响应 JSON 层次结构中的不同级别发生的更新。 例如，此处理程序可以监视从后端应用程序发送到设备的所有所需属性更改。 **delta** 变量包含从解决方案后端发送的所需属性：

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

以下处理程序仅响应对 **fanOn** 所需属性所做的更改：

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>多个属性的处理程序

在前面显示的所需属性 JSON 示例中，**components** 下的 **climate** 节点包含两个属性：**minTemperature** 和 **maxTemperature**。

设备的本地 **twin** 对象存储一组完整的所需属性和报告属性。 从后端发送的 **delta** 可能只会更新所需属性的某个子集。 在以下代码片段中，如果模拟设备只是收到了对某个 **minTemperature** 和 **maxTemperature** 的更新，则它会使用另一个值的本地孪生中的值来配置设备：

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

本地 **twin** 对象存储一组完整的所需属性和报告属性。 从后端发送的 **delta** 可能只会更新所需属性的某个子集。

### <a name="handle-insert-update-and-delete-operations"></a>处理插入、更新和删除操作

从后端发送的所需属性并不会指示当前正在对特定的所需属性执行哪个操作。 代码需要根据当前存储在本地的所需属性集，以及中心发送的更改来推断操作。

以下片段演示模拟设备如何处理针对所需属性中的**组件**列表执行的插入、更新和删除操作。 可以查看如何使用 **null** 值来指示应删除某个组件：

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>从后端向设备发送所需属性

前面已介绍设备如何实现处理程序来接收所需属性的更新。 本部分介绍如何将所需属性更改从后端应用程序发送到设备。

若要查看接收所需属性的模拟设备示例代码，请导航到下载的示例 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后在文本编辑器中打开 ServiceClient.js 文件。

以下代码片段演示如何连接到设备标识注册表并访问特定设备的孪生：

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

以下片段演示后端应用程序发送到设备的不同所需属性补丁：

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

以下片段演示后端应用程序如何将所需属性更新发送到设备：

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>运行应用程序

在本部分，我们将运行两个示例应用程序，以观察后端应用程序如何将所需属性更新发送到模拟设备应用程序。

若要运行模拟设备和后端应用程序，需要使用设备和服务连接字符串。 在本教程的开头部分创建资源时，我们已记下这些连接字符串。

若要运行模拟设备应用程序，请打开 shell 或命令提示符窗口，并导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

若要运行后端应用程序，请打开另一个 shell 或命令提示符窗口。 导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

以下屏幕截图显示模拟设备应用程序的输出，并突出显示它如何处理对 **maxTemperature** 所需属性做出的更新。 可以看到顶级处理程序和气候组件处理程序的运行方式：

![模拟设备](./media/tutorial-device-twins/SimulatedDevice1.png)

以下屏幕截图显示后端应用程序的输出，并突出显示它如何发送对 **maxTemperature** 所需属性做出的更新：

![后端应用程序](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>接收状态信息

后端应用程序从设备接收报告属性形式的状态信息。 设备会设置报告属性，并将其发送到中心。 后端应用程序可以从中心内存储的设备孪生读取报告属性的当前值。

### <a name="send-reported-properties-from-a-device"></a>从设备发送报告属性

可以补丁的形式发送对报告属性值所做的更新。 以下片段演示了模拟设备发送的补丁的模板。 模拟设备先更新补丁中的字段，然后将补丁发送到中心：

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

模拟设备使用以下函数将包含报告属性的补丁发送到中心：

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>处理报告属性

后端应用程序通过设备孪生访问设备的当前报告属性值。 以下片段演示后端应用程序如何读取模拟设备的报告属性值：

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>运行应用程序

在本部分，我们将运行两个示例应用程序，以观察模拟设备应用程序如何将报告属性更新发送到后端应用程序。

运行的两个示例应用程序，与前面在查看如何将所需属性发送到设备时所运行的应用程序相同。

若要运行模拟设备和后端应用程序，需要使用设备和服务连接字符串。 在本教程的开头部分创建资源时，我们已记下这些连接字符串。

若要运行模拟设备应用程序，请打开 shell 或命令提示符窗口，并导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

若要运行后端应用程序，请打开另一个 shell 或命令提示符窗口。 导航到下载的 Node.js 项目中的 **iot-hub/Tutorials/DeviceTwins** 文件夹。 然后运行以下命令：

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

以下屏幕截图显示模拟设备应用程序的输出，并突出显示它如何将报告属性更新发送到中心：

![模拟设备](./media/tutorial-device-twins/SimulatedDevice2.png)

以下屏幕截图显示后端应用程序的输出，并突出显示它如何从设备接收和处理报告属性更新：

![后端应用程序](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>清理资源

如果你打算完成下一篇教程，请保留资源组和 IoT 中心，以便到时重复使用。

如果不再需要 IoT 中心，请在门户中删除该中心与资源组。 为此，请选择包含 IoT 中心的 **tutorial-iot-hub-rg** 资源组，然后单击“删除”。

或者使用 CLI：

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何在设备与 IoT 中心之间同步状态信息。 请继续学习下一篇教程，了解如何使用设备孪生实现固件更新过程。

> [!div class="nextstepaction"]
[实现设备固件更新过程](tutorial-firmware-update.md)
