---
title: 故障排除 - Azure 自动化混合 Runbook 辅助角色
description: 本文提供对 Azure 自动化混合 Runbook 辅助角色进行故障排除的信息
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e3726037e16acdf1d6d624dbf8c2088a57b0bde6
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234535"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>对混合 Runbook 辅助角色进行故障排除

本文提供有关混合 Runbook 辅助角色故障排除问题的信息。

## <a name="general"></a>常规

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理为 Microsoft Monitoring Agent。 对于 Linux，此代理为适用于 Linux 的 OMS 代理。

### <a name="runbook-execution-fails"></a>场景：Runbook 执行失败

#### <a name="issue"></a>问题

Runbook 执行失败，你收到以下错误：

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook 在尝试执行三次后立刻暂停。 在某些情况下，Runbook 可能会中断，无法正常完成。 如果出现这种情况，收到的错误消息可能不会显示额外信息来解释相关原因。

#### <a name="cause"></a>原因

以下是可能的原因：

* runbook 无法使用本地资源进行身份验证

* 混合辅助角色在代理或防火墙后面

* runbook 无法使用本地资源进行身份验证

* 配置为运行混合 Runbook 辅助角色功能的计算机不满足最低硬件要求。

#### <a name="resolution"></a>解决方法

确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，才能配置它托管此功能。 它们使用的 Runbook 和后台进程可能会导致系统被过度利用，并造成 Runbook 作业延迟或超时。

确认将要运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存使用，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 如果存在内存或 CPU 压力，这可能意味着需要升级资源。 也可以选择其他可支持最低要求的计算资源，并在工作负荷需求指示需要增加时进行扩展。

