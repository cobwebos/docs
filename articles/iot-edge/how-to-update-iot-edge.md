---
title: 更新设备上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何将 IoT Edge 设备更新为运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c461da44d3d9075d66a68fe8994a4e970288fca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543749"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

当 IoT Edge 服务发布新版本时，可能需要更新 IoT Edge 设备，使其获得最新功能并改善安全性。 本文提供有关在新版本推出时如何更新 IoT Edge 设备的信息。 

若要转移到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，只能从设备本身更新安全守护程序。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署的构造方式，可以从设备或者远程更新运行时。 

若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

>[!IMPORTANT]
>如果在 Windows 设备上运行 Azure IoT Edge，则在下述某个条件适用于设备的情况下，请勿将其更新到版本 1.0.5： 
>* 尚未将设备升级到 Windows 内部版本 17763。 IoT Edge 版本 1.0.5 不支持低于 17763 的 Windows 内部版本。
>* 在 Windows 设备上运行 Java 或 Node.js 模块。 跳过版本 1.0.5，即使已将 Windows 设备更新到最新的内部版本。 
>
>有关 IoT Edge 版本 1.0.5 的详细信息，请参阅 [1.0.5 发行说明](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)。 若要详细了解如何不让开发工具更新到最新版本，请参阅 [IoT 开发人员博客](https://devblogs.microsoft.com/iotdev/)。


## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。 

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。 

### <a name="linux-devices"></a>Linux 设备

在 Linux x64 设备上使用 apt-get 或相应的包管理器来更新安全守护程序。 

```bash
apt-get update
apt-get install libiothsm iotedge
```

在 Linux ARM32 设备上使用中的步骤[Linux (ARM32v7/armhf) 上的安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux-arm.md)安装安全守护程序的最新版本。 

### <a name="windows-devices"></a>Windows 设备

在 Windows 设备上，请使用 PowerShell 脚本更新安全守护程序。 脚本会自动提取最新版本的安全守护程序。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

运行 Update-IoTEdge 命令会从设备中删除安全守护程序以及两个运行时容器映像。 config.yaml 文件以及 Moby 容器引擎中的数据会保留在设备上（如果使用 Windows 容器）。 保留配置信息意味着，在更新过程中，不需再次为设备提供连接字符串或设备预配服务信息。 

若要安装特定版本的安全守护程序，请从 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)下载相应的 Microsoft-Azure-IoTEdge.cab 文件。 然后，使用 `-OfflineInstallationPath` 参数指向文件位置。 有关详细信息，请参阅[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新的 IoT Edge 代理和 IoT Edge 中心容器的方式取决于是否在你的部署中使用滚动标记 （如 1.0) 或特定标记 （如 1.0.7)。 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。 

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记： 

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.0.x 版时，就更新 1.0。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 建议在开发时使用此方法。 Azure 门户中的部署默认使用滚动更新标记。 
* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 例如，其初始发布后不会更改 1.0.7。 准备好更新时，可以在部署清单中声明新的版本号。 建议在生产环境中使用此方法。

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

如果在部署中使用特定的标记 (例如，mcr.microsoft.com/azureiotedge-hub:**1.0.7**) 则需要做的就是更新部署清单中的标记并将所做的更改应用到你的设备。 

在 Azure 门户中，运行时部署映像在“配置高级 Edge 运行时设置”部分中声明。  

![配置高级 Edge 运行时设置](./media/how-to-update-iot-edge/configure-runtime.png)

在 JSON 部署清单中，更新 **systemModules** 节中的模块映像。 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>更新到候选发布版本

Azure IoT Edge 定期发布新版本的 IoT Edge 服务。 每个稳定版本之前，没有一个或多个发布候选版本 (RC) 版本。 RC 版本包括所有计划的功能版本，但仍需通过所需的稳定版本的测试和验证进程。 如果你想要提前测试一项新功能，可以安装 RC 版本，并通过 GitHub 提供反馈。 

候选发布版本遵循相同的编号约定的版本中，但具有 **-rc**加上递增的编号追加到末尾。 您可以看到相同的列表中的候选发布[Azure IoT Edge 释放](https://github.com/Azure/azure-iotedge/releases)作为稳定版本。 例如，查找**1.0.7-rc1**并**1.0.7-rc2**，这两个版本喜欢之前的候选项**1.0.7**。 你还可以看到 RC 版本标记有**预发行版**标签。 

预览版中，作为候选发布版本不应作为最新版本包含的正则安装程序目标。 相反，您需要手动面向想要测试的 RC 版本的资产。 根据 IoT Edge 设备操作系统，使用以下部分更新到特定版本的 IoT Edge:

* [Linux X64](how-to-install-iot-edge-linux.md#install-a-specific-version)
* [Linux ARM32](how-to-install-iot-edge-linux-arm.md#install-a-specific-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告 