---
title: 使用 DPS 自动预配 Azure IoT Edge 设备 - Windows | Microsoft Docs
description: 使用 Windows 计算机上的模拟设备通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 46970d5628df3b46ec88df998a328928f60e15b4
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090225"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>在 Windows 上创建和预配模拟 TPM Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。 

本文介绍如何使用以下步骤，在模拟的 Edge 设备上测试自动预配： 

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Windows 计算机上创建一个模拟设备。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

## <a name="prerequisites"></a>先决条件

* 一台 Windows 开发计算机。 本文使用 Windows 10。 
* 活动的 IoT 中心。 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。 

## <a name="simulate-a-tpm-device"></a>模拟 TPM 设备

在 Windows 开发计算机上创建模拟 TPM 设备。 检索设备的“注册 ID”和“认可密钥”，并使用它们在 DPS 中创建个人注册条目。 

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。 

选择要用来创建模拟设备的 SDK 语言，并遵循本文中的步骤，直到创建了个人注册为止。 

创建个人注册时，请选择“启用”，将此虚拟机声明为“IoT Edge 设备”。

模拟设备和个人注册指南： 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

创建个人注册后，保存“注册 ID”的值。 配置 IoT Edge 运行时时，需要使用此值。 

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在运行 Windows 的设备上，可以选择使用 Windows 容器或 Linux 容器。 选择要使用的容器类型，然后遵循本文中的步骤。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。 

遵照说明在运行上一部分所述的模拟 TPM 的设备上安装 IoT Edge 运行时。 

在开始学习这些文章之前，请了解 DPS 的“ID 范围”和设备的“注册 ID”。 

* [Windows 容器](how-to-install-iot-edge-windows-with-windows.md)
* [Linux 容器](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>创建 TPM 环境变量

在运行模拟设备的计算机上，修改 **iotedge** 服务注册表以设置环境变量。

1. 从“开始”菜单打开 **regedit**。 
2. 导航到 **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**。 
3. 选择“编辑” > “新建” > “多字符串值”。 
4. 输入名称 **Environment**。 
5. 双击新变量，并将值数据设置为 **IOTEDGE_USE_TPM_DEVICE = ON**。 
6. 单击“确定”  保存更改。 

## <a name="restart-the-iot-edge-runtime"></a>重启 IoT Edge 运行时

重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果成功启动了运行时，则可以转到 IoT 中心，查看新设备是否已自动预配并已准备好运行 IoT Edge 模块。 

检查 IoT Edge 服务的状态。

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

列出正在运行的模块。

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作