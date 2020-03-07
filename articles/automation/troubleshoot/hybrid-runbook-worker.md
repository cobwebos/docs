---
title: 故障排除 - Azure 自动化混合 Runbook 辅助角色
description: 本文提供对 Azure 自动化混合 Runbook 辅助角色进行故障排除的信息
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372497"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>对混合 Runbook 辅助角色进行故障排除

本文提供有关混合 Runbook 辅助角色故障排除问题的信息。

## <a name="general"></a>常规

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理是适用于 Windows 的 Log Analytics 代理，也称为 Microsoft Monitoring Agent （MMA）。 对于 Linux，这是适用于 Linux 的 Log Analytics 代理。

### <a name="runbook-execution-fails"></a>方案：Runbook 执行失败

#### <a name="issue"></a>问题

Runbook 执行失败，你将收到以下错误。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook 在尝试执行三次后会立即挂起。 存在可中断 runbook 完成的条件。 相关错误消息可能不包括任何其他信息。

#### <a name="cause"></a>原因

下面是可能的原因：

* Runbook 无法通过本地资源进行身份验证。

* 混合辅助角色在代理或防火墙后面。

* 配置为运行混合 Runbook 辅助角色功能的计算机不满足最低硬件要求。

#### <a name="resolution"></a>解决方法

验证计算机是否在端口443上具有对 *. azure-automation.net 的出站访问权限。

运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，然后才能将辅助角色配置为承载此功能。 Runbook 及其使用的后台进程可能会导致系统过度使用，并导致 runbook 作业延迟或超时。

确认运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存使用，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 任何内存或 CPU 压力都可能表明需要升级资源。 你还可以选择支持最低要求的其他计算资源，并在工作负荷需求指示需要增加时进行缩放。

检查 **Microsoft-SMA** 事件日志中是否有描述为 Win32 Process Exited with code [4294967295] 的相应事件。 导致此错误的原因是，你尚未在 runbook 中配置身份验证，或者未为混合 Runbook 辅助角色组指定运行方式凭据。 查看在[混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)中的 runbook 权限，确认已正确为 runbook 配置身份验证。

### <a name="no-cert-found"></a>方案：混合 Runbook 辅助角色上的证书存储中找不到证书

#### <a name="issue"></a>问题

混合 Runbook 辅助角色上运行的 runbook 失败，并出现以下错误消息。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>原因

当你尝试在运行方式帐户证书不存在的混合 Runbook 辅助角色上运行的 runbook 中尝试使用[运行方式帐户](../manage-runas-account.md)时，会发生此错误。 混合 Runbook 辅助角色在默认情况下没有本地证书资产，运行方式帐户需要此证书才能正常运行。

#### <a name="resolution"></a>解决方法

