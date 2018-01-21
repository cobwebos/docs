---
title: "快速入门 Azure IoT Edge + Linux | Microsoft Docs"
description: "通过在模拟边缘设备上运行分析来试用 Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 440b70f4d04728973d77e54e7f6303e1ad7fcd89
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>快速入门：将第一个 IoT Edge 模块部署到 Linux 或 Mac 设备 - 预览

Azure IoT Edge 将云带来的价值转移至物联网设备。 在本主题中，了解如何使用云接口将预生成的代码远程部署到 IoT Edge 设备。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户][lnk-account]。

## <a name="prerequisites"></a>系统必备

本快速入门使用计算机或虚拟机，如物联网设备。 若要将计算机转换为 IoT Edge 设备，需要以下服务：

* Python pip，用于安装 IoT Edge 运行时。
   * Linux：`sudo apt-get install python-pip`。
   * MacOS：`sudo easy_install pip`。
* Docker，用于运行 IoT Edge 模块
   * [安装适用于 Linux 的 Docker][lnk-docker-ubuntu] 并确保其正在运行。 
   * [安装适用于 Mac 的 Docker][lnk-docker-mac] 并确保其正在运行。 

## <a name="create-an-iot-hub-with-azure-cli"></a>使用 Azure CLI 创建 IoT 中心

在 Azure 订阅中创建一个 IoT 中心。 免费级的 IoT 中心适用于此快速入门。 如果过去用过 IoT 中心并且已经创建免费的中心，则可以跳过此部分，然后转到[注册 IoT Edge 设备][anchor-register]。 每个订阅仅能有一个免费 IoT 中心。 

1. 登录到 [Azure 门户][lnk-portal]。 
1. 选择“Cloud Shell”按钮。 

   ![“Cloud Shell”按钮][1]

1. 创建资源组。 以下代码在“美国西部”区域中创建名为“IoTEdge”的资源组：

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. 在新的资源组中创建 IoT 中心。 以下代码在资源组“IoTEdge”中创建名为“MyIotHub”的免费“F1”中心：

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

为模拟设备创建设备标识，以便它可以与 IoT 中心通信。 由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请从一开始就将此设备声明为 IoT Edge 设备。 

1. 在 Azure 门户中导航到 IoT 中心。
1. 选择“IoT Edge (预览版)”。
1. 选择“添加 IoT Edge 设备”。
1. 为模拟设备指定唯一设备 ID。
1. 选择“保存”以添加设备。
1. 从设备列表中选择新设备。 
1. 复制“连接字符串 - 主密钥”的值并保存。 在下一部分中配置 IoT Edge 运行时时将用到此值。 

## <a name="install-and-start-the-iot-edge-runtime"></a>安装和启动 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它由两个模块组成。 首先，IoT Edge 代理协助部署和监视 IoT Edge 设备上的模块。 其次，IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 IoT 中心之间的通信。 

在即将运行 IoT Edge 设备的计算机上，下载 IoT Edge 控件脚本：
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

使用上一节的 IoT Edge 设备连接字符串配置运行时：
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

启动运行时：
```cmd
sudo iotedgectl start
```

检查 Docker，查看 IoT Edge 代理是否正作为模块运行：
```cmd
sudo docker ps
```

![请参阅“Docker 中的 edgeAgent”](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>部署模块

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户推送 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。 这种情况下，推送的模块创建可用于本教程的环境数据。 

在运行模拟设备的计算机上再次打开命令提示符。 确认从云中部署的模块正在 IoT Edge 设备上运行：

```cmd
sudo docker ps
```

![查看设备上的三个模块](./media/tutorial-simulate-device-linux/docker-ps2.png)

查看从 tempSensor 模块发送到云的消息：

```cmd
sudo docker logs -f tempSensor
```

![查看模块中的数据](./media/tutorial-simulate-device-linux/docker-logs.png)

还可使用 [IoT 中心资源管理器工具][lnk-iothub-explorer]查看设备正在发送的遥测。 

## <a name="clean-up-resources"></a>清理资源

当不再需要创建的 IoT 中心时，可以使用 [az iot hub delete][lnk-delete] 命令删除资源和与资源相关联的设备：

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>后续步骤

你已了解如何部署 IoT Edge 模块到 IoT Edge 设备。 现在，尝试将不同类型的 Azure 服务作为模块部署，以便在边缘上分析数据。 

* [将自己的代码作为模块部署](tutorial-csharp-module.md)
* [将 Azure Function 作为模块部署](tutorial-deploy-function.md)
* [将 Azure 流分析作为模块部署](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
