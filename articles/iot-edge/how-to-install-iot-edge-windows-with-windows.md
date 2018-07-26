---
title: 如何采用 Windows 容器在 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 采用 Windows 容器在 Windows 上安装 Azure IoT Edge 的说明
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 18a1481b72904b0ac9c27e100271dc0fd0666baf
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001756"
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

**确保用于 Windows 的 Docker[ 配置为使用 Windows 容器][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

### <a name="download-the-edge-daemon-package-and-install"></a>下载 Edge 守护程序包并安装

在管理员 PowerShell 窗口中执行以下命令：

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

使用以下命令安装 vcruntime（在 IoT core Edge 设备上可以跳过此步骤）：

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

创建并启动 **iotedge** 服务：

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

为服务使用的端口添加防火墙例外：

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

创建包含以下内容的 **iotedge.reg** 文件，再双击该文件或使用 `reg import iotedge.reg` 命令将其导入到 Windows 注册表中：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>配置 Azure IoT Edge 安全守护程序

可以使用 `C:\ProgramData\iotedge\config.yaml` 处的配置文件配置守护程序。

可以使用[设备连接字符串][lnk-dcs]手动配置 edge 设备或[通过设备预配服务自动][lnk-dps]配置。

* 对于手动配置，取消注释**手动**预配模式。 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 对于自动配置，取消注释 **dps** 预配模式。 使用 IoT 中心 DPS 实例中的值更新 **scope_id** 和 **registration_id** 的值，并使用 TPM 更新 IoT Edge 设备。 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

在 PowerShell 中使用 `hostname` 命令获取边缘设备的名称，再在 configuration yaml 中将其设置为 **hostname:** 的值。 例如：

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

接下来，在配置的 **connect:** 和 **listen:** 节中为 **workload_uri** 和 **management_uri** 提供 IP 地址和端口。

若要检索 IP 地址，请在 PowerShell 窗口中输入 `ipconfig`，并复制 **vEthernet (nat)** 接口的 IP 地址，如下例所示（你的系统上的 IP 地址可能不同）：  

![nat][img-nat]

更新配置文件的 **connect:** 节中的 **workload_uri** 和 **management_uri**。 将 **\<GATEWAY_ADDRESS\>** 替换为复制的 vEthernet IP 地址。

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

在 **listen:** 节中输入相同的地址。

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

在 PowerShell 窗口中，创建包含 **management_uri** 地址的环境变量 **IOTEDGE_HOST**。

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

在重启时保留环境变量。

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

最后，确保取消注释 **moby_runtime:** 下的 **network:** 设置并将其设置为 **nat**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

保存配置文件并重启服务：

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果使用了上一部分中的**手动配置**步骤，则应在设备上成功预配并运行 IoT Edge 运行时。 如果使用了**自动配置**步骤，则需要完成一些额外的步骤，以便运行时可以代表你向 IoT 中心注册你的设备。 有关后续步骤，请参阅[在 Windows 上创建和预配模拟 TPM Edge 设备](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable)。

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

此外，还可使用以下命令列出正在运行的模块：

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

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

