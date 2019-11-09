---
title: 在 Azure 自动化中执行 Runbook
description: 详细介绍如何处理 Azure 自动化中的 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f8fa4c378ed394930a4018c58b99ed919cbc2c2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886969"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

在 Azure 自动化中启动 Runbook 时，会创建一个作业。 作业是 Runbook 的单一执行实例。 将分配一个 Azure 自动化工作线程来运行每个作业。 尽管辅助角色由多个 Azure 帐户共享，但不同自动化帐户中的作业是相互独立的。 无法控制要由哪个辅助角色为作业请求提供服务。 一个 runbook 可以同时运行多个作业。 可以重用同一自动化帐户中的作业的执行环境。 同时运行的作业越多，就越可能将其分派到同一个沙盒中。 在同一沙盒进程中运行的作业可能会相互影响，一个示例就是运行 `Disconnect-AzureRMAccount` cmdlet。 运行此 cmdlet 将断开共享沙盒进程中的每个 Runbook 作业。 在 Azure 门户中查看 Runbook 列表时，列表中会列出为每个 Runbook 启动的所有作业的状态。 可以查看每个 runbook 的作业列表以跟踪每个作业的状态。 作业日志最长可存储 30 天。 有关不同作业状态的说明，请参阅[作业状态](#job-statuses)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

下图显示 [PowerShell Runbooks](automation-runbook-types.md#powershell-runbooks)、[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 的 Runbook 作业生命周期。

![作业状态 - PowerShell 工作流](./media/automation-runbook-execution/job-statuses.png)

作业可以通过与 Azure 订阅建立连接来访问 Azure 资源。 仅当数据中心内的资源可从公有云访问时，作业才能访问这些资源。

## <a name="where-to-run-your-runbooks"></a>运行 runbook 的位置

Azure 自动化中的 Runbook 可以在 Azure 中的沙盒上运行，也可以在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行。 沙盒是 Azure 中可以由多个作业使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。 混合 Runbook 辅助角色既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一台或多台指定的计算机。 大多数 runbook 都可在 Azure 沙盒中轻松运行。 在某些特定方案中，可能会建议选择混合 Runbook（而不是 Azure 沙盒）执行 Runbook。 请参阅下表，了解一些示例方案：

|任务|最佳选择|说明|
|---|---|---|
|与 Azure 资源集成|Azure 沙盒|托管在 Azure 中，身份验证更为简单。 如果使用的是 Azure VM 上的混合 Runbook 辅助角色，则可使用 [Azure 资源的托管标识](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|管理 Azure 资源的最佳性能|Azure 沙盒|脚本在同一环境中运行，因此延迟更低|
|最大程度减少运营成本|Azure 沙盒|没有计算开销，不需要 VM|
|长期脚本|混合 Runbook 辅助角色|Azure 沙盒具有[对资源的限制](../azure-subscription-service-limits.md#automation-limits)|
|与本地服务进行交互|混合 Runbook 辅助角色|可以直接访问主机|
|需要第三方软件和可执行文件|混合 Runbook 辅助角色|管理操作系统并且可以安装软件|
|使用 Runbook 监视文件或文件夹|混合 Runbook 辅助角色|在混合 Runbook 辅助角色上，使用[观察程序任务](automation-watchers-tutorial.md)|
|资源密集型脚本|混合 Runbook 辅助角色| Azure 沙盒具有[对资源的限制](../azure-subscription-service-limits.md#automation-limits)|
|使用具有特定需求的模块| 混合 Runbook 辅助角色|下面是一些示例：</br> **WinSCP** - winscp.exe 上的依赖项 </br> **IISAdministration** - 需要启用 IIS|
|安装需要安装程序的模块|混合 Runbook 辅助角色|沙盒的模块必须是可复制的|
|使用需要不同于 4.7.2 版本的 .NET Framework 的 Runbook 或模块|混合 Runbook 辅助角色|自动化沙盒的 .NET Framework 4.7.2 无法进行升级|
|需要提升权限的脚本|混合 Runbook 辅助角色|沙盒不允许提升权限。 若要解决此问题，请使用混合 Runbook 辅助角色，可以在运行需要提升权限的命令时关闭 UAC 并使用 `Invoke-Command`|
|需要访问 WMI 的脚本|混合 Runbook 辅助角色|在云中的沙盒中运行的作业[无权访问 WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook 行为

Runbook 基于其内部定义的逻辑执行操作。 如果 Runbook 中断，则 Runbook 将在开始时重启。 这种行为要求 runbook 以某种方式进行编写，在此方式中，如果存在瞬态问题，runbook 支持重启。

从在 Azure 沙盒中运行的 Runbook 启动的 PowerShell 作业在完整语言模式下可能无法运行。 若要了解有关 PowerShell 语言模式的详细信息，请参阅 [PowerShell 语言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)。 有关如何与 Azure 自动化中的作业进行交互的更多详细信息，请参阅[使用 PowerShell 检索作业状态](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>创建资源

如果由脚本创建资源，则在尝试再次创建资源之前，应查看资源是否已经存在。 以下示例显示了一个基本示例：

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>时间依赖脚本

创作 runbook 时，应仔细考虑。 如前所述，需要以某种方式编写 runbook，使其可靠并且能够处理可能导致 runbook 重启或失败的瞬态错误。 如果某个 Runbook 失败，将会重试该 Runbook。 如果 Runbook 在时间约束内正常运行，则应在 Runbook 中实现检查执行时间的逻辑，确保只在特定时间运行启动、关闭或横向扩展等操作。

> [!NOTE]
> Azure 沙盒进程上的本地时间设置为 UTC 时间。 对 runbook 中的日期和时间的计算需要考虑到这一点。

### <a name="tracking-progress"></a>跟踪进程

实际上模块化创作 runbook 是很好的做法。 这意味着要在 runbook 中构造逻辑，以便能够轻松重用和重启。 跟踪 Runbook 的进度能够很好地确保在出现问题时正确执行 Runbook 中的逻辑。 跟踪 runbook 进程的一些可能方法是使用外部源（如存储帐户、数据库或共享文件）。 通过在外部跟踪状态可以在 Runbook 中创建逻辑，用于先检查 Runbook 所执行的最后一个操作的状态。 然后根据结果跳过或继续执行 Runbook 中的特定任务。

### <a name="prevent-concurrent-jobs"></a>预防并发作业

如果同时运行多个作业，某些 runbook 可能会表现得很奇怪。 在此情况下，重要的是实现检查逻辑，查看 runbook 是否已有正在运行的作业。 以下示例显示了如何执行此行为的基本操作：

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>使用多个订阅

在创作处理多个订阅的 Runbook 时，Runbook 需要使用 [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet 来确保不会从可能在同一个沙盒中运行的另一个 Runbook 检索到你的身份验证上下文。 然后，你需要使用 `-AzureRmContext` cmdlet 上的 `AzureRM` 参数并将其传递给适当的上下文。

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>处理异常

创作脚本时，必须能够处理异常和潜在的间歇性故障。 下面是用于处理 Runbook 异常或间歇性问题的一些方法：

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 首选项变量确定 PowerShell 如何对非终止性错误做出响应。 终止性错误不受 `$ErrorActionPreference` 的影响，它们始终会终止。 使用 `$ErrorActionPreference` 时，正常的非终止性错误（例如 `PathNotFound` cmdlet 返回的 `Get-ChildItem`）会阻止 Runbook 完成。 以下示例演示 `$ErrorActionPreference` 的用法。 因为脚本将会停止，最后一个 `Write-Output` 行永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

在 PowerShell 脚本中使用 [Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 可以帮助处理终止性错误。 使用 Try Catch 可以捕获特定的异常或一般的异常。 应该使用 Catch 语句来跟踪错误或尝试处理错误。 以下示例尝试下载一个不存在的文件。 它捕获 `System.Net.WebException` 异常，如果存在另一个异常，则返回最后一个值。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

可以使用 [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 来生成终止性错误。 在 Runbook 中定义自己的逻辑时，此语句非常有用。 如果满足停止脚本的特定条件，则可以使用 `throw` 来停止脚本。 以下示例使用 `throw` 要求在计算机上提供某个函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可执行文件或调用进程

在 Azure 沙盒中运行的 Runbook 不支持调用进程（例如 .exe 或 subprocess.call）。 这是因为，Azure 沙盒是在容器中运行的共享进程，它可能无权访问所有底层 API。 对于需要第三方软件或调用子进程的方案，建议在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上执行 runbook。

### <a name="device-and-application-characteristics"></a>设备和应用程序特征

在 Azure 沙盒中运行的 Runbook 作业无权访问任何设备或应用程序特征。 最常用于在 Windows 上查询性能指标的 API 是 WMI。 其中的一些常见指标是内存和 CPU 使用率。 但是，使用哪个 API 并不重要。 在云中运行的作业无权访问基于 Web 的企业管理（WBEM）的 Microsoft 实现，这是基于通用信息模型（CIM）构建的，这是用于定义设备和应用程序特征的行业标准。

## <a name="job-statuses"></a>作业状态

下表描述了作业的各种可能状态。 PowerShell 具有两种类型的错误，终止性和非终止性错误。 终止性错误在发生时将 runbook 状态设置为“失败”。 非终止性错误允许在发生错误时继续运行脚本。 非终止错误的示例为：对不存在的路径使用 `Get-ChildItem` cmdlet。 PowerShell 发现路径不存在，然后引发错误，并继续转到下一文件夹。 此错误不会将 runbook 状态设置为“失败”，并且可能标记为“完成”。 若要强制 runbook 在发生非终止性错误时停止，可以使用 `-ErrorAction Stop` cmdlet。

| 状态 | 说明 |
|:--- |:--- |
| 已完成 |作业已成功完成。 |
| 已失败 |对于[图形 Runbook 和 PowerShell 工作流 Runbook](automation-runbook-types.md)，Runbook 未能编译。 对于 [PowerShell 脚本 runbook](automation-runbook-types.md)，runbook 未能启动或作业遇到异常。 |
| 失败，正在等待资源 |作业失败，因为它已达到 [公平份额](#fair-share) 限制三次，并且每次都已从同一个检查点或 Runbook 开始处启动。 |
| 已排队 |作业正在等待提供自动化工作线程的资源，以便能够启动。 |
| 正在启动 |作业已分配给辅助角色，并且系统正在将它启动。 |
| 正在恢复 |系统正在恢复已暂停的作业。 |
| 正在运行 |作业正在运行。 |
| 正在运行，正在等待资源 |作业已卸载，因为它已达到[公平份额](#fair-share)限制。 片刻之后，它将从其上一个检查点恢复。 |
| 已停止 |作业在完成之前已被用户停止。 |
| 正在停止 |系统正在停止作业。 |
| 已挂起 |作业已被用户、系统或 Runbook 中的命令暂停。 如果 runbook 没有检查点，它将从 runbook 的开端启动。 如果它有检查点，它将重新启动并从其上一个检查点继续。 只有发生异常时，系统才会将 Runbook 挂起。 默认情况下，ErrorActionPreference 设置为“继续”，表示出错时作业将保持运行。 如果此首选项变量设置为“停止”，则出错时作业会挂起。 仅适用于[图形 Runbook 和 PowerShell 工作流 Runbook](automation-runbook-types.md)。 |
| 正在暂停 |系统正在尝试按用户请求暂停作业。 Runbook 只有在达到其下一个检查点后才能挂起。 如果 Runbook 越过了最后一个检查点，则只有在完成后才能挂起。 仅适用于[图形 Runbook 和 PowerShell 工作流 Runbook](automation-runbook-types.md)。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>从 Azure 门户查看作业状态

可以查看所有 runbook 作业的概述状态或在 Azure 门户中深入了解特定 runbook 作业的详细信息。 此外，还可配置与 Log Analytics 工作区的集成，以转发 runbook 作业状态和作业流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

### <a name="automation-runbook-jobs-summary"></a>自动化 Runbook 作业摘要

在所选的“自动化帐户”右侧，可在“作业统计信息”磁贴下看到所有 Runbook 作业的摘要。

![作业统计信息磁贴](./media/automation-runbook-execution/automation-account-job-status-summary.png)

此磁贴显示执行的所有作业的作业状态计数和图形表示形式。

单击磁贴可显示“作业”页，此页包括所有已执行作业的摘要列表。 此页显示状态、开始时间和完成时间。

![自动化帐户作业页](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

可以通过选择“筛选作业”来筛选作业列表并筛选特定的 runbook 和作业状态，或从下拉列表中的时间范围中进行搜索。

![筛选作业状态](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，可通过从自动化帐户中的“runbook”页上选择特定的 runbook，并选择“作业”磁贴，来查看该 runbook 的作业摘要详情。 此操作将显示“作业”页，在此可以单击作业记录，查看作业详细信息和输出。

![自动化帐户作业页](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>作业摘要

可以查看为特定 Runbook 创建的所有作业的列表及其最新状态。 可以按作业状态以及上次对作业进行更改的日期范围筛选此列表。 单击作业的名称可以查看其详细信息和输出。 作业的详细视图包括提供给该作业的 Runbook 参数值。

可以使用以下步骤查看 Runbook 的作业。

1. 在 Azure 门户中，选择“自动化”，然后选择自动化帐户的名称。
2. 从中心选择“Runbook”，然后在“Runbook”页的列表中选择一个 Runbook。
3. 在所选 runbook 的页上，单击“作业”磁贴。
4. 单击列表中的一个作业，然后可在 runbook 作业详细信息页上查看其详细信息和输出。

## <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 检索作业状态

可以使用 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) 检索为 Runbook 创建的作业和特定作业的详细信息。 如果在 PowerShell 中使用 [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) 启动 Runbook，则会返回生成的作业。 使用 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) 可以获取作业的输出。

以下示例命令检索示例 Runbook 的最后一个作业，并显示其状态、为 Runbook 参数提供的值以及作业的输出。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

以下示例检索特定作业的输出，并返回每个记录。 如果其中一个记录出现异常，将写出异常而不是值。 此行为非常有用，因为异常可以提供在输出过程中无法正常记录的其他信息。

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>从活动日志中获取详细信息

可以从自动化帐户的活动日志中检索其他详细信息，例如启动了 Runbook 的人员或帐户。 以下 PowerShell 示例提供运行相关 Runbook 的最后一个用户：

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>公平份额

为了在云中的所有 Runbook 之间共享资源，Azure 自动化会暂时卸载或停止已运行三小时以上的所有作业。 [基于 PowerShell 的 Runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python Runbook](automation-runbook-types.md#python-runbooks) 的作业将停止且不会重启，作业状态显示“已停止”。

对于长期任务，建议使用[混合 Runbook 辅助角色](automation-hrw-run-runbooks.md#job-behavior)。 混合 Runbook 辅助角色不受公平份额限制，并且不会限制 runbook 的执行时间。 其他作业[限制](../azure-subscription-service-limits.md#automation-limits)适用于 Azure 沙盒和混合 Runbook 辅助角色。 虽然混合 Runbook 辅助角色不受 3 小时公平份额限制的限制，但应开发在混合 Runbook 辅助角色上运行的 runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种选择是通过使用子 runbook 来优化 runbook。 如果 runbook 在多个资源上遍历同一函数，例如在多个数据库上执行某个数据库操作，可将该函数移到[子 runbook](automation-child-runbooks.md)，并使用 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 进行调用。 各个子 runbook 是在单独的进程中并行执行的。 此行为降低了完成父 runbook 所需的时间总量。 如果有在子 Runbook 完成后执行的操作，可使用 Runbook 中的 [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet 检查每个子 Runbook 的作业状态。

## <a name="next-steps"></a>后续步骤

* 若要详细了解可用于在 Azure 自动化中启动 Runbook 的不同方法，请参阅 [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)（在 Azure 自动化中启动 Runbook）
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
