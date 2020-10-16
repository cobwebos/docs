---
title: 使用 DPS 自动预配 Windows 设备 - Azure IoT Edge | Microsoft Docs
description: 使用 Windows 计算机上的模拟设备通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 56696f138fbf58993e990e263d2fa8e490672bb6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106291"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>在 Windows 上使用虚拟 TPM 创建和预配模拟 IoT Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配](../iot-dps/about-iot-dps.md#provisioning-process)概述。

DPS 在个人注册和组注册中都支持 IoT Edge 设备的对称密钥证明。 对于组注册，如果在对称密钥证明中将“是 IoT Edge 设备”选项选为 TRUE，则在该注册组下注册的所有设备都将标记为 IoT Edge 设备。

本文介绍如何使用以下步骤，在模拟 IoT Edge 设备上测试自动预配：

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Windows 计算机上创建一个模拟设备。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

> [!TIP]
> 本文介绍了如何通过在虚拟设备上使用 TPM 证明来测试自动预配，但是在使用物理 TPM 硬件时，它大部分也同样适用。

## <a name="prerequisites"></a>先决条件

* 一台 Windows 开发计算机。 本文使用 Windows 10。
* 活动的 IoT 中心。

> [!NOTE]
> 将 TPM 证明与 DPS 一起使用时，TPM 2.0 是必需的，并且只能用于创建个人（而非组）注册。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

> [!TIP]
> 如果使用的是物理 TPM 设备，则需要确定**认可密钥**，该密钥对于每个 TPM 芯片都是唯一的，并且可以从与之关联的 TPM 芯片制造商处获得。 例如，可以通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的**注册 ID**。
>
> 请按照文章[如何使用 Azure 门户管理设备注册](../iot-dps/how-to-manage-enrollments.md)中的说明在 DPS 中创建注册，然后继续执行本文中的[安装 IoT Edge 运行时](#install-the-iot-edge-runtime)部分以继续。

## <a name="simulate-a-tpm-device"></a>模拟 TPM 设备

在 Windows 开发计算机上创建模拟 TPM 设备。 检索设备的“注册 ID”和“认可密钥”，并使用它们在 DPS 中创建个人注册条目。  

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

选择要用来创建模拟设备的 SDK 语言，并遵循本文中的步骤，直到创建了个人注册为止。

创建个人注册时，请选择“True”  ，将 Windows 开发计算机上的模拟 TPM 设备声明为“IoT Edge设备”  。

> [!TIP]
> 在 Azure CLI 中，可以创建[注册](/cli/azure/ext/azure-iot/iot/dps/enrollment)或[注册组](/cli/azure/ext/azure-iot/iot/dps/enrollment-group)，并使用“支持 Edge”  标志来指定某个设备或设备组是 IoT Edge 设备。

模拟设备和个人注册指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

创建个人注册后，保存“注册 ID”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在运行模拟 TPM 的设备上安装 IoT Edge 运行时。

按照 [安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤操作，然后返回到本文来预配设备。

> [!TIP]
> 在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请使用它连接到设备预配服务和 IoT 中心所用的信息来配置设备。

1. 了解前面部分中收集的 DPS **Id 范围** 和设备 **注册 id** 。

1. 在管理员模式下打开 PowerShell 窗口。 在安装 IoT Edge 而不是 PowerShell (x86) 时，请确保使用 PowerShell 的 AMD64 会话。

1. **Deploy-IoTEdge** 命令检查 Windows 计算机是否使用了支持的版本，启用容器功能，然后下载 moby 运行时和 IoT Edge 运行时。 该命令默认使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此时，IoT Core 设备可能会自动重启。 Windows 10 或 Windows Server 设备可能会提示您重新启动。 如果是这样，请立即重启设备。 设备准备就绪后，再次以管理员身份运行 PowerShell。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时  。 该命令默认为使用 Windows 容器手动预配。 通过 `-Dps` 标志使用设备预配服务，而不是手动预配。

   请将 `{scope_id}` 和 `{registration_id}` 的占位符值替换为前面收集的数据。

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

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作