如果你的混合 Runbook 辅助角色是 Azure VM，则可以改用[azure 资源的托管标识](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。 此方案允许使用 Azure VM （而不是运行方式帐户）的托管标识对 Azure 资源进行身份验证，从而简化了身份验证。 当混合 Runbook 辅助角色为本地计算机时，需要在计算机上安装运行方式帐户证书。 若要了解如何安装证书，请参阅在[混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)中运行 PowerShell runbook export-runascertificatetohybridworker 的步骤。

### <a name="error-403-on-registration"></a>方案：在混合 Runbook 辅助角色的注册过程中出现错误403

#### <a name="issue"></a>问题

工作线程的初始注册阶段失败，你将收到以下错误（403）。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

下面是可能的原因：
* 代理设置中的工作区 ID 或工作区密钥（主）有误。 
* 混合 Runbook 辅助角色无法下载配置，导致帐户链接错误。 当 Azure 启用解决方案时，它仅支持特定的区域以链接 Log Analytics 工作区和自动化帐户。 还可能在计算机上设置了不正确的日期和/或时间。 如果从当前时间开始的时间为 +/-15 分钟，则载入失败。

#### <a name="resolution"></a>解决方法

##### <a name="mistyped-workspace-idkey"></a>工作区 ID/密钥错误键入
若要验证代理的工作区 ID 或工作区密钥是否已键入错误，请参阅[添加或删除工作区–](../../azure-monitor/platform/agent-manage.md#windows-agent) windows 代理的 windows 代理或[添加或删除工作区–](../../azure-monitor/platform/agent-manage.md#linux-agent) linux 代理的 linux 代理。  请确保从 Azure 门户中选择完整的字符串，并仔细复制并粘贴。

##### <a name="configuration-not-downloaded"></a>未下载配置

Log Analytics 工作区和自动化帐户必须位于链接区域。 有关支持的区域的列表，请参阅[Azure 自动化和 Log Analytics 工作区映射](../how-to/region-mappings.md)。

你可能还需要更新计算机的日期和或时区。 如果选择自定义时间范围，请确保范围为 UTC，这可能与本地时区不同。

## <a name="linux"></a>Linux

Linux 混合 Runbook 辅助角色依赖于[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)来与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="oms-agent-not-running"></a>方案：适用于 Linux 的 Log Analytics 代理未运行

#### <a name="issue"></a>问题

适用于 Linux 的 Log Analytics 代理未运行

#### <a name="cause"></a>原因

如果代理未运行，则它会阻止 Linux 混合 Runbook 辅助角色与 Azure Automation 通信。 代理可能由于各种原因而无法运行。

#### <a name="resolution"></a>解决方法

 输入以下命令，验证代理是否正在运行：`ps -ef | grep python`。 你应该看到类似如下的输出，即使用 **nxautomation** 用户帐户的 python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则以下任何进程都不会运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 这些进程全都位于 `/var/opt/microsoft/omsagent/state/automationworker/` 目录中。

* **oms** -worker 管理器进程。 它直接从 DSC 启动。

* **worker. 会议**-自动注册的混合工作进程。 它由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/** diy 混合辅助进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它只与自动注册混合辅助进程在使用不同配置的关键细节中不同。 如果 Azure 自动化解决方案已禁用并且 DIY Linux 混合辅助角色未注册，则此过程不存在。

如果代理未运行，请运行以下命令来启动该服务： `sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="class-does-not-exist"></a>方案：指定的类不存在

如果看到错误 **，则指定的类不存在。** 在 `/var/opt/microsoft/omsconfig/omsconfig.log`中，需要更新 Linux 的 Log Analytics 代理。 运行以下命令以重新安装代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 辅助角色依赖于[windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)来与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="mma-not-running"></a>方案： Microsoft Monitoring Agent 未运行

#### <a name="issue"></a>问题

`healthservice` 服务未在混合 Runbook 辅助角色计算机上运行。

#### <a name="cause"></a>原因

如果 Microsoft Monitoring Agent Microsoft 服务未运行，则此状态会阻止混合 Runbook 辅助角色与 Azure Automation 通信。

#### <a name="resolution"></a>解决方法

在 PowerShell 中输入以下命令，验证代理是否正在运行：`Get-Service healthservice`。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`Start-Service healthservice`。

### <a name="event-4502"></a>方案：事件 4502 Operations Manager 日志中

#### <a name="issue"></a>问题

在**Application And service Logs\Operations Manager**事件日志中，可以**看到包含、** 的事件4502和，其中包含以下说明：*服务提供的证书 \<AzureAutomation\>。 Eventmessage 以及不是由用于 Microsoft 服务的证书颁发机构颁发的。请与网络管理员联系，查看他们是否正在运行用于截获 TLS/SSL 通信的代理。*

#### <a name="cause"></a>原因

这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。 

#### <a name="resolution"></a>解决方法

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 你可以验证**Application And service Logs\Microsoft-SMA\Operations** and **application And service Logs\Operations Manager**事件日志中是否存在任何警告或错误事件，这些事件指明了影响角色加入 Azure 自动化或在正常操作中出现的问题。 有关排查 Log Analytics 代理问题的详细信息，请参阅[Log Analytics Windows 代理解决问题](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

[Runbook 输出和消息](../automation-runbook-output-and-messages.md)将从混合辅助角色发送到 Azure 自动化，就像在云中运行的 Runbook 作业一样。 就像在其他 Runbook 中一样，还可以启用详细流和进度流。

### <a name="corrupt-cache"></a>方案：混合 Runbook 辅助角色未报告

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

若要解决此问题，请登录到混合 Runbook 辅助角色并运行以下脚本。 此脚本将停止 Microsoft Monitoring Agent，删除其高速缓存并重启该服务。 此操作强制混合 Runbook 辅助角色从 Azure 自动化重新下载其配置。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>方案：无法添加混合 Runbook 辅助角色

#### <a name="issue"></a>问题

尝试使用 `Add-HybridRunbookWorker` cmdlet 添加混合 Runbook 辅助角色时收到以下消息。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

如果已使用不同的自动化帐户注册了计算机，或者在从计算机中删除了混合 Runbook 辅助角色之后尝试重新添加，则可能会导致此问题。

#### <a name="resolution"></a>解决方法

若要解决此问题，请删除以下注册表项并重启 `HealthService`，然后再次尝试 `Add-HybridRunbookWorker` cmdlet：

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。