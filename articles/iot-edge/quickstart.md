---
title: "快速入门 Azure IoT Edge + Windows | Microsoft Docs"
description: "通过在模拟边缘设备上运行分析来试用 Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 16d9431ec0a0425c8433557fe384864bb599bf1f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>快速入门：将第一个 IoT Edge 模块从 Azure 门户部署到 Windows 设备 - 预览

此快速入门中，使用 Azure IoT Edge 云接口将预生成的代码远程部署到 IoT Edge 设备。 若要完成此任务，首先使用 Windows 设备模拟 IoT Edge 设备，然后即可向其部署模块。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户][lnk-account]。

## <a name="prerequisites"></a>先决条件

本教程假设使用运行 Windows 的计算机或虚拟机来模拟物联网设备。 如果正在虚拟机中运行 Windows，则启用[嵌套虚拟化][lnk-nested]并分配至少 2GB 内存。 

1. 确保使用的是支持的 Windows 版本：
   * Windows 10 
   * Windows Server
2. 安装[适用于 Windows 的 Docker][lnk-docker] 并确保其正在运行。
3. 安装 [Windows 上的 Python 2.7][lnk-python] 并确保可以使用 pip 命令。
4. 运行以下命令，下载 IoT Edge 控制脚本。

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge 可运行 Windows 容器或 Linux 容器。 要使用 Windows 容器，必须在基于 x64 的设备上运行：
>    * Windows 10 Fall Creators Update，或
>    * Windows Server 1709（内部版本 16299），或
>    * Windows IoT Core（内部版本 16299）
>
> 对于 Windows IoT Core，请按照[在 Windows IoT Core 上安装 IoT Edge 运行时][lnk-install-iotcore]中的说明进行操作。 否则，只能[将 Docker 配置为使用 Windows 容器][lnk-docker-containers]，并根据需要使用以下 PowerShell 命令验证先决条件：
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

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

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>配置 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它由两个模块组成。 首先，IoT Edge 代理协助部署和监视 IoT Edge 设备上的模块。 其次，IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 IoT 中心之间的通信。 

使用上一节的 IoT Edge 设备连接字符串配置运行时。

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

启动运行时。

```cmd
iotedgectl start
```

检查 Docker，查看 IoT Edge 代理是否正作为模块运行。

```cmd
docker ps
```

![请参阅“Docker 中的 edgeAgent”](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>部署模块

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户推送 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。 这种情况下，推送的模块创建可用于本教程的环境数据。 

在运行模拟设备的计算机上再次打开命令提示符。 确认从云中部署的模块正在 IoT Edge 设备上运行。 

```cmd
docker ps
```

![查看设备上的三个模块](./media/tutorial-simulate-device-windows/docker-ps2.png)

查看从 tempSensor 模块发送到云的消息。 

```cmd
docker logs -f tempSensor
```

![查看模块中的数据](./media/tutorial-simulate-device-windows/docker-logs.png)

还可使用 [IoT 中心资源管理器工具][lnk-iothub-explorer]查看设备正在发送的遥测。 
## <a name="clean-up-resources"></a>清理资源

如果想要删除已创建的模拟设备以及已为每个模块启动的 Docker 容器，请使用以下命令： 

```cmd
iotedgectl uninstall
```

当不再需要创建的 IoT 中心时，可以使用 [az iot hub delete][lnk-delete] 命令删除资源和与资源相关联的设备：

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>后续步骤

你已了解如何部署 IoT Edge 模块到 IoT Edge 设备。 现在，尝试将不同类型的 Azure 服务作为模块部署，以便在边缘上分析数据。 

* [将 Azure Function 作为模块部署](tutorial-deploy-function.md)
* [将 Azure 流分析作为模块部署](tutorial-deploy-stream-analytics.md)
* [将自己的代码作为模块部署](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
