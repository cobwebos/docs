---
title: 如何在带有 Linux 容器的 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 在带有 Linux 容器的 Windows 上安装 Azure IoT Edge 的说明
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: c389f904fb3ea21c9bcc538a62c7a866ef3b254d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739254"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>在 Windows 上安装 Azure IoT Edge 运行时，使其与 Linux 容器一起使用

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了在 Windows x64 (AMD/Intel) 系统上使用 Linux 容器安装 Azure IoT Edge 运行时的步骤。 Windows 支持目前为预览版。

>[!NOTE]
不推荐或支持在 Windows 系统上使用 Linux 容器作为 Azure IoT Edge 的生产配置。 但可将其用于开发和测试。

## <a name="supported-windows-versions"></a>支持的 Windows 版本
使用 Linux 容器时，可在以下版本的 Windows 上使用 Azure IoT Edge 进行开发和测试：
  * Windows 10 或更高版本的桌面操作系统。
  * Windows Server 2016 或更高版本的服务器操作系统。

有关当前支持哪些操作系统的详细信息，请参阅 [Azure IoT Edge 支持](support.md#operating-systems)。 

## <a name="install-the-container-runtime"></a>安装容器运行时 

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器运行时（例如 Docker）。 

可通过[用于 Windows 的 Docker](https://www.docker.com/docker-windows) 进行开发和测试。 将用于 Windows 的 Docker 配置为[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

## <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，当需要预配多个设备时这会非常有用。 根据预配选项，选择合适的安装脚本。 

### <a name="option-1-install-and-manually-provision"></a>选项 1：安装和手动预配

1. 按照[注册新的 Azure IoT Edge 设备](how-to-register-device-portal.md)中的步骤注册设备并检索设备连接字符串。 

2. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。 

3. 下载并安装 IoT Edge 运行时。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. 当系统提示输入 DeviceConnectionString 时，请提供从 IoT 中心检索的连接字符串。 请勿对连接字符串使用引号。 

### <a name="option-2-install-and-automatically-provision"></a>选项 2：安装和自动预配

1. 按照[在 Windows上创建和预配模拟 TPM Edge 设备](how-to-auto-provision-simulated-device-windows.md)中的步骤，设置设备预配服务并检索其**范围 ID**，模拟 TPM 设备并检索其**注册 ID**，然后创建个人注册。 在 IoT 中心注册设备后，继续安装。  

   >[!TIP]
   >在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。 

2. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。 

3. 下载并安装 IoT Edge 运行时。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. 出现提示时，为预配服务和设备提供 ScopeID 和 RegistrationID。

## <a name="verify-successful-installation"></a>验证是否成功安装

可通过以下方式查看 IoT Edge 服务的状态： 

```powershell
Get-Service iotedge
```

使用下述项检查过去 5 分钟的服务日志：

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

此外，还可使用以下命令列出正在运行的模块：

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>提示和建议

如果网络具有代理服务器，请按照[配置 IoT Edge 设备以通过代理服务器进行通信](how-to-configure-proxy-support.md)中的步骤进行操作，以安装并启动 IoT Edge 运行时。

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正确安装 Edge 运行时，请参阅[故障排除](troubleshoot.md)页。
