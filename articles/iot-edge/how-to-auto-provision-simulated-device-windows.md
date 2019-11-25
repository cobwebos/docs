---
title: Automatically provision Windows devices with DPS - Azure IoT Edge | Microsoft Docs
description: 使用 Windows 计算机上的模拟设备通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457137"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Create and provision a simulated IoT Edge device with a virtual TPM on Windows

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

DPS supports symmetric key attestation for IoT Edge devices in both individual enrollment and group enrollment. For group enrollment, if you check “is IoT Edge device” option to be true in symmetric key attestation, all the devices that are registered under that enrollment group will be marked as IoT Edge devices. 

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Windows 计算机上创建一个模拟设备。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes testing auto-provisioning by using TPM attestation on virtual devices, but much of it applies when using physical TPM hardware as well.

## <a name="prerequisites"></a>必备组件

* 一台 Windows 开发计算机。 本文使用 Windows 10。
* 活动的 IoT 中心。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。

> [!TIP]
> If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.
>
> Follow the instructions in the article [How to manage device enrollments with Azure Portal](../iot-dps/how-to-manage-enrollments.md) to create your enrollment in DPS and then proceed with the [Install the IoT Edge runtime](#install-the-iot-edge-runtime) section in this article to continue.

## <a name="simulate-a-tpm-device"></a>模拟 TPM 设备

在 Windows 开发计算机上创建模拟 TPM 设备。 Retrieve the **Registration ID** and **Endorsement key** for your device, and use them to create an individual enrollment entry in DPS.

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。

选择要用来创建模拟设备的 SDK 语言，并遵循本文中的步骤，直到创建了个人注册为止。

When you create the individual enrollment, select **True** to declare that the simulated TPM device on your Windows development machine is an **IoT Edge device**.

模拟设备和个人注册指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

创建个人注册后，保存“注册 ID”的值。 配置 IoT Edge 运行时时，需要使用此值。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。

You'll need the following information when provisioning your device:

* The DPS **ID Scope** value
* The device **Registration ID** you created

Install the IoT Edge runtime on the device that is running the simulated TPM. You'll configure the IoT Edge runtime for automatic, not manual, provisioning.

> [!TIP]
> 在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。

For more detailed information about installing IoT Edge on Windows, including prerequisites and instructions for tasks like managing containers and updating IoT Edge, see [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

1. 在管理员模式下打开 PowerShell 窗口。 Be sure to use an AMD64 session of PowerShell when installing IoT Edge, not PowerShell (x86).

1. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。 Use the `-Dps` flag to use the Device Provisioning Service instead of manual provisioning.

   Replace the placeholder values for `{scope_id}` and `{registration_id}` with the data you collected earlier.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。 在设备上使用以下命令验证是否已成功安装并启动运行时。  

检查 IoT Edge 服务的状态。

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出正在运行的模块。

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作
