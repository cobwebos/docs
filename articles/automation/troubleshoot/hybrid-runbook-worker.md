---
title: Azure 自动化混合 Runbook 辅助角色故障排除
description: 本文提供了有关 Azure 自动化混合 Runbook 辅助角色故障排除的信息。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679336"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>对混合 Runbook 辅助角色进行故障排除

本文提供有关混合 Runbook 辅助角色故障排除问题的信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="general"></a>常规

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理是适用于 Windows 的 Log Analytics 代理。 对于 Linux，它是适用于 Linux 的 Log Analytics 代理。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>方案：Runbook 执行失败

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
* 配置为运行混合 Runbook 辅助角色的计算机不满足最低硬件要求。

#### <a name="resolution"></a>解决方法

验证计算机是否在端口443上具有对 ***. azure-automation.net**的出站访问权限。

运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，然后才能将辅助角色配置为承载此功能。 Runbook 及其使用的后台进程可能会导致系统过度使用，并导致 runbook 作业延迟或超时。

确认运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存使用，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 任何内存或 CPU 压力都可能表明需要升级资源。 你还可以选择支持最低要求的其他计算资源，并在工作负荷需求指示需要增加时进行缩放。

查看**MICROSOFT SMA**事件日志中的相应事件，并提供说明`Win32 Process Exited with code [4294967295]`。 导致此错误的原因是，你尚未在 runbook 中配置身份验证，或者未为混合 Runbook 辅助角色组指定运行方式凭据。 查看在[混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)中的 runbook 权限，确认已正确为 runbook 配置身份验证。

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>方案：混合 Runbook 辅助角色中的事件15011

#### <a name="issue"></a>问题

混合 Runbook 辅助角色收到事件15011，表示查询结果无效。 当工作线程尝试打开与[SignalR 服务器](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)的连接时，将出现以下错误。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

尚未为自动部署解决方案正确配置混合 Runbook 辅助角色。 此解决方案包含将 VM 连接到 Log Analytics 工作区的部件。 PowerShell 脚本将在订阅中查找具有提供的名称的工作区。 在这种情况下，Log Analytics 工作区位于不同的订阅中。 脚本找不到工作区，尝试创建一个，但该名称已被占用。 因此，部署将失败。

#### <a name="resolution"></a>解决方法

可以通过两种方法解决此问题：

* 修改 PowerShell 脚本以在另一个订阅中查找 Log Analytics 工作区。 如果将来打算部署多个混合 Runbook 辅助角色计算机，则这是一个很好的解决方案。

* 手动将辅助计算机配置为在 Orchestrator 沙盒中运行。 然后在辅助角色上运行在 Azure 自动化帐户中创建的 runbook 以测试该功能。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>方案：从混合辅助角色组中自动删除的 Windows Azure Vm

#### <a name="issue"></a>问题

当工作线程长时间关闭时，无法查看混合 Runbook 辅助角色或 Vm。

#### <a name="cause"></a>原因

混合 Runbook 辅助角色计算机未 ping 到超过30天的 Azure Automation。 因此，自动化已清除了混合 Runbook 辅助角色组或系统辅助角色组。 

#### <a name="resolution"></a>解决方法

