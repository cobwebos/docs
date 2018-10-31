---
title: 将设备更新到 Azure IoT Edge 的最新版本 | Microsoft Docs
description: 如何更新 IoT Edge 设备以运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b04f909d58e1555cad9f34b682f9062bbd96cd0e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394725"
---
# <a name="update-the-iot-edge-runtime"></a>更新 IoT Edge 运行时

随着 IoT Edge 服务发布新版本，你将需要更新 IoT Edge 设备来获得最新功能和安全性改进。 本文提供有关如何在新版本可用时更新 IoT Edge 设备的信息。 

如果想要更新到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时。 目前，安全守护程序只能从设备本身更新。 第二个组件是由 Edge 中心和 Edge 代理模块组成的运行时。 根据部署方式，可以从设备或从远程更新运行时。 

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

在 Windows 设备上，使用 PowerShell 脚本卸载安全守护程序，然后重新安装。 安装脚本会自动提取安全守护程序的最新版本。 你必须在安装过程中再次为设备提供连接字符串。 

在管理员 PowerShell 会话中卸载安全守护程序。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

根据 IoT Edge 设备是使用 Windows 容器还是 Linux 容器来重新安装安全守护程序。 使用 **\<Windows or Linux\>** 短语来取代其中一个容器操作系统。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新 Edge 代理和 Edge 中心容器的方式取决于在部署中使用的是滚动标记（如 1.0）还是特定标记（如 1.0.2）。 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 Edge 中心模块的版本。 

  ![查看容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

Edge 代理和 Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法将标记与运行时映像配合使用： 

* **滚动标记**：仅使用版本号的前两个值来获取与这些数字匹配的最新映像。 例如，只要有新版本指向最新的 1.0.x 版本，就会更新 1.0。 如果 IoT Edge 设备上的容器运行时重新提取映像，运行时模块就会更新为最新版本。 建议将此方法用于开发目的。 Azure 门户中的部署默认使用滚动标记。 
* **特定标记**：使用版本号的全部三个值来明确设置映像版本。 例如，1.0.2 在初始发布后不会更改。 准备好更新时，可以在部署清单中声明新的版本号。 建议将此方法用于生产目的。

### <a name="update-a-rolling-tag-image"></a>更新滚动标记映像

如果在部署中使用滚动标记（例如，mcr.microsoft.com/azureiotedge-hub:**1.0**），则需要强制设备上的容器运行时提取映像的最新版本。 

从 IoT Edge 设备中删除映像的本地版本。 

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