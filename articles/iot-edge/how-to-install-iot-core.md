---
title: 在 IoT Core 上安装 Azure IoT Edge | Microsoft Docs
description: 在 Windows IoT Core 设备上安装 Azure IoT Edge 运行时
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575992"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>在 Windows IoT Core 上安装 IoT Edge 运行时 - 预览

配合使用 Azure IoT Edge 和 [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/)，即使在小型设备上也能实现边缘计算。 Azure IoT Edge 运行时甚至可以在 IoT 行业内非常流行的小型单板计算机 (SBC) 设备上运行。 

本文逐步介绍了如何在运行 Windows IoT Core 的开发板上预配运行时。 

**目前，Windows IoT Core 仅在基于 Intel x64 的处理器上支持 Azure IoT Edge。**

## <a name="install-the-container-runtime"></a>安装容器运行时

1. 使用内部版本 17134 (RS4) IoT Core 映像配置板。 
1. 打开设备，然后[使用 PowerShell 远程登录][lnk-powershell]。
1. 在 PowerShell 控制台中，安装容器运行时： 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >此容器运行时来自 Moby 项目生成服务器，仅用于评估目的。 它未经过 Docker 测试、认可或支持。

## <a name="finish-installing"></a>完成安装

按照[这篇文章][lnk-install-windows-on-windows]中的说明操作，安装并配置 IoT Edge 安全守护程序

## <a name="next-steps"></a>后续步骤

现在已具有一个运行 IoT Edge 运行时的设备，下面了解如何[大规模部署和监视 IoT Edge 模块][lnk-deploy]。

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