检查 **Microsoft-SMA** 事件日志中是否有描述为 Win32 Process Exited with code [4294967295] 的相应事件。 此错误的原因是尚未在 runbook 中配置身份验证，或者未为混合辅助角色组指定运行方式凭据。 请查看 [Runbook 权限](../automation-hrw-run-runbooks.md#runbook-permissions)，确认已正确为 runbook 配置身份验证。

### <a name="no-cert-found"></a>场景：在混合 Runbook 辅助角色上的证书存储中找不到证书

#### <a name="issue"></a>问题

混合 Runbook 辅助角色上运行的 runbook 失败并显示以下错误消息：

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>原因

尝试在混合 Runbook 辅助角色上运行的 Runbook 中使用[运行方式帐户](../manage-runas-account.md)时，如果运行方式帐户证书不存在，则会发生此错误。 默认情况下，混合 Runbook 辅助角色没有本地证书资产，而运行方式帐户需有证书才能正常运行。

#### <a name="resolution"></a>解决方法

如果混合 Runbook 辅助角色是 Azure VM，则可以使用 [Azure 资源的托管标识](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。 此方案允许使用 Azure VM 的托管标识而非运行方式帐户，向 Azure 资源进行身份验证，从而简化了身份验证过程。 如果混合 Runbook 辅助角色是本地计算机，需要在此计算机上安装运行方式帐户证书。 若要了解如何安装证书，请查看 [Export-runascertificatetohybridworker](../automation-hrw-run-runbooks.md#runas-script) runbook 的运行步骤。

## <a name="linux"></a>Linux

Linux 混合 Runbook 辅助角色依靠适用于 Linux 的 OMS 代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="oms-agent-not-running"></a>场景：适用于 Linux 的 OMS 代理未运行

#### <a name="issue"></a>问题

适用于 Linux 的 OMS 代理未运行

#### <a name="cause"></a>原因

如果适用于 Linux 的 OMS 代理未运行，这会导致 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 代理可能会因各种原因而未在运行。

#### <a name="resolution"></a>解决方法

 输入以下命令，验证代理是否正在运行：`ps -ef | grep python`。 你应该看到类似如下的输出，即使用 **nxautomation** 用户帐户的 python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则以下任何进程都不会运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 这些进程全都位于 `/var/opt/microsoft/omsagent/state/automationworker/` 目录中。


* **oms.conf** - 此值是辅助角色管理器进程。 它直接从 DSC 启动。

* **worker.conf** - 此进程是自动注册混合辅助角色进程，由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/worker.conf**：此进程是 DIY 混合辅助角色进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它仅与使用不同配置的自动注册混合辅助角色进程在主要细节上有所不同。 如果禁用 Azure 自动化解决方案，并且 DIY Linux 混合辅助角色未注册，则不会显示此进程。

如果适用于 Linux 的 OMS 代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="class-does-not-exist"></a>场景：指定的类不存在

如果看到此错误：**指定的类不存在..** （在 `/var/opt/microsoft/omsconfig/omsconfig.log` 中），则需要更新适用于 Linux 的 OMS 代理。 运行以下命令重新安装 OMS 代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 辅助角色依靠 Microsoft Monitoring Agent 与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="mma-not-running"></a>场景：Microsoft Monitoring Agent 未运行

#### <a name="issue"></a>问题

`healthservice` 服务未在混合 Runbook 辅助角色计算机上运行。

#### <a name="cause"></a>原因

如果 Microsoft Monitoring Agent Windows 服务未运行，会导致混合 Runbook 辅助角色无法与 Azure 自动化通信。

#### <a name="resolution"></a>解决方法

在 PowerShell 中输入以下命令，验证代理是否正在运行：`Get-Service healthservice`。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`Start-Service healthservice`。

### <a name="event-4502"></a> Operations Manager 日志中的事件 4502

#### <a name="issue"></a>问题

在**应用程序和服务日志\Operations Manager** 事件日志中，你会看到事件 4502 和事件消息，其中包含 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** 及以下描述：*服务 \<wsid\>.oms.opinsights.azure.com 提供的证书不是由用于 Microsoft 服务的证书颁发机构颁发的。请联系网络管理员以查看其是否正在运行截获 TLS/SSL 通信的代理。KB3126513 一文还介绍了关于连接问题的其他故障排除信息。*

#### <a name="cause"></a>原因

这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

#### <a name="resolution"></a>解决方法

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 可以检查**应用程序和服务日志\Microsoft-SMA\操作**和 **应用程序和服务日志\Operations Manager** 事件日志中是否有任何警告或错误事件，指示出现了影响角色载入 Azure 自动化的连接问题或其他问题，或者在执行正常操作时出现问题。

[Runbook 输出和消息](../automation-runbook-output-and-messages.md)将从混合辅助角色发送到 Azure 自动化，就像在云中运行的 Runbook 作业一样。 就像在其他 Runbook 中一样，还可以启用详细流和进度流。

### <a name="corrupt-cache"></a> 混合 Runbook 辅助角色未提供报告

#### <a name="issue"></a>问题

混合 Runbook 辅助角色计算机在运行，但是在工作区中未看到该计算机的检测信号数据。

以下示例查询显示了工作区中的计算机及其上次检测信号：

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

此问题可能是由于混合 Runbook 辅助角色上的高速缓存损坏导致的。

#### <a name="resolution"></a>解决方法

若要解决此问题，请登录到混合 Runbook 辅助角色并运行以下脚本。 此脚本将停止 Microsoft Monitoring Agent，删除其高速缓存并重启该服务。 此操作会强制混合 Runbook 辅助角色从 Azure 自动化重新下载其配置。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>场景：无法添加混合 Runbook 辅助角色

#### <a name="issue"></a>问题

尝试使用 `Add-HybridRunbookWorker` cmdlet 添加混合 Runbook 辅助角色时收到以下消息。

```
Machine is already registered to a different account
```

#### <a name="cause"></a>原因

如果计算机已注册到一个不同的自动化帐户，或者在将混合 Runbook 辅助角色从计算机中删除后尝试重新添加它，则可能会出现此消息。

#### <a name="resolution"></a>解决方法

若要解决此问题，请删除以下注册表项，并再次尝试 `Add-HybridRunbookWorker` cmdlet：

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

