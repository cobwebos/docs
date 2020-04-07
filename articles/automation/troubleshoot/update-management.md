---
title: 使用 Azure 更新管理排除错误
description: 了解如何使用 Azure 中的更新管理解决方案来排除和解决问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 900853b1ca68c1c540223db670b1173f5bb2fa2b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754444"
---
# <a name="troubleshooting-issues-with-update-management"></a>排查更新管理问题

本文讨论在使用更新管理时可能会遇到的问题的解决方案。

混合辅助角色代理有一个代理疑难解答，用于确定根本问题。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。 对于所有其他问题，请使用以下故障排除指南。

如果在虚拟机 （VM） 上加入解决方案时遇到问题，请查看本地计算机上的 **"应用程序和服务日志**"下**的操作管理器**日志。 查找事件 ID 4502 的事件和包含`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`的事件详细信息。

以下部分重点介绍特定错误消息和每个错误消息的可能解决方法。 有关其他载入问题，请参阅[载入的疑难解答解决方案](onboarding.md)。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>方案：您会收到错误"无法启用更新解决方案"

### <a name="issue"></a>问题

当您尝试在自动化帐户中启用更新管理解决方案时，您会收到以下错误：

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

出现该错误的原因可能如下：

* 日志分析代理的网络防火墙要求可能未正确配置。 这可能会导致代理在解决 DNS URL 时失败。

* 解决方案定位配置错误，计算机未按预期接收更新。

* 您可能还注意到，计算机显示的`Non-compliant`"**符合性"** 状态。 同时，**代理桌面分析**将代理报告为`Disconnected`。

### <a name="resolution"></a>解决方法

* 运行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑难解答，具体取决于操作系统。

