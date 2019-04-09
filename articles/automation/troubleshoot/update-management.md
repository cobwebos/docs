---
title: 排查更新管理问题
description: 了解如何排查更新管理问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 22e3ea1c90946902fc2a16d947ff2884e5e0a44b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274580"
---
# <a name="troubleshooting-issues-with-update-management"></a>排查更新管理问题

本文讨论用于解决使用更新管理时可能遇到的问题的解决方案。

对于混合辅助角色代理，可使用代理故障排除程序来确定底层问题。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。 对于所有其他问题，请参阅以下有关可能问题的详细信息。

## <a name="general"></a>常规

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

#### <a name="resolution"></a>解决方法

1. 访问[网络规划](../automation-hybrid-runbook-worker.md#network-planning)，了解需要允许哪些地址和端口才能使更新管理正常工作。
2. 如果使用的是克隆的映像：
   1. 在 Log Analytics 工作区中，从已保存的搜索作用域配置为删除 VM`MicrosoftDefaultScopeConfig-Updates`如果它所示。 已保存的搜索位于工作区的“常规”下。
   2. 运行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. 运行 `Restart-Service HealthService` 重新启动 `HealthService`。 这将重新创建密钥，并生成新的 UUID。
   4. 如果这不起作用，对映像进行 sysprep 第一个并在这一事实后安装 MMA 代理。

### <a name="multi-tenant"></a>场景：在为另一个 Azure 租户中的计算机创建更新部署时收到链接订阅错误。

#### <a name="issue"></a>问题

尝试为另一个 Azure 租户中的计算机创建更新部署时收到以下错误：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>原因

当创建的更新部署包含另一个租户中的 Azure 虚拟机时会发生此错误。

#### <a name="resolution"></a>解决方法

你需要使用以下解决方法来进行安排。 你可以使用 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 和开关 `-ForUpdate` 来创建计划，然后使用 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 并将另一个租户中的计算机传递给 `-NonAzureComputer` 参数。 以下示例展示了如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>场景：不显示在一台计算机的 Azure Monitor 日志中的更新管理数据

#### <a name="issue"></a>问题

有显示为计算机**未评估**下**符合性**，但您看到的混合 Runbook 辅助角色但不是更新管理的 Azure Monitor 日志中的检测信号数据。

#### <a name="cause"></a>原因

混合 Runbook 辅助角色可能需要重新注册并重新安装。

#### <a name="resolution"></a>解决方法

遵循[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中的步骤来为 Windows 安装混合辅助角色，或者遵循[部署 Linux 混合 Runbook 辅助角色](../automation-linux-hrw-install.md)中的步骤为 Linux 进行安装。

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

#### <a name="resolution"></a>解决方法

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

#### <a name="resolution"></a>解决方法

检查网络并确保允许适当的端口和地址。 有关更新管理和混合 Runbook 辅助角色所需的端口和地址列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="unable-to-create-selfsigned-cert"></a>场景：无法创建自签名证书

#### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

混合 Runbook 辅助角色无法生成自签名证书

#### <a name="resolution"></a>解决方法

请验证系统帐户是否具有对文件夹 C:\ProgramData\Microsoft\Crypto\RSA 的读取权限，然后重试。

### <a name="hresult"></a>场景：计算机显示“未评估”，并显示 HResult 异常

#### <a name="issue"></a>问题

有计算机在“符合性”下显示“未评估”，并且能看到下面显示一条异常消息。

#### <a name="cause"></a>原因

计算机中的 Windows 更新或 WSUS 配置不正确。 更新管理依赖于 Windows 更新或 WSUS 来提供所需的更新、修补程序的状态，以及所部署的修补程序的结果。 如果没有该信息，则更新管理无法正确报告所需的或已安装的修补程序。

#### <a name="resolution"></a>解决方法

双击显示为红色的异常，查看完整的异常消息。 查看下表，了解可能采取的解决方案或措施：

|异常  |解决方法或操作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 搜索 [ Windows 更新错误代码列表](https://support.microsoft.com/help/938205/windows-update-error-code-list)中的相关错误代码，以查找有关异常原因的其他详细信息。        |
|`0x8024402C` 或 `0x8024401C`     | 这些错误是网络连接问题。 请确保你的计算机具有与更新管理的适当网络连接。 请参阅[网络规划](../automation-update-management.md#ports)部分，了解所需的端口和地址的列表。        |
|`0x8024402C`     | 如果使用 WSUS 服务器，请确保注册表项 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 下 `WUServer` 和 `WUStatusServer` 的注册表值具有正确的 WSUS 服务器。        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 请确保 Windows 更新服务 (wuauserv) 正在运行，并且未禁用。        |
|任何其他一般异常     | 在 Internet 上搜索可能的解决方案，并与本地 IT 支持人员合作。         |

查看`windowsupdate.log`可以帮助您尝试确定可能的原因。 有关如何读取日志的详细信息，请参阅[如何读取 Windowsupdate.log 文件](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)。

此外，你可以下载并运行 [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以检查计算机上的 Windows 更新是否存在任何问题。

> [!NOTE]
> [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)指出它适用于 Windows 客户端，但它也适用于 Windows Server。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>场景：无法启动更新运行

#### <a name="issue"></a>问题

无法在 Linux 计算机上启动更新运行。

#### <a name="cause"></a>原因

Linux 混合辅助角色运行状况不正常。

#### <a name="resolution"></a>解决方法

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

#### <a name="resolution"></a>解决方法

如果更新运行在 Linux 上成功启动后又失败，请检查运行中受影响的计算机的作业输出。 可以从计算机的包管理器查找特定的错误消息，可以对这些错误消息进行调查并对其采取操作。 更新管理要求包管理器正常运行才能成功进行更新部署。

某些情况下，包更新可能会干扰更新管理，因而会阻更新部署完成。 如果出现此情况，则必须从将来的更新运行中排除这些包，或者手动安装它们。

如果无法解决修补问题，请在下次更新部署启动之前创建以下日志文件的副本，并保留它以用于故障排除：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
