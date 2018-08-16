---
title: 如何采用 Windows 容器在 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 采用 Windows 容器在 Windows 上安装 Azure IoT Edge 的说明
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: 39e0de6b378ed61ab375c6468b58c8c4a87b5fb9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575958"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>在 Windows 上安装 Azure IoT Edge 运行时，以将其与 Windows 容器一起使用

Azure IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 **IoT Edge 安全守护程序**提供和维护 Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 代理来启动设备。 **IoT Edge 代理**协助部署和监视 Edge 设备（包括 IoT Edge 中心）上的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。

本文列出了在 Windows x64 (AMD/Intel) 系统上安装 Azure IoT Edge 运行时的步骤。 

Windows 支持目前为预览版。

## <a name="supported-windows-versions"></a>支持的 Windows 版本
采用 Windows 容器的 Azure IoT Edge 可以与以下项一起使用：
  * Windows 10/IoT 企业版/IoT 核心板，带 2018 年 4 月份更新（内部版本 17134）
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>安装容器运行时 

>[!NOTE]
>要在 Windows IoT 核心版上安装容器引擎，请执行[预配 IoT 核心版设备][lnk-iot-core]一文中的步骤，然后继续根据以下说明进行操作。

Azure IoT Edge 依赖于 [OCI 兼容的][lnk-oci]容器运行时（例如 Docker）。 可通过[用于 Windows 的 Docker][lnk-docker-for-windows] 进行开发和测试。 

将用于 Windows 的 Docker 配置为[使用 Windows 容器][lnk-docker-config]。

## <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，当需要预配多个设备时这会非常有用。 根据预配选项，选择合适的安装脚本。 

### <a name="install-and-manually-provision"></a>安装和手动预配

1. 按照[注册新的 Azure IoT Edge 设备][lnk-dcs]中的步骤注册设备并检索设备连接字符串。 

2. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。 

3. 下载并安装 IoT Edge 运行时。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. 当系统提示输入 DeviceConnectionString 时，请提供从 IoT 中心检索的连接字符串。 请勿对连接字符串使用引号。 

### <a name="install-and-automatically-provision"></a>安装和自动预配

1. 按照[在 Windows上创建和预配模拟 TPM Edge 设备][lnk-dps]中的步骤，设置设备预配服务并检索其范围 ID，模拟 TPM 设备并检索其注册 ID，然后创建个人注册。 在 IoT 中心注册设备后，继续安装。  

   >[!TIP]
   >在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。 

2. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。 

3. 下载并安装 IoT Edge 运行时。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. 出现提示时，为预配服务和设备提供 ScopeID 和 RegistrationID。 

## <a name="verify-successful-installation"></a>验证是否成功安装

可通过以下方式查看 IoT Edge 服务的状态： 

```powershell
Get-Service iotedge
```

使用以下命令观察过去 5 分钟的服务日志：

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

## <a name="next-steps"></a>后续步骤

预配 IoT Edge 设备和安装运行后，现在可以[部署 IoT Edge 模块][lnk-modules]。

如果在正确安装 Edge 运行时期间遇到问题，请查看[疑难解答][lnk-trouble]页面。


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md
[lnk-modules]: how-to-deploy-modules-portal.md
