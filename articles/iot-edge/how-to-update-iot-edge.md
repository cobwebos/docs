---
title: 更新设备上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何将 IoT Edge 设备更新为运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e29420b8e029a7e1723f969753eb2081381a8e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434159"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

随着 IoT Edge 服务发布新版本，你将需要更新 IoT Edge 设备，以获取最新功能和安全改进。 本文提供有关在新版本推出时如何更新 IoT Edge 设备的信息。

若要转移到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，只能从设备本身更新安全守护程序。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署的构造方式，可以从设备或者远程更新运行时。

若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。

### <a name="linux-devices"></a>Linux 设备

在 Linux x64 设备上，使用 apt 或适当的程序包管理器更新安全守护程序。

```bash
apt-get update
apt-get install libiothsm iotedge
```

在 Linux ARM32 设备上，使用在[linux 上安装 Azure IoT Edge 运行时（ARM32v7/armhf）](how-to-install-iot-edge-linux-arm.md)中的步骤来安装最新版本的安全守护程序。

### <a name="windows-devices"></a>Windows 设备

在 Windows 设备上，使用 PowerShell 脚本更新安全守护程序。 脚本会自动提取最新版本的安全守护程序。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

运行 IoTEdge 命令将从你的设备中删除和更新安全守护程序，以及两个运行时容器映像。 Yaml 文件保存在设备上，以及来自小鲸鱼容器引擎（如果使用的是 Windows 容器）的数据。 保留配置信息意味着无需在更新过程中再次为设备提供连接字符串或设备预配服务信息。

如果要安装安全守护程序的特定版本，请从[IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)下载相应的 Microsoft-Azure-IoTEdge 文件。 然后，使用 `-OfflineInstallationPath` 参数指向文件位置。 有关详细信息，请参阅[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新 IoT Edge 代理和 IoT Edge 中心容器的方式取决于是否在部署中使用滚动标记（如1.0）或特定标记（如1.0.7）。

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记：

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.0.x 版时，就更新 1.0。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 建议在开发时使用此方法。 Azure 门户中的部署默认使用滚动更新标记。

* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 例如，1.0.7 不会在其初始版本后更改。 准备好更新时，可以在部署清单中声明新的版本号。 建议在生产环境中使用此方法。

### <a name="update-a-rolling-tag-image"></a>更新滚动更新标记映像

如果在部署中使用滚动更新标记（例如 mcr.microsoft.com/azureiotedge-hub:**1.0**），则需要在设备上强制实施容器运行时，以提取最新版本的映像。

从 IoT Edge 设备中删除本地版本的映像。 在 Windows 计算机上，卸载安全守护程序时也会删除运行时映像，因此不需再次执行此步骤。

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

可能需要使用 `-f`（强制）标志来删除映像。

IoT Edge 服务将提取最新版本的运行时映像，并自动在设备上将其重新启动。

### <a name="update-a-specific-tag-image"></a>更新特定标记映像

如果在部署中使用特定标记（例如，mcr.microsoft.com/azureiotedge-hub：**1.0.8**），只需更新部署清单中的标记，并将更改应用到设备。

1. 在 Azure 门户的 IoT 中心中，选择 IoT Edge 设备，然后选择 "**设置模块**"。

1. 在**IoT Edge 模块**"部分中，选择"**运行时设置**"。

   ![配置运行时设置](./media/how-to-update-iot-edge/configure-runtime.png)

1. 在 "**运行时设置**" 中，用所需的版本更新**Edge 中心**的**映像**值。 不要选择 "**保存**"。

   ![更新边缘中心映像版本](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. 折叠**Edge 中心**设置，或向下滚动，并更新具有相同所需版本的**edge 代理**的 "**映像**" 值。

   ![更新 Edge 中心代理版本](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. 选择“保存”。

1. 选择 "查看" 和 "**创建**"，查看部署，然后选择 "**创建**"。

## <a name="update-to-a-release-candidate-version"></a>更新为候选发布版本

Azure IoT Edge 会定期发布 IoT Edge 服务的新版本。 在每个稳定版本之前，都有一个或多个候选发布（RC）版本。 RC 版本包含版本的所有计划功能，但仍要经历稳定版本所需的测试和验证过程。 如果要及早测试新功能，可以安装 RC 版本，并通过 GitHub 提供反馈。

候选发布版本遵循相同的版本编号约定，**但会在**末尾追加递增数字。 可以在与稳定版本相同的[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)列表中查看候选发布。 例如，查找**1.0.7-rc1**和**1.0.7-rc2**，在**1.0.7**之前提供两个候选发布版本。 你还可以看到 RC 版本标记有**预发布**标签。

作为预览，候选发布版本不包含为常规安装程序目标的最新版本。 相反，你需要为要测试的 RC 版本手动定位资产。 根据 IoT Edge 设备操作系统，使用以下部分将 IoT Edge 更新到特定版本：

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告