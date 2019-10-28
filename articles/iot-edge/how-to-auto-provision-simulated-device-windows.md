---
title: 通过 DPS Azure IoT Edge 自动预配 Windows 设备 |Microsoft Docs
description: 使用 Windows 计算机上的模拟设备通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5842d6fcb5f03754fc8f5922e299d0d9c30d21db
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900835"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>使用 Windows 上的虚拟 TPM 创建和预配模拟 IoT Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

对于单个注册和组注册中的 IoT Edge 设备，DPS 支持对称密钥证明。 对于组注册，如果在对称密钥证明中选中 "IoT Edge 设备" 选项为 true，则在该注册组中注册的所有设备将标记为 "IoT Edge 设备"。 

本文说明如何使用以下步骤测试模拟 IoT Edge 设备上的自动预配：

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Windows 计算机上创建一个模拟设备。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

> [!NOTE]
> 将 TPM 证明与 DPS 结合使用时，TPM 2.0 是必需的，并且只能用于创建个人、非组、注册。

> [!TIP]
> 本文介绍如何通过在虚拟设备上使用 TPM 证明来测试自动预配，但在使用物理 TPM 硬件时也适用。

## <a name="prerequisites"></a>必备组件

* 一台 Windows 开发计算机。 本文使用 Windows 10。
* 活动的 IoT 中心。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。

> [!TIP]
> 如果你使用的是物理 TPM 设备，则需要确定**签署密钥**，该密钥对于每个 tpm 芯片都是唯一的，并从与其关联的 tpm 芯片制造商那里获得。 例如，你可以通过创建认可密钥的 256 SHA-1 哈希，为你的 TPM 设备派生唯一的**注册 ID** 。
>
> 按照[如何在 Azure 门户中管理设备](../iot-dps/how-to-manage-enrollments.md)注册一文中的说明在 DPS 中创建你的注册，然后继续阅读本文中的[安装 IoT Edge 运行时](#install-the-iot-edge-runtime)部分以继续。

## <a name="simulate-a-tpm-device"></a>模拟 TPM 设备

在 Windows 开发计算机上创建模拟 TPM 设备。 检索设备的**注册 ID**和**认可密钥**，并使用它们在 DPS 中创建单个注册条目。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。

选择要用来创建模拟设备的 SDK 语言，并遵循本文中的步骤，直到创建了个人注册为止。

创建单个注册时，请选择 " **True** " 以声明 Windows 开发计算机上的模拟 TPM 设备是**IoT Edge 设备**。

模拟设备和个人注册指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

创建个人注册后，保存“注册 ID”的值。 配置 IoT Edge 运行时时，需要使用此值。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。

预配设备时，需要以下信息：

* DPS **ID 范围**值
* 你创建的设备**注册 ID**

在运行模拟 TPM 的设备上安装 IoT Edge 运行时。 将 IoT Edge 运行时配置为自动进行，而不是手动设置。

> [!TIP]
> 在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。

有关在 Windows 上安装 IoT Edge 的详细信息，包括管理容器和更新 IoT Edge 等任务的先决条件和说明，请参阅[在 Windows 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)。

1. 在管理员模式下打开 PowerShell 窗口。 安装 IoT Edge 而不是 PowerShell （x86）时，请务必使用 PowerShell 的 AMD64 会话。

1. **IoTEdge**命令检查 Windows 计算机是否在受支持的版本上，打开容器功能，然后下载小鲸鱼运行时和 IoT Edge 运行时。 命令默认为使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此时，IoT 核心设备可能会自动重新启动。 其他 Windows 10 或 Windows Server 设备可能会提示您重新启动。 如果是这样，请立即重新启动设备。 设备准备就绪后，请再次以管理员身份运行 PowerShell。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。 使用 `-Dps` 标志来使用设备预配服务，而不是手动预配。

   将 `{scope_id}` 和 `{registration_id}` 的占位符值替换为先前收集的数据。

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
