---
title: 排查更新管理问题
description: 了解如何解决更新管理的问题。
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 952bcb85484e885d45876de1e4cf3326db0a146a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693396"
---
# <a name="troubleshooting-issues-with-update-management"></a>排查更新管理问题

本文讨论在使用更新管理时可能遇到的问题的解决方案。

混合辅助角色代理的代理疑难解答可确定基本问题。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。 对于所有其他问题，请使用以下故障排除指南。

如果尝试在虚拟机（VM）上载入解决方案时遇到问题，请检查本地计算机上的**应用程序和服务日志**下的**Operations Manager**日志中是否存在事件 ID 4502 的事件和包含的事件详细信息**EnterpriseManagement. 运行状况服务. AzureAutomation. eventmessage 以及**。

以下部分重点介绍了每个特定的错误消息和可能的解决方法。 有关其他载入问题，请参阅[解决解决方案载入](onboarding.md)问题。

## <a name="nologs"></a>情况：计算机不会显示在门户中的更新管理

### <a name="issue"></a>问题

你会遇到以下症状：

* 你的计算机**未**从 VM 的更新管理视图中进行配置。

* 你的 Azure 自动化帐户的更新管理视图中缺少你的计算机。

* 你的计算机**显示为 "** **符合性**"。 但是，可以在混合 Runbook 辅助角色 Azure Monitor 日志中看到检测信号数据，但不能查看更新管理。

### <a name="cause"></a>原因

此问题可能是由本地配置问题或不正确配置的作用域配置引起的。

可能需要重新注册并重新安装混合 Runbook 辅助角色。

你可能已在工作区中定义了已达到的配额，这会阻止进一步的数据存储。

### <a name="resolution"></a>分辨率

* 根据操作系统运行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑难解答。

* 请确保计算机正在向正确的工作区报告。 有关如何验证此方面的指导，请参阅[验证代理与 Log Analytics 的连接](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)。 此外，请确保此工作区已链接到 Azure 自动化帐户。 若要确认，请跳到自动化帐户，并选择 "**相关资源**" 下的 "**链接的工作区**"。

