---
title: 更新设备上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何将 IoT Edge 设备更新为运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113303"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

当 IoT Edge 服务发布新版本时，可能需要更新 IoT Edge 设备，使其获得最新功能并改善安全性。 本文提供有关在新版本推出时如何更新 IoT Edge 设备的信息。

若要转移到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，只能从设备本身更新安全守护程序。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署的构造方式，可以从设备或者远程更新运行时。

若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。

### <a name="linux-devices"></a>Linux 设备

在 Linux x64 设备上，请使用 apt-get 或相应的包管理器将安全守护程序更新到最新版本。

```bash
apt-get update
apt-get install libiothsm iotedge
```

若要更新到安全守护程序的特定版本，请在 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中查找目标版本。 在该版本中，找到设备的相应 **libiothsm-std** 和 **iotedge** 文件。 右键单击每个文件对应的链接，并复制链接地址。 使用链接地址安装这些组件的特定版本：

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows 设备

在 Windows 设备上，请使用 PowerShell 脚本更新安全守护程序。 脚本会自动提取最新版本的安全守护程序。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

运行 Update-IoTEdge 命令会从设备中删除并更新安全守护程序以及两个运行时容器映像。 config.yaml 文件以及 Moby 容器引擎中的数据会保留在设备上（如果使用 Windows 容器）。 保留配置信息意味着，在更新过程中，不需再次为设备提供连接字符串或设备预配服务信息。

若要更新到安全守护程序的特定版本，请在 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中查找目标版本。 在该版本中，下载 **Microsoft-Azure-IoTEdge.cab** 文件。 然后，使用 `-OfflineInstallationPath` 参数指向本地文件位置。 例如：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` 参数将在提供的目录中查找名为 **Microsoft-Azure-IoTEdge.cab** 的文件。 从 IoT Edge 版本 1.0.9-rc4 开始，可以使用两个 .cab 文件，一个用于 AMD64 设备，另一个用于 ARM32。 下载适用于你的设备的正确文件，然后重命名该文件以删除体系结构后缀。

有关更新选项的详细信息，请使用命令 `Get-Help Update-IoTEdge -full`，或参考[所有安装参数](how-to-install-iot-edge-windows.md#all-installation-parameters)。

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新 IoT Edge 代理和 IoT Edge 中心容器的方式取决于在部署中使用的是滚动标记（如 1.0）还是特定标记（如 1.0.7）。

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记：

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.0.x 版时，就更新 1.0。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 建议在开发时使用此方法。 Azure 门户中的部署默认使用滚动更新标记。

* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 例如，1.0.7 在其初始版本发布后不会更改。 准备好更新时，可以在部署清单中声明新的版本号。 建议在生产环境中使用此方法。

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

如果在部署中使用特定标记（例如 mcr.microsoft.com/azureiotedge-hub:**1.0.8**），则只需更新部署清单中的标记，并将更改应用到设备即可。

1. 在 Azure 门户的 IoT 中心，选择 IoT Edge 设备，然后选择“设置模块”  。

1. 在“IoT Edge 模块”部分中，选择“运行时设置”   。

   ![配置运行时设置](./media/how-to-update-iot-edge/configure-runtime.png)

1. 在”运行时设置”中，将“Edge 中心”的“映像”值更新为所需的版本    。 暂时不要选择“保存”  。

   ![更新 Edge 中心的映像版本](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. 折叠“Edge 中心”设置，或向下滚动，将“Edge 代理”的“映像”值更新为所需的相同版本    。

   ![更新 Edge 中心的代理版本](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. 选择“保存”。 

1. 选择“查看 + 创建”，检查部署，然后选择“创建”   。

## <a name="update-offline-or-to-a-specific-version"></a>脱机或特定版本更新

如果你想要脱机更新设备，或者更新到特定版本的 IoT Edge 而不是最新版本，则可以使用`-OfflineInstallationPath`参数执行此操作。

用于更新 IoT Edge 设备的两个组件：

* 一个 PowerShell 脚本，其中包含安装说明
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序（iotedged）、小鲸鱼容器引擎和小鲸鱼 CLI

1. 有关最新的 IoT Edge 安装文件以及旧版本，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 找到要安装的版本，并从发行说明的 "**资产**" 部分将以下文件下载到 IoT 设备：

   * IoTEdgeSecurityDaemon
   * Microsoft-Azure-IoTEdge-amd64 发布1.0.9 或更高版本，或 Microsoft-Azure-IoTEdge 版本1.0.8 及更早版本。

   Microsoft-Azure-IotEdge-arm32 仅在1.0.9 中提供，以供测试之用。 Windows ARM32 设备目前不支持 IoT Edge。

   使用与你使用的 .cab 文件相同的版本中的 PowerShell 脚本非常重要，因为此功能将更改为支持每个版本中的功能。

3. 如果下载的 .cab 文件在其上有体系结构后缀，请将该文件重命名为**Microsoft-Azure-IoTEdge**。

4. 若要用脱机组件进行更新，请用[点](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing)作为 PowerShell 脚本的本地副本。 然后，在`Update-IoTEdge`命令`-OfflineInstallationPath`中使用参数，并提供文件目录的绝对路径。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>更新到候选发布版本

Azure IoT Edge 定期发布新版 IoT Edge 服务。 在发布每个稳定版本之前，会有一个或多个候选发布 (RC) 版本。 RC 版本包含发布的所有计划功能，但仍在进行测试和验证。 如果要及早测试新功能，可以安装 RC 版本，并通过 GitHub 提供反馈。

候选发布版本遵循相同的版本编号约定，但会在末尾追加 **-rc** 和一个增量数字。 可以在与稳定版本相同的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)列表中查看候选发布版本。 例如，可以找到 **1.0.7-rc1** 和 **1.0.7-rc2** 这两个在 **1.0.7** 之前发布的候选发布版本。 还可以看到 RC 版本带有**预发行版**标签。

IoT Edge 代理和中心模块具有使用相同约定标记的 RC 版本。 例如， **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**。

充当预览版的候选发布版本不会包括在常规安装程序所针对的最新版本中。 需要手动将要测试的 RC 版资产设为目标。 大多数情况下，安装或更新到 RC 版本与面向任何其他特定版本的 IoT Edge 的情况相同。

使用本文中的部分来了解如何将 IoT Edge 设备更新到特定版本的安全守护程序或运行时模块。

如果要在新计算机上安装 IoT Edge，请使用以下链接了解如何根据设备操作系统安装特定版本：

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告
