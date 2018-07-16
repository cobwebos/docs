---
title: 检查设备到 Azure IoT 中心的连接性
description: 在开发期间使用 IoT 中心工具排查设备连接到 IoT 中心的问题。
services: iot-hub
author: dominicbetts
manager: timlt
ms.author: dobett
ms.custom: mvc
ms.date: 05/29/2018
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: dc857760cf0d3fa2e146f22196b7bc36d119df5f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869555"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>教程：使用模拟设备测试 IoT 中心的连接性

本教程使用 Azure IoT 中心门户工具和 Azure CLI 命令来测试设备连接性。 本教程还使用简单的可以在台式机上运行的设备模拟器。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

本教程介绍如何执行以下操作：
> [!div class="checklist"]
> * 检查设备身份验证
> * 检查设备到云的连接性
> * 检查云到设备的连接性
> * 检查设备孪生同步

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

在本教程中运行的 CLI 脚本使用[适用于 Azure CLI 2.0 的 Microsoft Azure IoT 扩展](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)。 若要安装此扩展，请运行以下 CLI 命令：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

本教程中运行的设备模拟器应用程序是使用 Node.js 编写的。 开发计算机上需要有 Node.js v4.x.x 或更高版本。

可从 [nodejs.org](https://nodejs.org) 为下载适用于多个平台的 Node.js。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

从 https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip 下载示例设备模拟器 Node.js 项目并提取 ZIP 存档。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

如果已在以前的快速入门或教程中创建免费层或标准层 IoT 中心，则可跳过此步骤。

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>检查设备身份验证

设备在与中心交换数据之前，必须通过中心进行身份验证。 可以使用门户的“设备管理”部分的 **IoT 设备**工具来管理设备并检查其使用的身份验证密钥。 在教程的此部分，请添加一个新的测试设备，检索其密钥，并检查此测试设备能否连接到中心。 稍后请重置身份验证密钥，观察在设备尝试使用过期密钥时会发生什么情况。 教程的此部分使用 Azure 门户来创建、管理和监视设备以及示例 Node.js 设备模拟器。

登录门户，导航到 IoT 中心。 然后导航到“IoT 设备”工具：

![“IoT 设备”工具](media/tutorial-connectivity/iot-devices-tool.png)

若要注册新设备，请单击“+ 添加”，将“设备 ID”设置为 **MyTestDevice**，然后单击“保存”：

![添加新设备](media/tutorial-connectivity/add-device.png)

若要检索 **MyTestDevice** 的连接字符串，请在设备列表中单击它，然后复制“连接字符串-主键”的值。 连接字符串包含设备的共享访问密钥。

![检索设备连接字符串](media/tutorial-connectivity/copy-connection-string.png)

若要模拟 **MyTestDevice** 将遥测数据发送到 IoT 中心，请运行以前下载的 Node.js 模拟设备应用程序。

在开发计算机的终端窗口中，导航到下载的示例 Node.js 项目的根文件夹。 然后导航到 **iot-hub\Tutorials\ConnectivityTests\simulated-device** 文件夹。

在终端窗口中，运行以下命令以安装所需的库，并运行模拟设备应用程序。 使用在门户中添加设备时记下的设备连接字符串。

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

终端窗口在尝试连接到中心时会显示相关信息：

![模拟的正在连接的设备](media/tutorial-connectivity/sim-1-connected.png)

你现在已使用 IoT 中心生成的设备密钥成功地从设备进行了身份验证。

### <a name="reset-keys"></a>重置密钥

在此部分，请重置设备密钥，然后观察模拟的设备尝试进行连接时发生的错误。

若要重置 **MyTestDevice** 的主设备密钥，请运行以下命令：

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI 2.0
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

在开发计算机的终端窗口中，再次运行模拟的设备应用程序：

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

这一次会看到应用程序尝试进行连接时发生的身份验证错误：

![连接错误](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>生成共享访问签名 (SAS) 令牌

如果设备使用某个 IoT 中心设备 SDK，SDK 库代码会生成用于通过中心进行身份验证的 SAS 令牌。 可以通过中心名称、设备名称和设备密钥生成 SAS 令牌。

在某些情况下，例如在云协议网关中或使用自定义身份验证方案的情况下，可能需要自行生成 SAS 令牌。 若要排查 SAS 生成代码的问题，必须能够生成可以在测试过程中使用的已知良好的 SAS 令牌。

> [!NOTE]
> SimulatedDevice-2.js 示例包括使用 SDK 和不使用 SDK 生成 SAS 令牌的示例。

若要使用 CLI 生成已知良好的 SAS 令牌，请运行以下命令：

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

记下已生成的 SAS 令牌的完整文本。 SAS 令牌如下所示：`'SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307'`

在开发计算机的终端窗口中，导航到下载的示例 Node.js 项目的根文件夹。 然后导航到 **iot-hub\Tutorials\ConnectivityTests\simulated-device** 文件夹。

在终端窗口中，运行以下命令，安装所需的库，并运行模拟设备应用程序：

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

终端窗口在尝试使用 SAS 令牌连接到中心时会显示相关信息：

![模拟的正在使用令牌进行连接的设备](media/tutorial-connectivity/sim-2-connected.png)

你现在已使用 CLI 命令生成的测试性 SAS 令牌成功地从设备进行了身份验证。 **SimulatedDevice-2.js** 文件包含的示例代码演示了如何在代码中生成 SAS 令牌。

### <a name="protocols"></a>协议

设备可以使用以下任何协议连接到 IoT 中心：

| 协议 | 出站端口 |
| --- | --- |
| MQTT |8883 |
| 基于 WebSocket 的 MQTT |443 |
| AMQP |5671 |
| 基于 WebSockets 的 AMQP |443 |
| HTTPS |443 |

如果防火墙阻止了出站端口，则设备无法进行连接：

![端口被阻止](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>检查设备到云的连接性

通常情况下，设备在进行连接以后会尝试向 IoT 中心发送遥测数据。 此部分介绍如何验证设备发送的遥测数据是否已到达中心。

首先，请使用以下命令检索模拟设备的当前连接字符串：

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

若要运行可发送消息的模拟设备，请导航到已下载代码中的 **iot-hub\Tutorials\ConnectivityTests\simulated-device** 文件夹。

在终端窗口中，运行以下命令，安装所需的库，并运行模拟设备应用程序：

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

终端窗口在将遥测数据发送到中心时会显示相关信息：

![发送消息的模拟设备](media/tutorial-connectivity/sim-3-sending.png)

可以使用门户中的“指标”来验证遥测消息是否已到达 IoT 中心：

![导航到 IoT 中心指标](media/tutorial-connectivity/metrics-portal.png)

在“资源”下拉列表中选择 IoT 中心，选择“发送的遥测消息数”作为指标，然后将时间范围设置为“过去一小时”。 图表显示模拟设备发送的消息的聚合计数：

![显示 IoT 中心指标](media/tutorial-connectivity/metrics-active.png)

启动模拟设备后，指标在数分钟后才会变得可用。

## <a name="check-cloud-to-device-connectivity"></a>检查云到设备的连接性

此部分介绍如何对设备进行测试性的直接方法调用，以便检查云到设备的连接性。 请在开发计算机上运行模拟设备，以便侦听来自中心的直接方法调用。

在终端窗口中，请使用以下命令运行模拟设备应用程序：

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

使用 CLI 命令在设备上调用直接方法：

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

模拟设备在收到直接方法调用后会将一条消息列显到控制台：

![模拟设备接收直接方法调用](media/tutorial-connectivity/receive-method-call.png)

模拟设备在成功收到直接方法调用之后，会向中心发送确认：

![接收直接方法确认](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>检查孪生同步

设备使用孪生在设备和中心之间同步状态。 在此部分，请使用 CLI 命令向设备发送所需属性，以及读取设备发送的报告属性。

在此部分使用的模拟设备只要一启动就会向中心发送报告属性，并且只要一收到所需属性就会将其列显到控制台。

在终端窗口中，请使用以下命令运行模拟设备应用程序：

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

若要验证中心是否已收到设备的报告属性，请使用以下 CLI 命令：

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

在命令的输出中，可以在报告属性部分看到 **devicelaststarted** 属性。 该属性显示上次启动模拟设备的日期和时间。

![查看报告属性](media/tutorial-connectivity/reported-properties.png)

若要验证中心是否可以向设备发送所需属性值，请使用以下 CLI 命令：

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

模拟设备在收到中心的所需属性更新后会列显一条消息：

![接收所需属性](media/tutorial-connectivity/desired-properties.png)

除了即时接收所需属性更改，模拟设备还会在启动时自动检查是否存在所需属性。

## <a name="clean-up-resources"></a>清理资源

如果不再需要 IoT 中心，请在门户中删除该中心与资源组。 为此，请选择包含 IoT 中心的 **tutorials-iot-hub-rg** 资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何检查设备密钥、如何检查设备到云的连接性、如何检查云到设备的连接性，以及如何检查设备孪生同步。 若要详细了解如何监视 IoT 中心，请访问有关 IoT 中心监视的操作方法文章。

> [!div class="nextstepaction"]
> [使用诊断进行监视](iot-hub-monitor-resource-health.md)
