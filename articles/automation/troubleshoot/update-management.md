---
title: 排查更新管理问题
description: 了解如何排查更新管理问题
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 48d2463eee2caeaae36118bf736d00eed84c897a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186226"
---
# <a name="troubleshooting-issues-with-update-management"></a>排查更新管理问题

本文讨论用于解决使用更新管理时可能遇到的问题的解决方案。

对于混合辅助角色代理，可使用代理故障排除程序来确定底层问题。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。 对于所有其他问题，请参阅以下有关可能问题的详细信息。

## <a name="general"></a>常规

### <a name="rp-register"></a>场景：无法为订阅注册自动化资源提供程序

#### <a name="issue"></a>问题

在自动化帐户中使用解决方案时，可能会收到以下错误。

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>原因

未在订阅中注册自动化资源提供程序。

#### <a name="resolution"></a>分辨率

可以通过在 Azure 门户中完成以下步骤来注册自动化资源提供程序：

1. 在底部的 Azure 服务列表中，单击“所有服务”，然后在“常规”服务组中选择“订阅”。
2. 选择订阅。
3. 单击 "_设置_" 下的 "**资源提供程序**"。
4. 在资源提供程序列表中，验证是否已注册了**Microsoft。自动化**资源提供程序已注册。
5. 如果**Microsoft.Automation**提供程序未列出，请将该提供程序注册到[](/azure/azure-resource-manager/resource-manager-register-provider-errors)下面列出的步骤。

### <a name="mw-exceeded"></a>场景：计划的更新管理失败，出现错误 MaintenanceWindowExceeded

#### <a name="issue"></a>问题

更新的默认维护时段为120分钟。 您可以将维护时段增加到最多6个小时或360分钟。

#### <a name="resolution"></a>分辨率

编辑任何失败的计划更新部署，并增加维护时段。