* 请确保计算机显示在 Log Analytics 工作区中。 在链接到自动化帐户的 Log Analytics 工作区中运行以下查询：

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  如果未在查询结果中看到您的计算机，则表明该计算机最近未签入，这意味着可能存在本地配置问题，应[重新安装代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 如果计算机显示在查询结果中，则需要验证在此列表的下一个项目符号项中指定的范围配置。

* 检查作用域配置问题。 [作用域配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)确定为解决方案配置的计算机。 如果你的计算机显示在你的工作区中，而不是显示在**更新管理**门户中，则需要将作用域配置配置为以计算机为目标。 若要了解如何执行此操作，请参阅[工作区中的板载计算机](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 在工作区中，运行以下查询：

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  如果你得到 `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 结果，则表示你的工作区上定义了一个配额，并且已停止保存数据。 在工作区中，请参阅**使用情况和估计成本** > **数据量管理**，并检查配额或将其删除。

* 如果这些步骤不能解决问题，请按照[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中的步骤重新安装适用于 Windows 的混合辅助角色。 或者，对于 Linux，[部署 Linux 混合 Runbook 辅助角色](../automation-linux-hrw-install.md)。

## <a name="rp-register"></a>方案：无法注册订阅的自动化资源提供程序

### <a name="issue"></a>问题

在自动化帐户中使用解决方案时，遇到以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在订阅中注册 Automation 资源提供程序。

### <a name="resolution"></a>分辨率

若要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤：

1. 在门户底部的 "Azure 服务" 列表中，选择 "**所有服务**"，然后在 "常规" 服务组中选择 "**订阅**"。
2. 选择订阅。
3. 在 "**设置**" 下，选择 "**资源提供程序**"。
4. 在资源提供程序列表中，验证是否注册了**Microsoft. Automation**资源提供程序。
5. 如果未列出此项，请按照[解决资源提供程序注册错误](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步骤进行**操作。**

## <a name="components-enabled-not-working"></a>方案：已启用更新管理解决方案的组件，现在正在配置此虚拟机

### <a name="issue"></a>问题

在加入 15 分钟后继续在虚拟机上看到以下消息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

发生此错误的原因如下：

- 与自动化帐户的通信被阻止。
- 要载入的 VM 可能来自克隆的计算机，该计算机未与安装的 Microsoft Monitoring Agent （MMA）经过系统准备。

### <a name="resolution"></a>分辨率

1. 请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)，了解哪些地址和端口必须允许更新管理才能工作。
2. 如果使用的是克隆映像：
   1. 在 Log Analytics 工作区中，从已保存的 `MicrosoftDefaultScopeConfig-Updates` 搜索中删除 VM （如果显示）。 已保存的搜索位于工作区的“常规”下。
   2. 运行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`。
   3. 运行 `Restart-Service HealthService` 重新启动 `HealthService`。 这将重新创建密钥并生成新的 UUID。
   4. 如果此方法不起作用，请先在映像上运行 sysprep，然后安装 MMA。

## <a name="multi-tenant"></a>方案：在为另一个 Azure 租户中的计算机创建更新部署时收到链接订阅错误

### <a name="issue"></a>问题

尝试在另一个 Azure 租户中为计算机创建更新部署时遇到以下错误：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

当你创建的更新部署包含在更新部署中包含的另一个租户中的 Azure Vm 时，会发生此错误。

### <a name="resolution"></a>分辨率

使用以下解决方法来获取已计划的这些项目。 可以将[或者使用 set-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 与 `-ForUpdate` 开关一起使用来创建计划。 然后，使用[AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 并将另一个租户中的计算机传递到 `-NonAzureComputer` 参数。 以下示例介绍如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>方案：未解释的重新启动

### <a name="issue"></a>问题

即使已将**重新启动控制**选项设置为 "**从不重新启动**"，在安装更新后，计算机仍将重新启动。

### <a name="cause"></a>原因

可以通过多个注册表项来修改 Windows 更新，其中任何一个都可以修改重启行为。

### <a name="resolution"></a>分辨率

通过编辑[用于管理重启](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的注册表和注册表项，查看 "[配置自动更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry)" 下列出的注册表项，以确保正确配置了计算机。

## <a name="failed-to-start"></a>方案：计算机在更新部署中显示 "无法启动"

### <a name="issue"></a>问题

计算机显示 "**无法启动**" 状态。 查看计算机的特定详细信息时，将看到以下错误：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

存在以下任一原因时，可能出现此错误：

* 计算机不再存在。
* 计算机已关闭且无法访问。
* 计算机出现网络连接问题，因此无法访问计算机上的混合辅助角色。
* 已更新 SourceComputerId 的 MMA 的更新。
* 如果在自动化帐户中达到了2000并发作业的限制，则会限制更新运行。 每个部署都被视为一个作业，更新部署中的每台计算机都作为一个作业计数。 当前在你的自动化帐户中运行的任何其他自动化作业或更新部署都会计入并发作业限制。

### <a name="resolution"></a>分辨率

如果适用，请使用[动态组](../automation-update-management-groups.md)作为更新部署。 附加

* 验证计算机是否仍然存在并且可以访问。 如果它不存在，请编辑部署并删除计算机。
* 请参阅[网络规划](../automation-update-management.md#ports)部分，了解更新管理所需的端口和地址列表，并验证计算机是否满足这些要求。
* 在 Log Analytics 中运行以下查询，查找环境中其 `SourceComputerId` 已更改的计算机。 查找 `Computer` 值相同但 `SourceComputerId` 值不同的计算机。 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   找到受影响的计算机后，编辑针对这些计算机的更新部署，然后删除并重新添加它们，使 `SourceComputerId` 反映正确的值。

## <a name="updates-nodeployment"></a>方案：在不部署的情况下安装更新

### <a name="issue"></a>问题

当你在更新管理中注册 Windows 计算机时，你将看到安装的更新而不进行部署。

### <a name="cause"></a>原因

在 Windows 上，更新一旦可用就会自动安装。 如果未计划部署到计算机的更新，此行为可能会导致混淆。

### <a name="resolution"></a>分辨率

@No__t_0 注册表项默认设置为4：**自动下载和安装**。

对于更新管理客户端，建议将此项设置为3：**自动下载，但不自动安装**。

有关详细信息，请参阅[配置自动更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="machine-already-registered"></a>方案：计算机已注册到其他帐户

### <a name="issue"></a>问题

看到以下错误消息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

此计算机已载入另一工作区进行更新管理。

### <a name="resolution"></a>分辨率

1. 按照["更新管理中的" 计算机未显示](#nologs)的步骤 "下的步骤，确保计算机报告到正确的工作区。
2. [删除混合 runbook 组](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)，清除计算机上的旧项目，然后重试。

## <a name="machine-unable-to-communicate"></a>方案：计算机无法与服务进行通信

### <a name="issue"></a>问题

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

### <a name="cause"></a>原因

代理、网关或防火墙可能会阻止网络通信。

### <a name="resolution"></a>分辨率

查看网络并确保允许使用适当的端口和地址。 有关更新管理和混合 Runbook 辅助角色所需的端口和地址的列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="unable-to-create-selfsigned-cert"></a>方案：无法创建自签名证书

### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合 Runbook 辅助角色无法生成自签名证书。

### <a name="resolution"></a>分辨率

验证系统帐户对**C:\ProgramData\Microsoft\Crypto\RSA**文件夹是否具有读取权限，然后重试。

## <a name="mw-exceeded"></a>方案：计划的更新失败，并出现 MaintenanceWindowExceeded 错误

### <a name="issue"></a>问题

更新的默认维护时段为120分钟。 最多可将维护时段增加6小时或360分钟。

### <a name="resolution"></a>分辨率

编辑任何失败的计划更新部署，并增加维护时段。

有关维护时段的详细信息，请参阅[安装更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="hresult"></a>方案：计算机显示为 "未评估" 并显示 HResult 异常

### <a name="issue"></a>问题

* 有计算机在“符合性”下显示“未评估”，并且能看到下面显示一条异常消息。
* 你的计算机显示为未评估。
* 门户中会显示 HRESULT 错误代码。

### <a name="cause"></a>原因

更新代理（Windows 上的 Windows 更新代理; Linux 分发的包管理器）未正确配置。 更新管理依赖于计算机的更新代理来提供所需的更新、修补程序的状态以及所部署的修补程序的结果。 如果没有此信息，更新管理无法正确报告所需或已安装的修补程序。

### <a name="resolution"></a>分辨率

尝试在计算机上本地执行更新。 如果此操作失败，则通常表示更新代理存在配置错误。

此问题通常是由网络配置和防火墙问题导致的。 请尝试以下做法：

* 对于 Linux，请查看相应的文档，以确保可以访问包存储库的网络终结点。
* 对于 Windows，请检查 "[更新不从 intranet 终结点下载（WSUS/SCCM）"](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)中所列的代理配置。
  * 如果为 Windows 更新配置了计算机，请确保可以访问[与 HTTP/proxy 相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中所述的终结点。
  * 如果为 Windows Server Update Services （WSUS）配置了计算机，请确保可以访问由[WUServer 注册表项](/windows/deployment/update/waas-wu-settings)配置的 WSUS 服务器。

如果看到 HRESULT，请双击以红色显示的异常，以查看整个异常消息。 请查看下表，了解可能的解决方案或建议的操作：

|异常  |解决方法或操作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows 更新错误代码列表](https://support.microsoft.com/help/938205/windows-update-error-code-list)中搜索相关错误代码，查找有关异常原因的其他详细信息。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 这表明网络连接问题。 请确保计算机已通过网络连接到更新管理。 请参阅[网络规划](../automation-update-management.md#ports)部分，了解所需端口和地址的列表。        |
|`0x8024001E`| 更新操作未完成，因为服务或系统正在关闭。|
|`0x8024002E`| Windows 更新服务处于禁用状态。|
|`0x8024402C`     | 如果你使用的是 WSUS 服务器，请确保 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 注册表项下的 `WUServer` 和 `WUStatusServer` 的注册表值指定正确的 WSUS 服务器。        |
|`0x80072EE2`|与已配置的 WSUS 服务器通信时出现网络连接问题或问题。 检查 WSUS 设置，并确保可以从客户端访问该服务。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 请确保 Windows 更新服务（wuauserv）正在运行且未禁用。        |
|任何其他一般异常     | 针对可能的解决方案在 internet 上运行搜索，并与本地 IT 支持部门合作。         |

查看 Windowsupdate.log 文件还可以帮助你确定可能的原因。 有关如何读取日志的详细信息，请参阅[如何读取 windowsupdate.log 文件](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)。

你还可以下载并运行[Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，检查计算机上 Windows 更新是否有任何问题。

> [!NOTE]
> [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)文档表明它适用于 windows 客户端，但它也适用于 windows Server。

## <a name="scenario-update-run-returns-failed-status"></a>方案：更新运行返回 "失败" 状态

### <a name="issue"></a>问题

更新运行开始，但在运行期间遇到错误。

### <a name="cause"></a>原因

可能的原因：

* 程序包管理器不正常。
* 更新代理（用于 Windows 的 WUA，适用于 Linux 的发行版包管理器）配置错误。
* 特定包干扰基于云的修补。
* 计算机无法访问。
* 更新具有未解析的依赖关系。

### <a name="resolution"></a>分辨率

如果在更新运行期间发生故障，则在运行期间检查受影响计算机的[作业输出](../manage-update-multi.md#view-results-of-an-update-deployment)。 你可能会从你的计算机中查找特定的错误消息，你可以进行研究并对其采取措施。 更新管理要求包管理器正常运行才能成功进行更新部署。

如果在作业失败之前立即显示特定修补程序、包或更新，则可以尝试从下一次更新部署中[排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)这些修补程序、包或更新。 若要从 Windows 更新收集日志信息，请参阅[Windows 更新日志文件](/windows/deployment/update/windows-update-logs)。

如果无法解决修补问题，请在下一个更新部署开始*之前*，复制以下日志文件并将其保留以供故障排除：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>未安装修补程序

### <a name="machines-dont-install-updates"></a>计算机不安装更新

* 请尝试直接在计算机上运行更新。 如果计算机无法应用这些更新，请参阅[故障排除指南中的潜在错误列表](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。
* 如果更新以本地方式运行，请遵循[从更新管理删除 VM 中](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management)的指南，在计算机上删除并重新安装代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道有可用更新，但它们在我的计算机上未显示为可用

* 如果将计算机配置为从 WSUS 或 System Center Configuration Manager （SCCM）获取更新，但 WSUS 和 SCCM 未批准更新，则通常会发生这种情况。
* 你可以通过[在本文的 "通过编辑注册表配置自动更新" 部分中，通过交叉引用 UseWUServer 注册表项来](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)检查是否为 WSUS 和 SCCM 配置了计算机。
* 如果未在 WSUS 中批准更新，则不会安装更新。 可以通过运行以下查询，在 Log Analytics 中检查未批准的更新：

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新显示为 "已安装"，但是我在计算机上找不到它们

* 更新通常会被其他更新替代。 有关详细信息，请参阅 Windows 更新故障排除指南中的[更新已被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>按 Linux 上的分类安装更新

* 按分类（“关键更新和安全更新”）将更新部署到 Linux 有重要的注意事项，尤其是对 CentOS 来说。 [更新管理概述 "页](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)上介绍了这些限制。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 一直丢失

* KB2267602 是 [Windows Defender 定义更新](https://www.microsoft.com/wdsi/definitions)。 每日更新。

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 使用官方 Microsoft Azure 帐户连接[@AzureSupport](https://twitter.com/azuresupport)，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