* 转到[网络规划](../automation-hybrid-runbook-worker.md#network-planning)以了解必须允许哪些地址和端口才能更新管理工作。  

* 转到[网络规划](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)以了解日志分析代理必须允许哪些地址和端口工作。

* 检查范围配置问题。 [范围配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)确定为解决方案配置了哪些计算机。 如果计算机显示在工作区中，但未显示在 [更新管理门户》中，则需要设置范围配置以定位计算机。 要了解作用域配置，请参阅[工作区中的板载计算机](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 按照[删除混合 Runbook 辅助角色](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)的步骤删除辅助角色配置。 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>方案：在更新管理中指示缺少的已取代更新

### <a name="issue"></a>问题

旧更新出现在自动化帐户中的"更新管理"中，即使它们已被取代，也显示为丢失。 取代的更新是您不必安装的更新，因为稍后的更新可以更正相同的漏洞。 更新管理忽略被取代的更新，使其不适用于取代的更新。 有关相关问题的信息，请参阅[更新被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

被取代的更新未正确指示为已拒绝，因此可以认为它们不适用。

### <a name="resolution"></a>解决方法

当被取代的更新变为 100% 不适用时，应将该更新的审批状态更改为`Declined`。 要更改所有更新的审批状态，

1. 在"自动化"帐户中，选择 **"更新管理**"以查看计算机状态。 请参阅[查看更新评估](../manage-update-multi.md#view-an-update-assessment)。

2. 检查被取代的更新，以确保它是 100% 不适用。 

3. 除非对更新有疑问，否则将更新标记为已拒绝。 

4. 选择 **"计算机**"，并在 **"合规性"** 列中强制重新扫描符合性。 请参阅[管理多台计算机的更新](../manage-update-multi.md)。

5. 对其他被取代的更新重复上述步骤。

6. 运行清理向导以从已拒绝的更新中删除文件。 

7. 对于 Windows 服务器更新服务 （WSUS），手动清理所有被取代的更新以刷新基础结构。

8. 定期重复此过程以更正显示问题并最小化用于更新管理的磁盘空间量。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>方案：计算机不会显示在更新管理下的门户中

### <a name="issue"></a>问题

您的机器有以下症状：

* 您的计算机从`Not configured`VM 的更新管理视图中显示。

* Azure 自动化帐户的更新管理视图中缺少计算机。

* 您有显示在**合规**下的计算机`Not assessed`。 但是，在 Azure 监视器日志中看到混合 Runbook 辅助角色中的检测信号数据，但不适用于更新管理。

### <a name="cause"></a>原因

此问题可能由本地配置问题或配置不正确的范围配置引起。

您可能需要重新注册并重新安装混合 Runbook 辅助角色。

您可能已在工作区中定义了已达到的配额，这阻止了进一步的数据存储。

### <a name="resolution"></a>解决方法

1. 运行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑难解答，具体取决于操作系统。

2. 确保计算机报告到正确的工作区。 有关如何验证此方面的指导，请参阅[验证代理连接到日志分析](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)。 还要确保此工作区链接到 Azure 自动化帐户。 要确认，请转到您的自动化帐户，并在 **"相关资源**"下选择 **"链接工作区**"。

3. 确保计算机显示在日志分析工作区中。 在链接到自动化帐户的日志分析工作区中运行以下查询：

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

4. 如果在查询结果中看不到计算机，则计算机最近未签入。 可能存在本地配置问题，您应该[重新安装代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 

5. 如果计算机显示在查询结果中，请检查范围配置问题。 [范围配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)确定为解决方案配置了哪些计算机。 如果计算机显示在工作区中，但未显示在 _Update 管理门户中，则必须配置范围配置以目标计算机为目标。 要了解如何执行此操作，请参阅[工作区中的板载计算机](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

6. 在工作区中，运行以下查询：

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

7. 如果得到结果`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`，工作区上定义了已到达的配额，并且阻止保存数据。 在工作区中，转到 **"使用情况"下****的数据卷管理**以及估计成本，并检查配额或删除配额。

8. 如果问题仍未解决，请按照["部署 Windows 混合 Runbook 辅助角色"](../automation-windows-hrw-install.md)中的步骤重新安装 Windows 的混合辅助角色。 对于 Linux，请按照[部署 Linux 混合 Runbook 辅助手册 中](../automation-linux-hrw-install.md)的步骤操作。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>方案：无法注册订阅的自动化资源提供程序

### <a name="issue"></a>问题

当您使用自动化帐户中的解决方案时，会发生以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

未在订阅中注册自动化资源提供程序。

### <a name="resolution"></a>解决方法

要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤：

1. 在门户底部的 Azure 服务列表中，选择 **"所有服务**"，然后在"常规服务"组中选择 **"订阅**"。
2. 选择订阅。
3. 在 **"设置"** 下，选择 **"资源提供程序**"。
4. 从资源提供程序列表中，验证`Microsoft.Automation`资源提供程序是否已注册。
5. 如果未列出，请按照`Microsoft.Automation`[资源提供程序注册"解决错误"](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步骤注册提供程序。

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="update-missed-machines"></a>方案：计划更新，动态计划未命中某些计算机

### <a name="issue"></a>问题

更新中包含的预览计算机并不都显示在计划运行期间修补的计算机列表中。

### <a name="cause"></a>原因

此问题可能有以下原因之一：

* 动态查询中定义的订阅未为注册的自动化资源提供程序配置。 
* 执行计划时，计算机不可用或没有适当的标记。

### <a name="resolution"></a>解决方法

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>未为已注册的自动化资源提供程序配置的订阅

如果未为自动化资源提供程序配置订阅，则无法查询或获取该订阅中的计算机上的信息。 使用以下步骤确保订阅的注册。

1. 在[Azure 门户](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)中，访问 Azure 服务列表。
2. 选择 **"所有服务**"，然后在"常规服务"组中选择 **"订阅**"。 
3. 查找部署范围中定义的订阅。
4. 在 **"设置"** 下，选择 **"资源提供程序**"。
5. 验证`Microsoft.Automation`资源提供程序是否已注册。
6. 如果未列出，请按照`Microsoft.Automation`[资源提供程序注册"解决错误"](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步骤注册提供程序。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>执行计划时计算机不可用或未正确标记

如果您的订阅已配置为自动化资源提供程序，但使用指定的[动态组](../automation-update-management-groups.md)运行更新计划时错过了某些计算机，请使用以下过程。

1. 在 Azure 门户中，打开自动化帐户并选择 **"更新管理**"。
2. 检查[更新管理历史记录](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment)以确定运行更新部署的确切时间。 
3. 对于您怀疑更新管理遗漏的计算机，请使用 Azure 资源图[查找计算机更改](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details)。 
4. 在运行更新部署之前，搜索相当长一段时间内的更改，例如一天。
5. 检查搜索结果中是否有任何系统更改（如删除或更新更改）到此时间段内的计算机。 这些更改可以更改计算机状态或标记，以便在部署更新时不会在计算机列表中选择计算机。
6. 根据需要调整计算机和资源设置，以更正计算机状态或标记问题。
7. 重新运行更新计划，以确保具有指定动态组的部署包括所有计算机。

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>方案：启用了用于更新管理解决方案的组件，而 VM 继续显示为已配置

### <a name="issue"></a>问题

在加入 15 分钟后继续在虚拟机上看到以下消息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

出现该错误的原因可能如下：

* 与自动化帐户的通信被阻止。

* 有一个重复的计算机名称与不同的源计算机指示。 当在不同的资源组中创建具有特定计算机名称的 VM 并报告到订阅中的同一物流代理工作区时，将发生此情况。

* 正在装入的 VM 映像可能来自未准备好安装 Microsoft 监视代理 （MMA） 的系统准备 （sysprep） 的克隆计算机。

### <a name="resolution"></a>解决方法

为了帮助确定 VM 的确切问题，请在链接到自动化帐户的日志分析工作区中运行以下查询：

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>与自动化帐户的通信被阻止

转到[网络规划](../automation-update-management.md#ports)以了解必须允许哪些地址和端口才能更新管理工作。

#### <a name="duplicate-computer-name"></a>重复的计算机名称

重命名 VM 以确保其环境中的唯一名称。

#### <a name="onboarded-image-from-cloned-machine"></a>克隆计算机的板载映像

如果使用克隆映像，则不同的计算机名称具有相同的源计算机 ID。 在这种情况下：

1. 在日志分析工作区中，如果显示`MicrosoftDefaultScopeConfig-Updates`范围配置，则从保存的搜索中删除 VM。 已保存的搜索位于工作区的“常规”下****。

2. 运行以下 cmdlet：

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 运行`Restart-Service HealthService`以重新启动运行状况服务。 此操作将重新创建密钥并生成新的 UUID。

4. 如果此方法不起作用，请先在映像上运行 sysprep，然后安装 MMA。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>方案：在为另一个 Azure 租户中的计算机创建更新部署时，您会收到链接的订阅错误

### <a name="issue"></a>问题

当您尝试为另一个 Azure 租户中的计算机创建更新部署时，您会遇到以下错误：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

当您创建的更新部署时，将 Azure VM 包含在更新部署中的另一个租户中，将发生此错误。

### <a name="resolution"></a>解决方法

使用以下解决方法来安排这些项目。 您可以使用`ForUpdate`[带有参数的"新建 AzureRm自动化计划](/powershell/module/azurerm.automation/new-azurermautomationschedule)cmdlet"创建计划。 然后，使用[New-AzureRm自动化软件更新配置](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
)cmdlet 并将另一个租户中的计算机传递给`NonAzureComputer`参数。 以下示例介绍如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>方案：无法解释的重新启动

### <a name="issue"></a>问题

即使您将 **"重新启动控制"** 选项设置为 **"从不重新启动**"，但安装更新后，计算机仍会重新启动。

### <a name="cause"></a>原因

Windows 更新可以由多个注册表项修改，其中任一注册表项都可以修改重新启动行为。

### <a name="resolution"></a>解决方法

通过编辑[用于管理重新启动的注册表和注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)，以确保计算机配置正确，查看[配置自动更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)下的注册表项。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>方案：计算机在更新部署中显示"无法启动"

### <a name="issue"></a>问题

计算机显示状态`Failed to start`。 查看计算机的特定详细信息时，您将看到以下错误：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

存在以下任一原因时，可能出现此错误：

* 这台机器已经不存在了。
* 机器已关闭且无法访问。
* 计算机有网络连接问题，因此无法联系到计算机上的混合工作线程。
* 有一个更新的MMA，改变了源计算机 ID。
* 如果达到自动化帐户中 2000 个并发作业的限制，则更新运行将受到限制。 每个部署都被视为作业，更新部署中的每台计算机都算作作业。 自动化帐户中当前运行的任何其他自动化作业或更新部署都计入并发作业限制。

### <a name="resolution"></a>解决方法

如果适用，请对更新部署使用[动态组](../automation-update-management-groups.md)。 此外：

* 验证机器是否仍然存在且可到达。 
* 如果计算机不存在，请编辑部署并删除计算机。
* 有关更新管理所需的端口和地址的列表，请参阅[网络规划](../automation-update-management.md#ports)部分，然后验证计算机是否满足这些要求。
* 使用混合 Runbook 工作线程代理疑难解答验证与混合 Runbook 辅助角色的连接。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。
* 在日志分析中运行以下查询，以查找源计算机 ID 已更改的环境中的计算机。 查找具有相同`Computer`值但值不同的`SourceComputerId`计算机。

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* 找到受影响的计算机后，编辑针对这些计算机的更新部署，然后删除并读取它们，`SourceComputerId`以反映正确的值。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>方案：在不部署的情况下安装更新

### <a name="issue"></a>问题

在更新管理中注册 Windows 计算机时，您将看到未部署即可安装的更新。

### <a name="cause"></a>原因

在 Windows 上，更新一旦可用即自动安装。 如果不计划将更新部署到计算机，则此行为可能会导致混淆。

### <a name="resolution"></a>解决方法

注册表`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`项默认为 4： `auto download and install`。

对于更新管理客户端，我们建议将此键设置为 3： `auto download but do not auto install`。

有关详细信息，请参阅[配置自动更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>方案：计算机已注册到其他帐户

### <a name="issue"></a>问题

看到以下错误消息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

计算机已连接到另一个工作区以进行更新管理。

### <a name="resolution"></a>解决方法

1. 按照["计算机"下的步骤不显示在"更新管理"下的门户中](#nologs)，以确保计算机报告到正确的工作区。
2. 通过[删除混合 Runbook 组](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)来清理计算机上的工件，然后重试。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>方案：计算机无法与服务通信

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

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>原因

代理、网关或防火墙可能阻止网络通信。 

### <a name="resolution"></a>解决方法

查看您的网络并确保允许适当的端口和地址。 有关更新管理和混合 Runbook Worker 所需的端口和地址的列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>方案：无法创建自签名证书

### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合 Runbook 工作线程无法生成自签名证书。

### <a name="resolution"></a>解决方法

验证系统帐户是否对**C：_程序数据\Microsoft_加密\RSA**文件夹具有读取访问权限，然后重试。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>方案：计划更新失败，出现超出维护窗口的错误

### <a name="issue"></a>问题

更新的默认维护窗口为 120 分钟。 您可以将维护窗口增加到最多 6 小时或 360 分钟。

### <a name="resolution"></a>解决方法

编辑任何失败的计划更新部署，并增加维护窗口。

有关维护窗口的详细信息，请参阅[安装更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>方案：机器显示为"未评估"并显示 HRESULT 异常

### <a name="issue"></a>问题

* 您有显示在`Not assessed`**"符合性"** 下的计算机，并在它们下方看到一条异常消息。
* 在门户中可以看到 HRESULT 错误代码。

### <a name="cause"></a>原因

更新代理（Windows 上的 Windows 更新代理;Linux 发行版的包管理器）配置不正确。 更新管理依赖于计算机的更新代理提供所需的更新、修补程序的状态以及已部署修补程序的结果。 如果没有此信息，更新管理将无法正确报告需要或安装的修补程序。

### <a name="resolution"></a>解决方法

尝试在计算机上本地执行更新。 如果此操作失败，则通常意味着存在更新代理配置错误。

此问题通常是由网络配置和防火墙问题引起的。 使用以下检查更正问题。

* 对于 Linux，请检查适当的文档，以确保您可以到达包存储库的网络终结点。

* 对于 Windows，请检查"更新"中列出的代理配置[未从 Intranet 终结点 （WSUS/SCCM） 下载](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)。

  * 如果计算机配置为 Windows 更新，请确保可以到达[HTTP/代理问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中描述的终结点。
  * 如果计算机配置为 Windows 服务器更新服务 （WSUS），请确保可以到达[由 WUServer 注册表项](/windows/deployment/update/waas-wu-settings)配置的 WSUS 服务器。

如果看到 HRESULT，双击以红色显示的异常以查看整个异常消息。 查看下表，查看潜在的解决方案或建议的操作：

|异常  |解决或行动  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows 更新错误代码列表中](https://support.microsoft.com/help/938205/windows-update-error-code-list)搜索相关错误代码，以查找有关异常原因的其他详细信息。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 这些表示网络连接问题。 确保计算机具有与更新管理的网络连接。 有关所需端口和地址的列表，请参阅[网络规划](../automation-update-management.md#ports)部分。        |
|`0x8024001E`| 更新操作未完成，因为服务或系统正在关闭。|
|`0x8024002E`| Windows 更新服务已禁用。|
|`0x8024402C`     | 如果使用 WSUS 服务器，请确保`WUServer``WUStatusServer``HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate`注册表项的注册表值和注册表项下的注册表值指定正确的 WSUS 服务器。        |
|`0x80072EE2`|与配置的 WSUS 服务器交谈时存在网络连接问题或问题。 检查 WSUS 设置，并确保可从客户端访问该服务。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 确保 Windows 更新服务 （wuauserv） 正在运行且未禁用。        |
|`0x80070005`| 访问被拒绝错误可能由以下任一原因引起：<br> 受感染的计算机<br> 未正确配置的 Windows 更新设置<br> 包含 %WinDir%_软件分发文件夹的文件权限错误<br> 系统驱动器 （C：） 上的磁盘空间不足。
|任何其他一般异常     | 在互联网上搜索可能的解决方案，并与您的本地 IT 支持合作。         |

查看 %Windir%*Windowsupdate.log 文件还可以帮助您确定可能的原因。 有关如何读取日志的详细信息，请参阅[如何读取 Windowsupdate.log 文件](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)。

您还可以下载并运行 Windows[更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以检查计算机上的 Windows 更新是否有任何问题。

> [!NOTE]
> [Windows 更新疑难解答](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)文档指示它用于 Windows 客户端，但它也适用于 Windows 服务器。

## <a name="scenario-update-run-returns-failed-status-linux"></a>方案：更新运行返回"失败"状态（Linux）

### <a name="issue"></a>问题

更新运行将启动，但在运行过程中遇到错误。

### <a name="cause"></a>原因

可能的原因：

* 包管理器不正常。
* 更新代理（适用于 Windows 的 WUA，Linux 的特定于版本解的软件包管理器）配置错误。
* 特定包干扰基于云的修补。
* 机器无法到达。
* 更新具有未解决的依赖项。

### <a name="resolution"></a>解决方法

如果在更新成功启动后运行期间发生故障，请在运行中检查来自受影响计算机的[作业输出](../manage-update-multi.md#view-results-of-an-update-deployment)。 您可能会从计算机中找到特定的错误消息，您可以研究和执行操作。 更新管理要求包管理器正常运行才能成功进行更新部署。

如果在作业失败之前立即看到特定的修补程序、包或更新，则可以尝试从下一个更新部署[中排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)这些项目。 要从 Windows 更新收集日志信息，请参阅[Windows 更新日志文件](/windows/deployment/update/windows-update-logs)。

如果无法解决修补问题，请复制以下日志文件，并在下一次更新部署开始之前保留该文件以进行故障排除。

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>未安装修补程序

### <a name="machines-dont-install-updates"></a>计算机未安装更新

请尝试直接在计算机上运行更新。 如果计算机无法应用更新，请参阅[故障排除指南中的潜在错误列表](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。

如果更新在本地运行，请尝试按照[更新管理中删除 VM](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)中的指南在计算机上删除和重新安装代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道更新是可用的，但它们不能在我的计算机上显示

如果计算机配置为从 WSUS 或 Microsoft 终结点配置管理器获取更新，但 WSUS 和配置管理器尚未批准更新，则通常会发生这种情况。

您可以通过在本文的`UseWUServer`[注册表部分](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)中将注册表键交叉引用注册表项来检查计算机是否配置为 WSUS 和 SCCM。

如果 WSUS 中未批准更新，则不会安装更新。 您可以通过运行以下查询来检查日志分析中的未批准更新。

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新显示为已安装，但我在计算机上找不到它们

更新通常会被其他更新替代。 有关详细信息，请参阅在 Windows 更新故障排除指南中[取代更新](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>按 Linux 上的分类安装更新

按分类（“关键更新和安全更新”）将更新部署到 Linux 有重要的注意事项，尤其是对 CentOS 来说。 这些限制记录在[更新管理概述页上](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 始终缺失

KB2267602 是 [Windows Defender 定义更新](https://www.microsoft.com/wdsi/definitions)。 它每天更新。

## <a name="next-steps"></a>后续步骤

如果您没有看到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
