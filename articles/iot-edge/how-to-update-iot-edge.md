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
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456546"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

As the IoT Edge service releases new versions, you'll want to update your IoT Edge devices for the latest features and security improvements. 本文提供有关在新版本推出时如何更新 IoT Edge 设备的信息。 

若要转移到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，只能从设备本身更新安全守护程序。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署的构造方式，可以从设备或者远程更新运行时。 

若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。 

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。 

### <a name="linux-devices"></a>Linux 设备

On Linux x64 devices, use apt-get or your appropriate package manager to update the security daemon. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

On Linux ARM32 devices, use the steps in [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) to install the latest version of the security daemon. 

### <a name="windows-devices"></a>Windows 设备

On Windows devices, use the PowerShell script to update the security daemon. The script automatically pulls the latest version of the security daemon. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Running the Update-IoTEdge command removes the security daemon from your device, along with the two runtime container images. The config.yaml file is kept on the device, as well as data from the Moby container engine (if you're using Windows containers). Keeping the configuration information means that you don't have to provide the connection string or Device Provisioning Service information for your device again during the update process. 

If you want to install a specific version of the security daemon, download the appropriate Microsoft-Azure-IoTEdge.cab file from [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases). 然后，使用 `-OfflineInstallationPath` 参数指向文件位置。 有关详细信息，请参阅[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新运行时容器

The way that you update the IoT Edge agent and IoT Edge hub containers depends on whether you use rolling tags (like 1.0) or specific tags (like 1.0.7) in your deployment. 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。 

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记： 

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.0.x 版时，就更新 1.0。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 建议在开发时使用此方法。 Azure 门户中的部署默认使用滚动更新标记。 
* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 For example, 1.0.7 won't change after its initial release. 准备好更新时，可以在部署清单中声明新的版本号。 建议在生产环境中使用此方法。

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

If you use specific tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0.7**) then all you need to do is update the tag in your deployment manifest and apply the changes to your device. 

在 Azure 门户中，运行时部署映像在“配置高级 Edge 运行时设置”部分中声明。 

![Configure advanced edge runtime settings](./media/how-to-update-iot-edge/configure-runtime.png)

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

## <a name="update-to-a-release-candidate-version"></a>Update to a release candidate version

Azure IoT Edge regularly releases new versions of the IoT Edge service. Before each stable release, there is one or more release candidate (RC) versions. RC versions include all the planned features for the release, but are still going through the testing and validation processes required for a stable release. If you want to test a new feature early, you can install the RC version and provide feedback through GitHub. 

Release candidate versions follow the same numbering convention of releases, but have **-rc** plus an incremental number appended to the end. You can see the release candidates in the same list of [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) as the stable versions. For example, find **1.0.7-rc1** and **1.0.7-rc2**, the two release candidates that came before **1.0.7**. You can also see that RC versions are marked with **pre-release** labels. 

As previews, release candidate versions aren't included as the latest version that the regular installers target. Instead, you need to manually target the assets for the RC version that you want to test. Depending on your IoT Edge device operating system, use the following sections to update IoT Edge to a specific version:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告 