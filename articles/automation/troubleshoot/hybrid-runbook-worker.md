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
ms.openlocfilehash: 33e3e162892f1e2a148258273160ca26fa9c2efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153516"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>对混合 Runbook 辅助角色进行故障排除

本文提供有关混合 Runbook 辅助角色故障排除问题的信息。

## <a name="general"></a>常规

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理是 Windows 的日志分析代理，也称为 Microsoft 监视代理 （MMA）。 对于 Linux，它是适用于 Linux 的 Log Analytics 代理。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>方案：Runbook 执行失败

#### <a name="issue"></a>问题

Runbook 执行失败，您会收到以下错误。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

您的 Runbook 在尝试执行三次后不久即挂起。 在某些情况下，可能会中断 Runbook 的完成。 相关的错误消息可能不包含任何其他信息。

#### <a name="cause"></a>原因

下面是可能的原因：

* Runbook 无法使用本地资源进行身份验证。

* 混合辅助角色在代理或防火墙后面。

* 配置为运行混合 Runbook 辅助功能的计算机不符合最低硬件要求。

#### <a name="resolution"></a>解决方法

验证计算机在端口 443 上具有对 ***.azure-automation.net**的出站访问权限。

运行混合 Runbook 辅助角色的计算机应在将辅助角色配置为承载此功能之前满足最低硬件要求。 Runbook 及其使用的后台进程可能会导致系统过度使用，并导致 Runbook 作业延迟或超时。

确认运行混合 Runbook 辅助功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存使用，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 任何内存或 CPU 压力都可能表示需要升级资源。 您还可以选择支持最低要求和扩展的不同计算资源，当工作负载需求指示需要增加时。

检查**Microsoft-SMA**事件日志中有关说明`Win32 Process Exited with code [4294967295]`的相应事件。 此错误的原因是，您尚未在 Runbook 中配置身份验证，也没有为混合 Runbook 工作组指定"运行为"凭据。 查看[混合 Runbook 辅助角色运行簿中的 Runbook](../automation-hrw-run-runbooks.md)权限，以确认已正确配置运行簿的身份验证。

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>方案：混合 Runbook 辅助角色中的事件 15011

#### <a name="issue"></a>问题

混合 Runbook 工作线程接收事件 15011，指示查询结果无效。 当工作人员尝试打开与[SignalR 服务器](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)的连接时，将显示以下错误。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

尚未为自动部署解决方案正确配置混合 Runbook 辅助角色。 此解决方案包含将 VM 连接到日志分析工作区的部件。 PowerShell 脚本使用提供的名称查找订阅中的工作区。 在这种情况下，日志分析工作区位于不同的订阅中。 脚本找不到工作区并尝试创建工作区，但名称已被获取。 因此，部署失败。

#### <a name="resolution"></a>解决方法

有两个选项可以解决此问题：

* 修改 PowerShell 脚本以查找另一个订阅中的日志分析工作区。 如果您计划将来部署许多混合 Runbook 辅助计算机，则这是一个很好的解决方案。

* 手动将辅助计算机配置为在协调器沙盒中运行。 然后运行在辅助角色上的 Azure 自动化帐户中创建的 Runbook 以测试功能。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>方案：Windows Azure VM 自动从混合辅助角色组删除

#### <a name="issue"></a>问题

当工作机已关闭很长时间时，您看不到混合 Runbook 工作项或 VM。

#### <a name="cause"></a>原因

混合 Runbook 工作机已 30 天以上未 ping Azure 自动化。 因此，自动化已清除混合 Runbook 工作群体或系统辅助角色组。 

#### <a name="resolution"></a>解决方法

