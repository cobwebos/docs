---
title: Windows Azure 来宾代理疑难解答
description: 排查 Windows Azure 来宾代理不起作用问题
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/07/2020
ms.author: genli
ms.openlocfilehash: c3295365859ad3291a95b616cccc6fa265237a01
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263889"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Windows Azure 来宾代理疑难解答

Windows Azure 来宾代理是 (VM) 代理的虚拟机。 它允许 VM 与结构控制器进行通信， (在其上托管了 VM) IP 地址168.63.129.16 上的基础物理服务器。 这是一个简化通信的虚拟公共 IP 地址。 有关详细信息，请参阅 [什么是 IP 地址 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)。

 从本地迁移到 Azure 或使用自定义映像创建的 VM 未安装 Windows Azure 来宾代理。 在这些情况下，必须手动安装 VM 代理。 有关如何安装 VM 代理的详细信息，请参阅 [Azure 虚拟机代理概述](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。

成功安装 Windows Azure 来宾代理后，你可以在虚拟机上的 services.msc 中看到以下服务：
 
- Windows Azure 来宾代理服务
- 遥测服务
- RD 代理服务

**Windows Azure 来宾代理服务**：此服务是负责 WAppAgent 中所有日志记录的服务。 此服务负责配置各种扩展以及从来宾到主机的通信。 

**遥测服务**：此服务负责将 VM 的遥测数据发送到后端服务器。

**RD 代理服务**：此服务负责安装来宾代理。 透明安装程序也是 Rd 代理的一个组件，可帮助升级来宾代理的其他组件和服务。 RDAgent 还负责从来宾 VM 向物理服务器上的主机代理发送检测信号。

> [!NOTE]
> 从 VM 来宾代理的2.7.41491.971 版开始，遥测组件包含在 RDAgent 服务中，因此，在新创建的 Vm 中可能看不到此遥测服务。

## <a name="checking-agent-status-and-version"></a>检查代理状态和版本

中转到 Azure 门户中的 VM 属性页，并检查 **代理状态**。 如果 Windows Azure 来宾代理正常工作，则状态显示为 " **就绪**"。 如果 VM 代理处于 " **未就绪** " 状态，则 Azure 门户上的 "扩展" 和 " **运行" 命令** 将不起作用。

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>故障排除处于 "就绪" 状态的 VM 代理

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>步骤1检查是否已安装 Windows Azure 来宾代理服务

- 检查包

    找到 C:\WindowsAzure 文件夹。 如果看到显示版本号的 GuestAgent 文件夹，则表示已在 VM 上安装了 Windows Azure 来宾代理。 你还可以查找已安装的包。  如果在 VM 上安装了 Windows Azure 来宾代理，则会将包保存在以下位置： `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` 。
    
    可以运行以下 PowerShell 命令，以检查 VM 代理是否已部署到 VM：
    
    `Get-Az VM -ResourceGroup “RGNAME” – Name “VMNAME” -displayhint expand`
    
    在输出中，找到 **ProvisionVMAgent** 属性，并检查该值是否设置为 **True**。 如果是，则表示已在 VM 上安装代理。
    
- 检查服务和进程

   请在服务控制台 (services.msc) 并检查以下服务的状态： Windows Azure 来宾代理服务、RDAgent 服务、Microsoft Azure 遥测服务和 Windows Azure 网络代理服务。
 
    还可以检查任务管理器中是否有以下过程，以检查这些服务是否正在运行：
       
    - WindowsAzureGuestAgent.exe： Windows Azure 来宾代理服务
    - WaAppAgent.exe： RDAgent 服务
    - WindowsAzureNetAgent.exe： Windows Azure 网络代理服务
    - WindowsAzureTelemetryService.exe： Windows Azure 遥测服务

   如果你找不到这些进程和服务，则表示你未安装 Windows Azure 来宾代理。

- 检查程序和功能

    在 "控制面板" 中，请参阅 " **程序和功能** " 来确定是否安装了 Windows Azure 来宾代理服务。

如果未找到任何包、运行的服务和进程，甚至没有在 "程序和功能" 下安装 Windows Azure 来宾代理，请尝试 [安装 Windows Azure 来宾代理服务](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。 如果来宾代理安装不正确，你可以 [脱机安装 VM 代理](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)。

如果可以看到服务，并且服务正在运行，请重新启动该服务，以查看是否解决了问题。 如果服务已停止，请启动它们并等待几分钟。 然后检查 **代理状态** 是否报告为 " **就绪**"。 如果发现这些服务崩溃，某些第三方进程可能会导致这些服务崩溃。 若要进一步排查这些问题，请联系 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="step-2-check-whether-auto-update-is-working"></a>步骤2检查自动更新是否正常工作

Windows Azure 来宾代理具有自动更新功能。 它将自动检查并安装新的更新。 如果自动更新功能不能正常工作，请尝试使用以下步骤卸载和安装 Windows Azure 来宾代理：

1. 如果 " **程序和功能**" 中出现 Windows Azure 来宾代理，请卸载 Windows Azure 来宾代理。

2. 打开具有管理员权限的命令提示符窗口。

3. 停止来宾代理服务。 如果服务未停止，则必须将服务设置为 **手动启动** ，然后重新启动 VM。
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
1. 在 " `C:\WindowsAzure` 创建名为旧的文件夹" 下。

1. 将名为包或 GuestAgent 的任何文件夹移动到旧文件夹中。

1. 从 [此处](https://go.microsoft.comfwlink/?linkid=394789&clcid=0x409)下载并安装最新版本的代理安装包。 必须拥有管理员权限才能完成安装。

1. 使用以下命令安装来宾代理：

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    然后检查来宾代理服务是否正常启动。
 
    在不能正确安装来宾代理的罕见情况下，你可以 [脱机安装 VM 代理](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)。
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>步骤3检查 VM 是否可以连接到结构控制器

使用 PsPing 之类的工具测试 VM 是否可以连接到端口80、32526和443上的168.63.129.16。 如果 VM 未按预期方式连接，请检查是否已在 VM 上的本地防火墙中打开通过端口80、443和32526的出站通信。 如果此 IP 地址被阻止，VM 代理可能会在各种情况下显示意外行为。

## <a name="advanced-troubleshooting"></a>高级故障排除

Windows Azure 来宾代理故障排除事件记录在以下日志文件中：

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

下面是一些常见方案，在这些方案中，Windows Azure 来宾代理可以进入 " **未就绪** " 状态或停止按预期方式工作。

### <a name="agent-stuck-on-starting"></a>代理停滞在 "正在启动"

在 Waappagent.exe 日志中，可以看到代理在启动过程中停滞，无法启动。

**记录信息**
 
[00000007] [05/28/2019 12：58： 50.90] [INFO] WindowsAzureGuestAgent 开始。 版本2.7.41491.901

**分析**
 
VM 仍在运行旧版本的 Windows Azure 来宾代理。 在 C:\WindowsAzure 文件夹中，你可能会注意到多个 Windows Azure 来宾代理实例已安装，包括多个同一版本。 由于安装了多个代理实例，因此 VM 不会启动最新版本的 Windows Azure 来宾代理。

**解决方案**

手动卸载 Windows Azure 来宾代理，然后通过执行以下步骤重新安装它：

1. 打开 "控制面板" > "程序和功能"，然后卸载 Windows Azure 来宾代理。
1. 打开任务管理器，并停止以下服务： Windows Azure 来宾代理服务、RDAgent 服务、Microsoft Azure 遥测服务和 Windows Azure 网络代理服务。
1. 在 "C:\WindowsAzure" 下，创建名为 "旧" 的文件夹。
1. 将名为包或 GuestAgent 的任何文件夹移动到旧文件夹中。 同时，将以 GuestAgent_x 开头的 C:\WindowsAzure\logs 中的任何 GuestAgent 文件夹移动到旧文件夹中。
1. 下载并安装最新版本的 MSI 代理。 您必须具有管理员权限才能完成安装。
1. 使用以下 MSI 命令安装来宾代理：
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. 验证 RDAgent、Microsoft Azure 来宾代理和 Windows Azure 遥测服务是否正在运行。
 
1. 检查 Waappagent.exe 以确保最新版本的 Windows Azure 来宾代理正在运行。
 
1. 删除 C:\WindowsAzure. 下的旧文件夹
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>无法连接到 WireServer IP (主机 IP)  

你会在 Waappagent.exe 和遥测中注意到以下错误条目：

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

1. 由于无法访问 wireserver，因此请使用远程桌面连接到 VM，然后尝试从 internet 浏览器访问以下 URL： http://168.63.129.16/?comp=versions 
1. 如果无法连接到步骤1中的 URL，请检查网络接口，以确定它是否已设置为启用 DHCP 并且具有 DNS。 若要检查网络接口的 DHCP 状态，请运行以下命令：  `netsh interface ip show config` 。
1. 如果 DHCP 处于禁用状态，请运行以下操作确保将黄色值更改为您的接口的名称： `netsh interface ip set address name="Name of the interface" source=dhcp` 。
1. 检查可能会导致防火墙、代理或其他源阻止访问 IP 地址168.63.129.16 的任何问题。
1. 检查 Windows 防火墙或第三方防火墙是否阻止访问端口80、443和32526。 有关此地址不应被阻止的原因的详细信息，请参阅 [什么是 IP 地址 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)。

### <a name="guest-agent-is-stuck-stopping"></a>来宾代理停滞 "正在停止"  

你会在 Waappagent.exe 中注意到以下错误条目：

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

Windows Azure 来宾代理停滞在停止进程。

**解决方案**

1. 请确保 WaAppAgent.exe 正在 VM 上运行。 如果该服务未运行，请重新启动 rdgagent 服务，然后等待5分钟。 当 WaAppAgent.exe 运行时，结束 WindowsAzureGuest.exe 进程。
2. 如果步骤1不能解决问题，请删除当前安装的版本，并手动安装最新版本的代理。

### <a name="npcap-loopback-adapter"></a>Npcap 环回适配器 

你会在 Waappagent.exe 中注意到以下错误条目：
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**分析**

Npcap 环回适配器是在 VM 上通过 Wireshark 安装的。 Wireshark 是一个开源工具，用于分析网络流量和分析数据包。 此类工具通常称为网络分析器、网络协议分析器或嗅探器。

**解决方案**

Npcap 环回适配器可能由 WireShark 安装。 尝试禁用它，然后检查问题是否已解决。

## <a name="next-steps"></a>后续步骤

若要进一步排查 "Windows Azure 来宾代理无法工作" 问题，请 [联系 Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门。