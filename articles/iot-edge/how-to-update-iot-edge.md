---
title: 更新设备上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何更新 IoT Edge 设备以运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dfad3199ba3a9cd2f3bca55be50760ddde676e70
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558186"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

随着 IoT Edge 服务发布新版本，你将需要更新 IoT Edge 设备来获得最新功能和安全性改进。 本文提供有关如何在新版本可用时更新 IoT Edge 设备的信息。 

如果想要更新到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，安全守护程序只能从设备本身更新。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署方式，可以从设备或从远程更新运行时。 

>[!IMPORTANT]
>如果在 Windows 设备上运行 Azure IoT Edge，则在下述某个条件适用于设备的情况下，请勿将其更新到版本 1.0.5： 
>* 尚未将设备升级到 Windows 内部版本 17763。 IoT Edge 版本 1.0.5 不支持低于 17763 的 Windows 内部版本。
>* 在 Windows 设备上运行 Java 或 Node.js 模块。 跳过版本 1.0.5，即使已将 Windows 设备更新到最新的内部版本。 
>
>有关 IoT Edge 版本 1.0.5 的详细信息，请参阅 [1.0.5 发行说明](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)。 若要详细了解如何不让开发工具更新到最新版本，请参阅 [IoT 开发人员博客](https://aka.ms/dev-win-iot-edge-module)。


若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。 

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。 

### <a name="linux-devices"></a>Linux 设备

在 Linux 设备上，使用 apt-get 或相应的包管理器来更新安全守护程序。 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows 设备

在 Windows 设备上，使用 PowerShell 脚本卸载安全守护程序，然后重新安装。 安装脚本会自动提取安全守护程序的最新版本。 

在管理员 PowerShell 会话中卸载安全守护程序。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

在没有任何参数的情况下运行 `Uninstall-SecurityDaemon` 命令会从设备中删除安全守护程序以及两个运行时容器映像。 config.yaml 文件以及 Moby 容器引擎中的数据会保留在设备上。 保留该配置意味着，在安装过程中，不需再次为设备提供连接字符串或设备预配服务信息。 

根据 IoT Edge 设备是使用 Windows 容器还是 Linux 容器来重新安装安全守护程序。 使用 **\<Windows or Linux\>** 短语来取代其中一个容器操作系统。 使用 **-ExistingConfig** 标志即可指向设备上的现有 config.yaml 文件。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

若要安装特定版本的安全守护程序，请从 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)下载相应的 iotedged-windows.zip 文件。 然后，使用 `-OfflineInstallationPath` 参数指向文件位置。 有关详细信息，请参阅[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新 IoT Edge 代理和 IoT Edge 中心容器的方式取决于在部署中使用的是滚动标记（如 1.0）还是特定标记（如 1.0.2）。 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。 

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法将标记与运行时映像配合使用： 

* **滚动标记**：仅使用版本号的前两个值来获取与这些数字匹配的最新映像。 例如，只要有新版本指向最新的 1.0.x 版本，就会更新 1.0。 如果 IoT Edge 设备上的容器运行时重新提取映像，运行时模块就会更新为最新版本。 建议将此方法用于开发目的。 Azure 门户中的部署默认使用滚动标记。 
* **特定标记**：使用版本号的全部三个值来明确设置映像版本。 例如，1.0.2 在初始发布后不会更改。 准备好更新时，可以在部署清单中声明新的版本号。 建议将此方法用于生产目的。

### <a name="update-a-rolling-tag-image"></a>更新滚动标记映像

如果在部署中使用滚动标记（例如，mcr.microsoft.com/azureiotedge-hub:**1.0**），则需要强制设备上的容器运行时提取映像的最新版本。 

从 IoT Edge 设备中删除映像的本地版本。 在 Windows 计算机上，卸载安全守护程序时也会删除运行时映像，因此不需再次执行此步骤。 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

你可能需要使用强制标志 `-f` 来删除映像。 

IoT Edge 服务将提取运行时映像的最新版本，并自动在你的设备上重新启动它们。 

### <a name="update-a-specific-tag-image"></a>更新特定标记映像

如果在部署中使用特定标记（例如，mcr.microsoft.com/azureiotedge-hub:**1.0.2**），则只需要更新部署清单中的标记并将更改应用于你的设备即可。 

在 Azure 门户中，运行时部署映像在**配置高级 Edge 运行时设置**部分中声明。 

[配置高级 Edge 运行时设置](./media/how-to-update-iot-edge/configure-runtime.png)

在 JSON 部署清单中，更新 **systemModules** 部分中的模块映像。 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告 