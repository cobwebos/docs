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
ms.openlocfilehash: ac729963e63bd97c83719e21dad3ad2cfc9b4fee
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392788"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>在 Windows IoT Core 上安装 IoT Edge 运行时 - 预览

配合使用 Azure IoT Edge 和 [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/)，即使在小型设备上也能实现边缘计算。 Azure IoT Edge 运行时甚至可以在 IoT 行业内非常流行的小型单板计算机 (SBC) 设备上运行。 

本文逐步介绍了如何在运行 Windows IoT Core 的开发板上预配运行时。 

**目前，Windows IoT Core 仅在基于 Intel x64 的处理器上支持 Azure IoT Edge。**

## <a name="install-the-container-runtime"></a>安装容器运行时

1. 使用内部版本 17134 (RS4) IoT Core 映像配置板。 
1. 打开设备，然后[使用 PowerShell 远程登录](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)。
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

按照[本文](how-to-install-iot-edge-windows-with-windows.md)中的说明操作，安装并配置 IoT Edge 安全守护程序

## <a name="next-steps"></a>后续步骤

现在已具有一个运行 IoT Edge 运行时的设备，下面了解如何[大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)。