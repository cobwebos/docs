---
title: "在 IoT Core 上安装 Azure IoT Edge | Microsoft Docs"
description: "在 Windows IoT Core 设备上安装 Azure IoT Edge 运行时"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>在 Windows IoT Core 上安装 IoT Edge 运行时 - 预览

Azure IoT Edge 运行时甚至可以在 IoT 行业内非常流行的小型单板计算机 (SBC) 设备上运行。 本文介绍在运行 Windows IoT Core 的 [MinnowBoard Turbot][lnk-minnow] 开发板上预配运行时。

## <a name="install-the-runtime"></a>安装运行时

1. 在主机系统上安装 [Windows 10 IoT Core 仪表板][lnk-core]。
1. 安装[设置设备][lnk-board]中的步骤使用 MinnowBoard Turbot/MAX Build 16299 映像配置板。 
1. 打开设备，然后[使用 PowerShell 远程登录][lnk-powershell]。
1. 在 PowerShell 控制台中，[安装 Docker 二进制文件][lnk-docker-install]。
1. 在 PowerShell 控制台中运行以下命令，安装 IoT Edge 运行时并验证配置：

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   此脚本提供以下内容： 
   * Python 3.6
   * IoT Edge 控件脚本 (iotedgectl.exe)

在远程 PowerShell 窗口中可能会看见 iotedgectl.exe 工具中的信息输出为红色。 这不一定表示错误。 

## <a name="next-steps"></a>后续步骤

现在已具有一个运行 IoT Edge 运行时的设备，下面了解如何[大规模部署和监视 IoT Edge 模块][lnk-deploy]。

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers