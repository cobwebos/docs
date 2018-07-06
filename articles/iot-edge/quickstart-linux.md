---
title: 快速入门 Azure IoT Edge + Linux | Microsoft Docs
description: 本快速入门介绍如何将预生成的代码远程部署到 IoT Edge 设备。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 27e5b7fed227248d9d60c8ede460c9ecc65ca52d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096268"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>快速入门：将第一个 IoT Edge 模块部署到 Linux x64 设备

Azure IoT Edge 将云带来的价值转移至物联网设备。 本快速入门介绍如何使用云接口将预生成的代码远程部署到 IoT Edge 设备。

此快速入门介绍如何：

1. 创建 IoT 中心。
2. 将 IoT Edge 设备注册到 IoT 中心。
3. 在设备上安装并启动 IoT Edge 运行时。
4. 以远程方式将模块部署到 IoT Edge 设备。

![快速入门体系结构][2]

本快速入门可以将 Linux 计算机或虚拟机变成 IoT Edge 设备。 然后即可将模块从 Azure 门户部署到设备。 在本快速入门中部署的模块为模拟传感器，可以生成温度、湿度和压强数据。 其他 Azure IoT Edge 教程均以本教程中通过部署模块（这些模块通过分析模拟数据来获得业务见解）执行的操作为基础。 

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户][lnk-account]。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

请使用 Azure CLI 完成本快速入门中的多个步骤。Azure IoT 有一个可以启用其他功能的扩展。 

将 Azure IoT 扩展添加到 Cloud Shell 实例。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

在 Azure 门户中创建 IoT 中心，启动快速入门。
![创建 IoT 中心][3]

免费级的 IoT 中心适用于此快速入门。 如果曾经用过 IoT 中心并且已创建免费的中心，则可使用该 IoT 中心。 每个订阅仅能有一个免费 IoT 中心。 

1. 在 Azure Cloud Shell 中创建一个资源组。 以下代码在“美国西部”区域创建名为“TestResources”的资源组。 将快速入门和教程的所有资源置于一个组中可以对其集中管理。 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. 在新的资源组中创建 IoT 中心。 以下代码在资源组“TestResources”中创建免费的“F1”中心。 将 *{hub_name}* 替换为 IoT 中心的唯一名称。

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用新创建的 IoT 中心注册 IoT Edge 设备。
![注册设备][4]

为模拟设备创建设备标识，以便它可以与 IoT 中心通信。 由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请从一开始就将此设备声明为 IoT Edge 设备。 

1. 在 Azure Cloud Shell 中输入以下命令，以便在中心创建名为 **myEdgeDevice** 的设备。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. 检索设备的连接字符串，该字符串将物理设备与其在 IoT 中心的标识链接在一起。 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. 复制并保存连接字符串。 在下一部分中配置 IoT Edge 运行时时将用到此值。 


## <a name="install-and-start-the-iot-edge-runtime"></a>安装和启动 IoT Edge 运行时

在设备上安装并启动 Azure IoT Edge 运行时。 
![注册设备][5]

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 每次某个 Edge 设备在启动后通过启动 IoT Edge 代理来启动此设备时，**IoT Edge 安全守护程序**就会启动。 **IoT Edge 代理**协助部署和监视 IoT Edge 设备（包括 IoT Edge 中心）的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。 

### <a name="register-your-device-to-use-the-software-repository"></a>注册设备，以便使用软件存储库

运行 IoT Edge 运行时所需的包在软件存储库中托管。 将 IoT Edge 设备配置为访问此存储库。 

此部分的步骤适用于运行 **Ubuntu 16.04** 的设备。 若要访问其他版本的 Linux 上的软件存储库，请参阅[在 Linux (x64) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)或[在 Linux (ARM32v7/armhf) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux-arm.md)。

1. 在要用作 IoT Edge 设备的计算机上，安装存储库配置。

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. 安装用于访问存储库的公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>安装容器运行时

IoT Edge 运行时是一组容器，而部署到 IoT Edge 设备的逻辑则以容器形式打包。 通过安装容器运行时，针对这些组件准备设备。

更新 **apt-get**。

   ```bash
   sudo apt-get update
   ```

安装 Moby（容器运行时）及其 CLI 命令。 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>安装并配置 IoT Edge 安全守护程序

此安全守护程序作为系统服务安装，因此，每次设备启动时，IoT Edge 运行时就会启动。 安装还包括一个 **hsmlib** 版本，该版本允许安全守护程序与设备的硬件安全交互。 

1. 下载并安装 IoT Edge 安全守护程序。 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. 打开 IoT Edge 配置文件。 它是一个受保护的文件，因此可能需要使用提升的权限才能访问它。
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. 添加在注册设备时复制的 IoT Edge 设备连接字符串。 替换此前在本快速入门中复制的变量 **device_connection_string** 的值。

4. 重启 Edge 安全守护程序：

   ```bash
   sudo systemctl restart iotedge
   ```

5. 查看 Edge 安全守护程序是否正作为系统服务运行：

   ```bash
   sudo systemctl status iotedge
   ```

   ![查看作为系统服务运行的 Edge 守护程序](./media/quickstart-linux/iotedged-running.png)

   也可通过运行以下命令来查看 Edge 安全守护程序提供的日志：

   ```bash
   journalctl -u iotedge
   ```

6. 查看在设备上运行的模块： 

   ```bash
   sudo iotedge list
   ```

   先注销再登录以后，上述命令就不需要 *sudo*。

   ![查看设备上的一个模块](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>部署模块

从云端管理 Azure IoT Edge 设备，部署将遥测数据发送到 IoT 中心的模块。
![注册设备][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户推送 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。 这种情况下，推送的模块创建可用于本教程的环境数据。 

在运行模拟设备的计算机上再次打开命令提示符。 确认从云中部署的模块正在 IoT Edge 设备上运行：

   ```bash
   sudo iotedge list
   ```
   先注销再登录以后，上述命令就不需要 *sudo*。

   ![查看设备上的三个模块](./media/quickstart-linux/iotedge-list-2.png)

查看从 tempSensor 模块发送的消息：

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
先注销再登录以后，上述命令就不需要 *sudo*。

![查看模块中的数据](./media/quickstart-linux/iotedge-logs.png)

如果在日志中看到的最后一行是 `Using transport Mqtt_Tcp_Only`，则说明温度传感器模块可能正等着连接到 Edge 中心。 尝试终止该模块，然后让 Edge 代理重启它。 可以使用 `sudo docker stop tempSensor` 命令来终止它。

也可使用 [IoT 中心资源管理器工具][lnk-iothub-explorer]或 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看设备正发送的遥测数据。 


## <a name="clean-up-resources"></a>清理资源

若要继续学习 IoT Edge 教程，可以使用在本快速入门中注册并设置的设备。 若要从设备中删除这些安装，请使用以下命令。  

删除 IoT Edge 运行时。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

删除在设备上创建的容器。 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

删除容器运行时。

   ```bash
   sudo apt-get remove --purge moby
   ```

如果不再需要所创建的 Azure 资源，则可使用以下命令删除已创建的资源组及其关联的任何资源：

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>后续步骤

本快速入门是所有 IoT Edge 教程的先决条件。 可以继续查看任何其他教程，了解 Azure IoT Edge 如何帮助将这些数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 Azure Functions 筛选传感器数据](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
