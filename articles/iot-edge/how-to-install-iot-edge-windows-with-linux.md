---
title: 在 Windows 上安装适用于 Linux 的 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 10、Windows Server 和 Windows IoT Core 上安装适用于 Linux 容器的 Azure IoT Edge 的说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 9e9028d0c9aeff19dc221b81defa5e2057927fa6
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034190"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>使用 Windows 上的 IoT Edge 运行 Linux 容器

使用 Windows 计算机测试适用于 Linux 设备的 IoT Edge 模块。 

在生产方案中，Windows 设备应该只运行 Windows 容器。 但是，一种常见的开发方案是使用 Windows 计算机生成适用于 Linux 设备的 IoT Edge 模块。 使用适用于 Windows 的 IoT Edge 运行时可以运行 Linux 容器以实现**测试和开发**目的。 

本文列出了在 Windows x64 (AMD/Intel) 系统上使用 Linux 容器安装 Azure IoT Edge 运行时的步骤。 若要详细了解 IoT Edge 运行时安装程序，包括有关所有安装参数的详细信息，请参阅 [在 Windows 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)。

## <a name="prerequisites"></a>先决条件

参考本部分检查你的 Windows 设备是否支持 IoT Edge，并在安装之前为容器引擎准备好该设备。 

### <a name="supported-windows-versions"></a>支持的 Windows 版本

与 Linux 容器 Azure IoT Edge 可以在满足[Docker 桌面要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的任何 Windows 版本上运行

有关最新版 IoT Edge 包含的功能的详细信息，请参阅 [Azure IoT Edge 发行说明](https://github.com/Azure/azure-iotedge/releases)。

若要在虚拟机上安装 IoT Edge，请启用嵌套虚拟化并分配至少 2-GB 内存。 如何启用嵌套虚拟化取决于所用的虚拟机监控程序。 就 Hyper-V 来说，第 2 代虚拟机已默认启用嵌套虚拟化。 如果使用 VMWare，则可通过切换开关在虚拟机上启用此功能。 

### <a name="prepare-the-container-engine"></a>准备容器引擎 

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器引擎。 在 Windows 计算机上运行 Windows 与 Linux 容器的最大配置差别在于，IoT Edge 安装包含 Windows 容器运行时，但在安装 IoT Edge 之前，需要提供自己的 Linux 容器运行时。 

若要设置一台 Windows 计算机用于开发和测试适用于 Linux 设备的容器，可以使用 [Docker Desktop](https://www.docker.com/docker-windows) 作为容器引擎。 在安装 IoT Edge 之前，需要安装 Docker 并将其配置为[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。  

如果 IoT Edge 设备是 Windows 计算机，请检查它是否符合 Hyper-V 的[系统要求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。

## <a name="install-iot-edge-on-a-new-device"></a>在新设备上安装 IoT Edge

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

某个 PowerShell 脚本将下载并安装 Azure IoT Edge 安全守护程序。 然后，安全守护程序将启动两个运行时模块中的第一个，即 IoT Edge 代理，以便能够远程部署其他模块。 

首次在设备上安装 IoT Edge 运行时时，需要使用 IoT 中心内的标识预配该设备。 可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，需要设置多个设备时，这种做法非常有用。 

可以在[在 Windows 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)一文中详细了解不同的安装选项和参数。 为 Linux 容器安装并配置 Docker Desktop 后，主要安装差别是使用 **-ContainerOs** 参数声明 Linux。 例如： 

1. 注册新的 IoT Edge 设备并检索设备连接字符串（如果尚未这样做）。 复制连接字符串，以便稍后在本部分中使用。 可以使用以下工具完成此步骤：

   * [Azure 门户](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. 以管理员身份运行 PowerShell。

   >[!NOTE]
   >使用 PowerShell 的 AMD64 会话安装 IoT Edge，不要使用 PowerShell (x86)。 如果不确定您使用的是什么会话类型，请运行以下命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-IoTEdge** 命令检查 Windows 计算机是否使用了支持的版本，启用容器功能，然后下载 moby 运行时（不是用于 Linux 容器）和 IoT Edge 运行时。 该命令默认使用 Windows 容器，因此会将 Linux 声明为所需的容器操作系统。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 此时，IoT Core 设备可能会自动重启。 其他 Windows 10 或 Windows Server 设备可能会提示你重启。 如果是这样，请立即重启设备。 设备准备就绪后，再次以管理员身份运行 PowerShell。

5. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用设备连接字符串进行手动预配。 再次将 Linux 声明为所需的容器操作系统。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 出现提示时，请提供在步骤 1 中检索到的设备连接字符串。 该设备连接字符串会将物理设备与 IoT 中心内的设备 ID 相关联。 

   该设备连接字符串采用以下格式（不包括引号）：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>验证是否成功安装

检查 IoT Edge 服务的状态。 该服务应列为“正在运行”。  

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出正在运行的模块。 完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。 首次[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后, 其他系统模块**edgeHub**也会在设备上启动。 


```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正常安装 IoT Edge，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