有关维护时段的详细信息，请参阅[安装更新](../automation-update-management.md#install-updates)。

### <a name="components-enabled-not-working"></a>场景：已启用“更新管理”解决方案的组件，现在正在配置此虚拟机

#### <a name="issue"></a>问题

在加入 15 分钟后继续在虚拟机上看到以下消息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>原因

此错误可能由以下原因引起：

1. 返回到自动化帐户的通信被阻止。
2. 正在加入的 VM 可能来自未在安装 Microsoft Monitoring Agent 的情况下进行系统准备的克隆计算机。

#### <a name="resolution"></a>分辨率

1. 访问[网络规划](../automation-hybrid-runbook-worker.md#network-planning)，了解需要允许哪些地址和端口才能使更新管理正常工作。
2. 如果使用的是克隆的映像：
   1. 在 Log Analytics 工作区中，从已保存的搜索`MicrosoftDefaultScopeConfig-Updates`中删除 VM （如果显示）。 已保存的搜索位于工作区的“常规”下。
   2. 运行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. 运行 `Restart-Service HealthService` 重新启动 `HealthService`。 这将重新创建密钥，并生成新的 UUID。
   4. 如果这不起作用，请先 sysprep 映像，然后安装 MMA 代理。

### <a name="multi-tenant"></a>场景：在为另一个 Azure 租户中的计算机创建更新部署时收到链接订阅错误。

#### <a name="issue"></a>问题

尝试为另一个 Azure 租户中的计算机创建更新部署时收到以下错误：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>原因

当创建的更新部署包含另一个租户中的 Azure 虚拟机时会发生此错误。

#### <a name="resolution"></a>分辨率

你需要使用以下解决方法来进行安排。 你可以使用 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 和开关 `-ForUpdate` 来创建计划，然后使用 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 并将另一个租户中的计算机传递给 `-NonAzureComputer` 参数。 以下示例展示了如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="updates-nodeployment"></a>场景：无部署的更新安装

### <a name="issue"></a>问题

在更新管理中注册 Windows 计算机时，可能会看到 "更新安装" 而不进行部署。

### <a name="cause"></a>原因

在 Windows 上，更新一旦可用就会自动安装。 如果未计划将更新部署到计算机，这可能会导致混淆。

### <a name="resolution"></a>分辨率

Windows 注册表项， `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`默认值为 "4"-**自动下载并安装**。

对于更新管理客户端，建议将此项设置为 "3"-**自动下载，但不自动安装**。

有关详细信息，请参阅[配置自动更新](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

### <a name="nologs"></a>场景：虚拟机不会显示在门户中更新管理

#### <a name="issue"></a>问题

您可以在以下情况下运行：

* 你的计算机**未**从 VM 的更新管理视图中进行配置

* 你的自动化帐户的更新管理视图中缺少你的计算机

* 你的计算机显示为 "**不** **符合**"，但在混合 Runbook 辅助角色 Azure Monitor 日志中看到了检测信号数据，但不能更新管理。

#### <a name="cause"></a>原因

这可能是由可能的本地配置问题或配置不当的作用域引起的。

混合 Runbook 辅助角色可能需要重新注册并重新安装。

您可能已在工作区中定义了已达到和正在阻止存储数据的配额。

#### <a name="resolution"></a>分辨率

* 确保你的计算机已向正确的工作区报告。 验证计算机报告到的工作区。 有关如何验证此操作的说明，请参阅[验证代理与 Log Analytics 的连接](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)。 然后，确保此工作区已链接到 Azure 自动化帐户。 若要确认这一点，请导航到自动化帐户，并单击 "**相关资源**" 下的 "**链接工作区**"。

* 检查以确保计算机显示在 Log Analytics 工作区中。 在链接到自动化帐户的 Log Analytics 工作区中运行以下查询。 如果未在查询结果中看到您的计算机，则表明您的计算机没有检测信号，这意味着可能会出现本地配置问题。 您可以根据操作系统运行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑难解答程序，也可以[重新安装代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 如果计算机显示在查询结果中，则需要执行以下项目符号中指定的范围配置。

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* 检查作用域配置问题。 [作用域配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)确定为解决方案配置的计算机。 如果你的计算机显示在你的工作区中，但未显示，你将需要配置作用域配置以将计算机设定为目标。 若要了解如何执行此操作，请参阅[工作区中的板载计算机](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 如果上述步骤无法解决问题，请按照[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中的步骤重新安装适用于 Windows 的混合辅助角色或[部署 Linux 混合 runbook 辅助角色](../automation-linux-hrw-install.md)（适用于 linux）。

* 在工作区中，运行以下查询。 如果看到结果`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` ，则表示你在工作区中定义了配额，但已达到并已停止保存数据。 在工作区中，导航到 "**使用情况和估计成本** > " "**数据量管理**"，并检查配额或删除拥有的配额。

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

如果在尝试在虚拟机上载入解决方案时遇到问题，请查看本地计算机“应用程序和服务日志”下的“Operations Manager”事件日志中是否存在事件 ID 为 4502、事件消息包含 Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent 的事件。

下一部分突出显示了特定的错误消息，以及每个消息的可能解决方案。 有关其他载入问题，请参阅[排查解决方案载入问题](onboarding.md)。

### <a name="machine-already-registered"></a>场景：计算机已注册到其他帐户

#### <a name="issue"></a>问题

看到以下错误消息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>原因

计算机已载入到其他进行更新管理的工作区。

#### <a name="resolution"></a>分辨率

通过[删除混合 runbook 组](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)对计算机上的旧项目进行清理，然后重试。

### <a name="machine-unable-to-communicate"></a>场景：计算机无法与服务进行通信

#### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>原因

可能是因为代理、网关或防火墙阻止了网络通信。

#### <a name="resolution"></a>分辨率

检查网络并确保允许适当的端口和地址。 有关更新管理和混合 Runbook 辅助角色所需的端口和地址列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="unable-to-create-selfsigned-cert"></a>场景：无法创建自签名证书

#### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

混合 Runbook 辅助角色无法生成自签名证书

#### <a name="resolution"></a>分辨率

请验证系统帐户是否具有对文件夹 C:\ProgramData\Microsoft\Crypto\RSA 的读取权限，然后重试。

### <a name="failed-to-start"></a>场景：计算机显示无法在更新部署中启动

#### <a name="issue"></a>问题

计算机的状态**无法启动**计算机。 查看计算机的特定详细信息时，会看到以下错误：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>原因

此错误可能是由以下原因之一导致的：

* 计算机不再存在。
* 计算机已关闭且无法访问。
* 计算机出现网络连接问题，计算机上的混合辅助角色无法访问。
* 已更改 SourceComputerId 的 Microsoft Monitoring Agent 更新
* 如果在自动化帐户中遇到2000并发作业限制，则更新运行可能已被阻止。 每个部署都被视为一个作业，更新部署中的每台计算机都作为一个作业计数。 当前在你的自动化帐户中运行的任何其他自动化作业或更新部署均计入并发作业限制。

#### <a name="resolution"></a>分辨率

如果适用，请使用[动态组](../automation-update-management.md#using-dynamic-groups)作为更新部署。

* 验证计算机是否仍然存在并且可以访问。 如果不存在，请编辑部署并删除计算机。
* 请参阅[网络规划](../automation-update-management.md#ports)部分，了解更新管理所需的端口和地址列表，并验证计算机是否满足这些要求。
* 在 Log Analytics 中运行以下查询，查找环境中更改了的`SourceComputerId`计算机。 查找值相同`Computer`但值不同`SourceComputerId`的计算机。 找到受影响的计算机后，你必须编辑以这些计算机为目标的更新部署，删除并重新添加计算机，使`SourceComputerId`反映正确的值。

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>场景：计算机显示“未评估”，并显示 HResult 异常

#### <a name="issue"></a>问题

有计算机在“符合性”下显示“未评估”，并且能看到下面显示一条异常消息。

#### <a name="cause"></a>原因

计算机中的 Windows 更新或 WSUS 配置不正确。 更新管理依赖于 Windows 更新或 WSUS 来提供所需的更新、修补程序的状态，以及所部署的修补程序的结果。 如果没有该信息，则更新管理无法正确报告所需的或已安装的修补程序。

#### <a name="resolution"></a>分辨率

双击显示为红色的异常，查看完整的异常消息。 查看下表，了解可能采取的解决方案或措施：

|异常  |解决方法或操作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 搜索 [ Windows 更新错误代码列表](https://support.microsoft.com/help/938205/windows-update-error-code-list)中的相关错误代码，以查找有关异常原因的其他详细信息。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 这些错误是网络连接问题。 请确保你的计算机具有与更新管理的适当网络连接。 请参阅[网络规划](../automation-update-management.md#ports)部分，了解所需的端口和地址的列表。        |
|`0x8024001E`| 更新操作未完成，因为服务或系统正在关闭。|
|`0x8024002E`| Windows 更新服务处于禁用状态。|
|`0x8024402C`     | 如果使用 WSUS 服务器，请确保注册表项 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 下 `WUServer` 和 `WUStatusServer` 的注册表值具有正确的 WSUS 服务器。        |
|`0x80072EE2`|网络连接问题或与配置的 WSUS 服务器通信时出现问题。 检查 WSUS 设置并确保可以从客户端访问它。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 请确保 Windows 更新服务 (wuauserv) 正在运行，并且未禁用。        |
|任何其他一般异常     | 在 Internet 上搜索可能的解决方案，并与本地 IT 支持人员合作。         |

`windowsupdate.log`查看有助于您尝试确定可能的原因。 有关如何读取日志的详细信息，请参阅[如何读取 windowsupdate.log 文件](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)。

此外，你可以下载并运行 [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以检查计算机上的 Windows 更新是否存在任何问题。

> [!NOTE]
> [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)指出它适用于 Windows 客户端，但它也适用于 Windows Server。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>场景：无法启动更新运行

#### <a name="issue"></a>问题

无法在 Linux 计算机上启动更新运行。

#### <a name="cause"></a>原因

Linux 混合辅助角色运行状况不正常。

#### <a name="resolution"></a>分辨率

请创建以下日志文件的副本并保留它以用于故障排除：

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>场景：启动更新运行后遇到错误

#### <a name="issue"></a>问题

启动更新运行后，在运行期间遇到错误。

#### <a name="cause"></a>原因

可能的原因包括：

* 包管理器运行状况不正常
* 特定包可能干扰了基于云的修补
* 其他原因

#### <a name="resolution"></a>分辨率

如果更新运行在 Linux 上成功启动后又失败，请检查运行中受影响的计算机的作业输出。 可以从计算机的包管理器查找特定的错误消息，可以对这些错误消息进行调查并对其采取操作。 更新管理要求包管理器正常运行才能成功进行更新部署。

某些情况下，包更新可能会干扰更新管理，因而会阻更新部署完成。 如果出现此情况，则必须从将来的更新运行中排除这些包，或者手动安装它们。

如果无法解决修补问题，请在下次更新部署启动之前创建以下日志文件的副本，并保留它以用于故障排除：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>未安装修补程序

### <a name="machines-do-not-install-updates"></a>计算机不安装更新

* 请尝试直接在计算机上运行更新。 如果计算机无法更新，请查阅[故障排除指南中的潜在错误列表](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。
* 如果更新在本地运行，请尝试按照[“从更新管理中删除 VM”](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management)中的说明在计算机上删除并重新安装代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>我知道有可用的更新，但不会在我的计算机上显示

* 如果将计算机配置为从 WSUS/SCCM 获取更新，但 WSUS/SCCM 尚未批准更新，通常会发生这种情况。
* 可以检查是否已针对 WSUS/SCCM 配置计算机，方法是[将“UseWUServer”注册表项交叉引用到此文档“通过编辑注册表配置自动更新”部分的注册表项](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**更新显示为已安装，但我在计算机上找不到它们**

* 更新通常会被其他更新替代。 有关详细信息，请参阅 [Windows 更新疑难解答指南中的“更新被替代”](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>**按 Linux 上的分类安装更新**

* 按分类（“关键更新和安全更新”）将更新部署到 Linux 有重要的注意事项，尤其是对 CentOS 来说。 这些[限制记录在“更新管理”概览页上](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>**KB2267602 始终缺失**

* KB2267602 是 [Windows Defender 定义更新](https://www.microsoft.com/wdsi/definitions)。 它每天更新一次。

## <a name="other"></a>场景：上面未列出我的问题

### <a name="issue"></a>问题

你遇到了未被列出的其他方案解决的问题。

### <a name="cause"></a>原因

配置错误或缺少注册表项可能会导致更新管理问题。

### <a name="resolution"></a>分辨率

删除注册表项`HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker`并重新启动**运行状况服务**。

你还可以使用以下 PowerShell 命令。

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
