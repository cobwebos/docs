---
title: 对 Windows Azure 来宾代理进行故障排除
description: 对 Windows Azure 来宾代理不起作用的问题进行故障排除
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 738c2a240ad6c88186357e69b02d33b40d366d7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977059"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>对 Windows Azure 来宾代理进行故障排除

Microsoft Azure 来宾代理是一个虚拟机 (VM) 代理。 它使虚拟机能够与 IP 地址 168.63.129.16 上的结构控制器（VM 所在的基础物理服务器）通信。 这是一个虚拟的公共 IP 地址，便于通信。 有关详细信息，请参阅[什么是 IP 地址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

 从本地迁移到 Azure 的 VM 或使用自定义映像创建的 VM 未安装 Windows Azure 来宾代理。 在这些情况下，必须手动安装 VM 代理。 有关如何安装 VM 代理的详细信息，请参阅 [Azure 虚拟机代理概述](../extensions/agent-windows.md)。

成功安装 Windows Azure 来宾代理后，你可以在 VM 上的 services.msc 中看到下列服务：
 
- Windows Azure 来宾代理服务
- 遥测服务
- RDAgent 服务

**Windows Azure 来宾代理服务**：此服务是负责 WAppAgent.log 中所有日志记录的服务。 此服务负责配置从来宾到主机的各种扩展和通信。 

**遥测服务**：此服务负责将 VM 的遥测数据发送到后端服务器。

**RDAgent 服务**：这项服务负责来宾代理的安装。 透明安装程序也是 RDAgent 的一个组件，可以帮助升级来宾代理的其他组件和服务。 RDAgent 还负责将检测信号从来宾 VM 发送到物理服务器上的主机代理。

> [!NOTE]
> 从 VM 来宾代理的版本 2.7.41491.971 开始，RDAgent 服务中包含了遥测组件，因此，你可能在新创建的 VM 中看不到此遥测服务。

## <a name="checking-agent-status-and-version"></a>检查代理状态和版本

转到 Azure 门户中的 VM 属性页，检查“代理状态”。 如果 Microsoft Azure 来宾代理运行正常，则状态显示为“就绪”。 如果 VM 代理处于 " **未就绪** " 状态，则 Azure 门户上的 "扩展" 和 " **运行" 命令** 将不起作用。

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>对处于“未就绪”状态的 VM 代理进行故障排除

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>步骤 1 检查是否安装了 Windows Azure 来宾代理服务

- 检查包

    找到 C:\WindowsAzure 文件夹。 如果看到显示版本号的 GuestAgent 文件夹，则表示已在 VM 上安装了 Microsoft Azure 来宾代理。 你还可以查找已安装的包。  如果 VM 上安装了 Windows Azure 来宾代理，相关包会保存在以下位置：`C:\windows\OEM\GuestAgent\VMAgentPackage.zip`。
    
    可以运行以下 PowerShell 命令以检查是否已将 VM 代理部署到 VM：
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    在输出中，找到“ProvisionVMAgent”属性，并检查该值是否设置为“True” 。 如果是，则表示已在 VM 上安装代理。
    
- 检查服务和进程

   转到服务控制台 (services.msc) 并检查以下服务的状态：Windows Azure 来宾代理服务、RDAgent 服务、Windows Azure 遥测服务和 Windows Azure 网络代理服务。
 
    还可以通过检查任务管理器的以下进程来检查这些服务是否正在运行：
       
    - WindowsAzureGuestAgent.exe:Windows Azure 来宾代理服务
    - WaAppAgent.exe:RDAgent 服务
    - WindowsAzureNetAgent.exe:Windows Azure 网络代理服务
    - WindowsAzureTelemetryService.exe:Windows Azure 遥测服务

   如果找不到这些进程和服务，则表示你未安装 Microsoft Azure 来宾代理。

- 检查程序和功能

    请在“控制面板”中转到“程序和功能”，确定是否已安装 Windows Azure 来宾代理服务。

如果找不到任何正在运行的包、服务和进程，甚至看不到在“程序和功能”下安装的 Windows Azure 来宾代理，请尝试[安装 Windows Azure 来宾代理服务](../extensions/agent-windows.md)。 如果来宾代理未正确安装，你可以[脱机安装 VM 代理](./install-vm-agent-offline.md)。

如果可以看到服务且服务正在运行，请重启该服务以查看问题是否已解决。 如果服务已停止，请启动它们并等待几分钟。 然后检查“代理状态”是否报告为“就绪” 。 如果发现这些服务崩溃，某些第三方进程可能会导致这些服务崩溃。 若要进一步排查这些问题，请联系 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="step-2-check-whether-auto-update-is-working"></a>步骤 2 检查自动更新是否正常工作

Microsoft Azure 来宾代理具有自动更新功能。 它将自动检查并安装新的更新。 如果自动更新功能无法正常工作，请尝试使用以下步骤卸载并安装 Windows Azure 来宾代理：

1. 如果“程序和功能”中显示了 Windows Azure 来宾代理，请将其卸载。

2. 打开具有管理员权限的命令提示符窗口。

3. 停止来宾代理服务。 如果服务没有停止，则必须将服务设置为“手动启动”，然后重启 VM。
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. 删除来宾代理服务：
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. 在 `C:\WindowsAzure` 下创建一个名为 OLD 的文件夹。

1. 将名为 Packages 或 GuestAgent 的任何文件夹都移到 OLD 文件夹中。

1. 从[此处](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409)下载并安装代理安装包的最新版本。 必须拥有管理员权限才能完成安装。

1. 使用以下命令安装来宾代理：

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    然后检查来宾代理服务是否正确启动。
 
    在来宾代理无法正确安装的少数情况下，你可以[脱机安装 VM 代理](./install-vm-agent-offline.md)。
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>步骤 3 检查 VM 是否可以连接到结构控制器

使用 PsPing 等工具测试 VM 是否可以连接到端口 80、32526 和 443 上的 168.63.129.16。 如果 VM 未按预期方式连接，请检查是否已在 VM 上的本地防火墙中打开通过端口80、443和32526的出站通信。 如果阻止此地址，VM 代理可能在各种情况下出现意外行为。

## <a name="advanced-troubleshooting"></a>高级故障排除

Windows Azure 来宾代理故障排除事件记录在以下日志文件中：

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

以下是一些常见场景，在这些场景中，Windows Azure 来宾代理可能会进入“未就绪”状态或按预期停止工作。

### <a name="agent-stuck-on-starting"></a>代理卡在“正在启动”

在 WaAppAgent 日志中，可以看到代理在启动过程中卡住，无法启动。

**记录信息**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent 正在启动。 版本 2.7.41491.901

**分析**
 
VM 仍在运行旧版本的 Microsoft Azure 来宾代理。 在 C:\WindowsAzure 文件夹中，你可能会注意到安装了多个 Windows Azure 来宾代理实例，其中包括多个相同版本的实例。 由于安装了多个代理实例，因此 VM 不会启动最新版本的 Microsoft Azure 来宾代理。

**解决方案**

手动卸载 Microsoft Azure 来宾代理，然后按照以下步骤重新安装：

1. 打开“控制面板”>“程序和功能”，然后卸载 Microsoft Azure 来宾代理。
1. 打开任务管理器，停止以下服务：Windows Azure 来宾代理服务、RDAgent 服务、Windows Azure 遥测服务和 Windows Azure 网络代理服务。
1. 在 "C:\WindowsAzure" 下，创建名为 "旧" 的文件夹。
1. 将名为 Packages 或 GuestAgent 的任何文件夹都移到 OLD 文件夹中。 另外，将 C:\WindowsAzure\logs 中以 GuestAgent_x.x.xxxxx 开头的任何 GuestAgent 文件夹都移到 OLD 文件夹中。
1. 下载并安装最新版本的 MSI 代理。 必须拥有管理员权限才能完成安装。
1. 使用以下 MSI 命令安装来宾代理：
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. 验证 RDAgent、Windows Azure 来宾代理和 Windows Azure 遥测服务当前是否正在运行。
 
1. 检查 WaAppAgent.log 以确保最新版本的 Microsoft Azure 来宾代理正在运行。
 
1. 删除 C:\WindowsAzure 下的 OLD 文件夹。
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>无法连接到 WireServer IP（主机 IP） 

在 WaAppAgent.log 和 Telemetry.log 中发现以下错误条目：

**记录信息**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**分析**

VM 无法访问 wireserver 主机服务器。

**解决方案**

1. 由于无法访问 wireserver，因此可使用远程桌面连接到 VM，然后尝试从 Internet 浏览器访问以下 URL： http://168.63.129.16/?comp=versions 
1. 如果无法访问步骤 1 中的 URL，请检查网络接口以确定是否已将其设置为已启用 DHCP 并具有 DNS。 若要检查网络接口的 DHCP 状态，请运行以下命令：`netsh interface ip show config`。
1. 如果 DHCP 处于禁用状态，请运行以下操作，确保将黄色的值更改为接口名称：`netsh interface ip set address name="Name of the interface" source=dhcp`。
1. 检查防火墙、代理或其他可能阻止访问 IP 地址 168.63.129.16 的源可能导致的任何问题。
1. 检查 Windows 防火墙或第三方防火墙是否阻止对端口 80、443 和 32526 的访问。 有关此地址不应被阻止的原因的详细信息，请参阅[什么是 IP 地址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

### <a name="guest-agent-is-stuck-stopping"></a>来宾代理卡在“正在停止”  

在 WaAppAgent.log 中发现以下错误条目：

**记录信息** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**分析**

Windows Azure 来宾代理在停止进程中卡住。

**解决方案**

1. 确保 WaAppAgent.exe 正在 VM 上运行。 如果它没有运行，请重启 rdgagent 服务，然后等待 5 分钟。 运行 WaAppAgent.exe 时，WindowsAzureGuest.exe 进程停止。
2. 如果步骤 1 无法解决此问题，请删除当前安装的版本，然后手动安装代理的最新版本。

### <a name="npcap-loopback-adapter"></a>Npcap 环回适配器 

在 WaAppAgent.log 中发现以下错误条目：
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**分析**

Npcap 环回适配器由 Wireshark 安装在 VM 上。 Wireshark 是一个开放源代码工具，用于分析流量和数据包。 这种工具通常称为网络分析器、网络协议分析器或嗅探器。

**解决方案**

Npcap 环回适配器可能是由 WireShark 安装的。 尝试禁用它，然后检查问题是否得以解决。

## <a name="next-steps"></a>后续步骤

若要进一步排查 "Windows Azure 来宾代理无法工作" 问题，请 [联系 Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门。