启动辅助计算机，然后将其重新注册到 Azure 自动化。 请参阅有关在[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中安装 Runbook 环境和连接到 Azure 自动化的说明。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>方案：在混合 Runbook 辅助功能的证书存储中未找到证书

#### <a name="issue"></a>问题

在混合 Runbook 工作线程上运行的 Runbook 失败，出现以下错误消息。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>原因

当您[尝试在运行](../manage-runas-account.md)在混合 Runbook 工作线程上运行的 Runbook 上运行的 Runbook 帐户时，将发生此错误，该操作簿中不存在"运行为帐户"证书。 默认情况下，混合 Runbook 工作人员没有本地证书资产，这是运行 As 帐户正常运行所必需的。

#### <a name="resolution"></a>解决方法

如果混合 Runbook 工作线程是 Azure VM，则可以对[Azure 资源使用托管标识](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。 此方案允许使用 Azure VM 的托管标识而不是"运行为"帐户对 Azure 资源进行身份验证，从而简化身份验证。 当混合 Runbook 工作线程是本地计算机时，您需要在计算机上安装"运行为帐户"证书。 要了解如何安装证书，请参阅在[混合 Runbook 工作线程上运行 Runbook](../automation-hrw-run-runbooks.md)运行簿中运行 PowerShell Runbook 运行簿导出运行证书到混合工作线程的步骤。

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>方案：混合 Runbook 工作线程注册期间的错误 403

#### <a name="issue"></a>问题

工作人员的初始注册阶段失败，您会收到以下错误 （403）。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

下面是可能的原因：
* 代理的设置中键入的工作区 ID 或工作区密钥（主键）类型错误。 
* 混合 Runbook 辅助角色无法下载配置，从而导致帐户链接错误。 当 Azure 启用解决方案时，它仅支持某些区域来链接日志分析工作区和自动化帐户。 计算机上也可能设置了不正确的日期和/或时间。 如果时间与当前时间的时间为 +/-15 分钟，则载入失败。

#### <a name="resolution"></a>解决方法

##### <a name="mistyped-workspace-idkey"></a>键入错误的工作区 ID/键
要验证代理的工作区 ID 或工作区密钥是否键入错误，请参阅[添加或删除工作区 —](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows 代理的 Windows 代理或[添加或删除工作区 -](../../azure-monitor/platform/agent-manage.md#linux-agent) Linux 代理的 Linux 代理。  请确保从 Azure 门户中选择完整字符串，并仔细复制并粘贴它。

##### <a name="configuration-not-downloaded"></a>未下载配置

日志分析工作区和自动化帐户必须位于链接区域。 有关受支持区域的列表，请参阅[Azure 自动化和日志分析工作区映射](../how-to/region-mappings.md)。

您可能还需要更新计算机的日期和/或时区。 如果选择自定义时间范围，请确保该范围为 UTC，这可能与您的本地时区不同。

## <a name="linux"></a>Linux

Linux 混合 Runbook 辅助角色依靠[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>方案：Linux 的日志分析代理未运行

#### <a name="issue"></a>问题

Linux 的日志分析代理未运行

#### <a name="cause"></a>原因

如果代理未运行，这会导致 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 由于各种原因，代理可能未运行。

#### <a name="resolution"></a>解决方法

 通过输入命令`ps -ef | grep python`验证代理正在运行。 您应该会看到类似于以下内容的输出，即使用**nx 自动化**用户帐户的 Python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则以下进程均未运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 它们都位于 **/var/opt/microsoft/omsagent/状态/自动化工作者/** 目录中。

* **oms.conf** - 工作人员管理器流程。 它直接从 DSC 启动。

* **worker.conf** - 自动注册的混合工作进程。 它由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/worker.conf** - DIY 混合工人流程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它仅在使用不同配置的关键详细信息中与自动注册混合工作进程不同。 如果 Azure 自动化解决方案已禁用且未注册 DIY Linux 混合辅助角色，则此过程不存在。

如果代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>方案：指定的类不存在

如果您在`The specified class does not exist..`**/var/opt/microsoft/omsconfig/omsconfig.log**中看到错误，则需要更新 Linux 的日志分析代理。 运行以下命令重新安装代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 辅助角色依靠[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果工作人员的注册失败，本节包括一些可能的原因。

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>方案：微软监视代理未运行

#### <a name="issue"></a>问题

`healthservice` 服务未在混合 Runbook 辅助角色计算机上运行。

#### <a name="cause"></a>原因

如果 Microsoft 监视代理服务未运行，则阻止混合 Runbook 辅助角色与 Azure 自动化通信。

#### <a name="resolution"></a>解决方法

通过在 PowerShell 中输入以下命令来`Get-Service healthservice`验证代理是否正在运行。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`Start-Service healthservice`。

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>方案：操作管理器日志中的事件 4502

#### <a name="issue"></a>问题

在**应用程序和服务日志_操作管理器**事件日志中，您将看到`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`事件 4502 和事件消息，其中包含以下说明：<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 验证计算机在端口 443 上具有对 ***.azure-automation.net**的出站访问权限。 

#### <a name="resolution"></a>解决方法

日志存储在**C：_程序数据\微软系统中心\协调器\7.2_SMA_沙盒**的每个混合工作线程上本地存储。 您可以验证**应用程序和服务日志中是否有任何警告或服务日志、Microsoft-SMA_操作****以及应用程序和服务日志\操作管理器**事件日志中是否存在任何警告或错误事件。 这些日志指示影响角色加入 Azure 自动化的连接或其他类型的问题，或在正常操作下遇到的问题。 如果需要更多帮助来排查 Log Analytics 代理问题，请参阅[排查 Log Analytics Windows 代理问题](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

混合工作人员向 Azure 自动化发送[Runbook 输出和消息](../automation-runbook-output-and-messages.md)的方式与在云中运行的 Runbook 作业发送输出和消息的方式相同。 您可以像启用 Runbook 一样启用"详细"和"进度"流。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>方案：混合 Runbook 工作人员不报告

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

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>方案：无法添加混合 Runbook 辅助角色

#### <a name="issue"></a>问题

尝试使用 `Add-HybridRunbookWorker` cmdlet 添加混合 Runbook 辅助角色时收到以下消息。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

如果计算机已注册到其他自动化帐户，或者您尝试在从计算机中删除混合 Runbook 辅助角色后读取该角色，则可能导致此问题。

#### <a name="resolution"></a>解决方法

要解决此问题，请删除以下注册表项，重新启动 ，`HealthService`然后重试`Add-HybridRunbookWorker`cmdlet。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。