启动辅助计算机，然后将其与 Azure 自动化 rereregister。 请参阅[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中的 "安装 runbook 环境并连接到 Azure 自动化" 的说明。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>方案：混合 Runbook 辅助角色上的证书存储中找不到证书

#### <a name="issue"></a>问题

混合 Runbook 辅助角色上运行的 runbook 失败，并出现以下错误消息。

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>原因

当你尝试在运行方式帐户证书不存在的混合 Runbook 辅助角色上运行的 runbook 中尝试使用[运行方式帐户](../manage-runas-account.md)时，会发生此错误。 默认情况下，混合 Runbook 辅助角色在本地没有证书资产。 运行方式帐户需要此资产才能正常运行。

#### <a name="resolution"></a>解决方法

如果你的混合 Runbook 辅助角色是 Azure VM，则可以改用[azure 资源的托管标识](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。 此方案允许使用 Azure VM （而不是运行方式帐户）的托管标识对 Azure 资源进行身份验证，从而简化了身份验证。 当混合 Runbook 辅助角色为本地计算机时，需要在计算机上安装运行方式帐户证书。 若要了解如何安装证书，请参阅在[混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)中运行 PowerShell runbook **export-runascertificatetohybridworker**的步骤。

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>方案：在混合 Runbook 辅助角色的注册过程中出现错误403

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

你可能还需要更新计算机的日期和/或时区。 如果选择自定义时间范围，请确保范围为 UTC，这可能与本地时区不同。

## <a name="linux"></a>Linux

Linux 混合 Runbook 辅助角色依靠[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>方案：适用于 Linux 的 Log Analytics 代理未运行

#### <a name="issue"></a>问题

适用于 Linux 的 Log Analytics 代理未运行

#### <a name="cause"></a>原因

如果代理未运行，这会导致 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 代理可能由于各种原因而无法运行。

#### <a name="resolution"></a>解决方法

 通过输入命令`ps -ef | grep python`来验证代理是否正在运行。 应会看到如下所示的输出，其中包含**nxautomation**用户帐户的 Python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则无法运行以下任何进程。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 它们都位于 **/var/opt/microsoft/omsagent/state/automationworker/** 目录中。

* **oms** -worker 管理器进程。 它直接从 DSC 启动。

* **worker. 会议**-自动注册的混合工作进程。 它由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/** diy 混合辅助进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它仅与自动注册混合辅助进程在使用不同配置的关键细节中不同。 如果 Azure 自动化解决方案已禁用并且 DIY Linux 混合辅助角色未注册，则此过程不存在。

如果代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>方案：指定的类不存在

如果在/var/opt/microsoft/omsconfig/omsconfig.log 中看到`The specified class does not exist..`错误， **/var/opt/microsoft/omsconfig/omsconfig.log**则需要更新适用于 Linux 的 Log Analytics 代理。 运行以下命令重新安装代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 辅助角色依靠[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，此部分将包括一些可能的原因。

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>方案： Windows 的 Log Analytics 代理未运行

#### <a name="issue"></a>问题

`healthservice`未在混合 Runbook 辅助角色计算机上运行。

#### <a name="cause"></a>原因

如果 Windows 服务的 Log Analytics 未运行，则混合 Runbook 辅助角色将无法与 Azure 自动化通信。

#### <a name="resolution"></a>解决方法

通过在 PowerShell 中输入以下命令来验证代理是否正在运行： `Get-Service healthservice`。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`Start-Service healthservice`。

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>方案：事件 4502 Operations Manager 日志中

#### <a name="issue"></a>问题

在**Application And service Logs\Operations Manager**事件日志中，可以看到包含`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`以下说明的事件4502和、：<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 验证计算机是否在端口443上具有对 ***. azure-automation.net**的出站访问权限。 

#### <a name="resolution"></a>解决方法

日志以本地方式存储在**C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**的每个混合辅助角色上。 可以验证**Application And service Logs\Microsoft-SMA\Operations** and **application And service Logs\Operations Manager**事件日志中是否有任何警告或错误事件。 这些日志表示连接或其他类型的问题，该问题会影响将角色加入 Azure 自动化的问题或在正常操作下遇到的问题。 如果需要更多帮助来排查 Log Analytics 代理问题，请参阅[排查 Log Analytics Windows 代理问题](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

混合辅助角色以在云中运行的 runbook 作业发送输出和消息的相同方式将[runbook 输出和消息](../automation-runbook-output-and-messages.md)发送到 Azure 自动化。 可以启用详细流和进度流，就像对 runbook 执行的操作一样。

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>方案： Orchestrator. wsdl.exe 无法通过代理连接到 Office 365

#### <a name="issue"></a>问题

在 Windows 混合 Runbook 辅助角色上运行的脚本不能按预期方式连接到 Orchestrator 沙盒上的 Office 365。 此脚本使用连接[connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)进行连接。 

如果调整**Orchestrator. .config**以设置代理和绕过列表，则沙盒仍无法正确连接。 使用相同的代理和绕过列表设置的**Powershell_ise .exe**文件似乎会按预期方式工作。 Service Management Automation （SMA）日志和 PowerShell 日志不提供有关代理的任何信息。

#### <a name="cause"></a>原因

与服务器上的 Active Directory 联合身份验证服务（ADFS）的连接不能绕过代理。 请记住，PowerShell 沙箱作为已登录的用户运行。 但是，业务流程协调程序沙盒经过大量自定义，并且可能会忽略**orchestrator. .config**文件设置。 它具有用于处理计算机或 MMA 代理设置的特殊代码，但不适用于处理其他自定义代理设置。 

#### <a name="resolution"></a>解决方法

可以通过将脚本迁移到使用 Azure AD 模块，而不是使用 PowerShell cmdlet 的 MSOnline 模块来解决 Orchestrator 沙盒的问题。 请参阅[从 Orchestrator 迁移到 Azure 自动化（Beta）](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)。

如果要继续使用 MSOnline 模块 cmdlet，请将脚本更改为使用[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。 指定`ComputerName`和`Credential`参数的值。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

此代码更改在指定凭据的上下文中启动一个完全新的 PowerShell 会话。 它应该允许流量流过正在对活动用户进行身份验证的代理服务器。

>[!NOTE]
>此解决方案使得不必操作沙盒配置文件。 即使已成功使配置文件适用于你的脚本，每次更新混合 Runbook 辅助角色代理时都会擦除该文件。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>方案：混合 Runbook 辅助角色未报告

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

若要解决此问题，请登录到混合 Runbook 辅助角色并运行以下脚本。 此脚本停止 Windows 的 Log Analytics 代理，删除其缓存并重新启动该服务。 此操作会强制混合 Runbook 辅助角色从 Azure 自动化重新下载其配置。

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

如果已使用不同的自动化帐户注册了计算机，或者在从计算机中删除了混合 Runbook 辅助角色之后尝试重新添加，则可能会导致此问题。

#### <a name="resolution"></a>解决方法

若要解决此问题，请删除以下注册表项，重新`HealthService`启动，并重试`Add-HybridRunbookWorker`该 cmdlet。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。