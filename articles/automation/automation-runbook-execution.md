---
title: 在 Azure 自动化中执行 Runbook
description: 详细介绍如何处理 Azure 自动化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 09122581a3ade4e741a29996b7202ce0f96d074b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145531"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

通过 Azure Automation 中的流程自动化，你可以创建和管理 PowerShell、PowerShell 工作流和图形 runbook。 有关详细信息，请参阅[Azure 自动化 runbook](automation-runbook-types.md)。 

自动基于在其中定义的逻辑执行 runbook。 如果 runbook 中断，它将在开始时重新启动。 此行为需要你编写支持在发生暂时性问题时重新启动的 runbook。

在 Azure 自动化中启动 runbook 会创建一个作业，该作业是 runbook 的单个执行实例。 每个作业通过与 Azure 订阅建立连接来访问 Azure 资源。 仅当数据中心的资源可从公有云访问时，作业才能访问这些资源。

Azure Automation 分配一个辅助角色，以便在执行 runbook 的过程中运行每个作业。 尽管辅助角色由多个 Azure 帐户共享，但不同自动化帐户中的作业是相互独立的。 无法控制作业请求的工作线程。

查看 Azure 门户中的 runbook 列表时，它会显示已为每个 runbook 启动的每个作业的状态。 Azure Automation 最多存储30天的作业日志。

下图显示了[powershell runbook](automation-runbook-types.md#powershell-runbooks)、 [powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和[图形 runbook](automation-runbook-types.md#graphical-runbooks)的 runbook 作业生命周期。

![作业状态 - PowerShell 工作流](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="where-to-run-your-runbooks"></a>运行 runbook 的位置

Azure Automation 中的 runbook 可以在 Azure 沙盒或[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行。 当 runbook 旨在针对 Azure 中的资源进行身份验证和运行时，它们将在 Azure 沙箱中运行，这是多个作业可以使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。

>[!NOTE]
>Azure 沙箱环境不支持交互式操作。 它还需要对进行 Win32 调用的 runbook 使用本地 MOF 文件。

你可以使用混合 Runbook 辅助角色直接在托管角色的计算机上或在环境中的本地资源上运行 runbook。 Azure Automation 存储和管理 runbook，然后将其传递给一个或多个已分配的计算机。

下表列出了一些 runbook 执行任务，其中每个任务都列出了建议的执行环境。

|任务|建议|注意|
|---|---|---|
|与 Azure 资源集成|Azure 沙盒|在 Azure 中托管，身份验证更简单。 如果在 Azure VM 上使用混合 Runbook 辅助角色，则可以使用[azure 资源的托管标识](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。|
|获得最佳性能来管理 Azure 资源|Azure 沙盒|脚本运行在相同的环境中，延迟较低。|
|最大程度减少运营成本|Azure 沙盒|不需要计算开销，也无需 VM。|
|执行长时间运行的脚本|混合 Runbook 辅助角色|Azure 沙箱具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|与本地服务交互|混合 Runbook 辅助角色|可以直接访问主机、其他云环境中的资源或本地环境中的资源。 |
|需要第三方软件和可执行文件|混合 Runbook 辅助角色|你可以管理操作系统并安装软件。|
|使用 Runbook 监视文件或文件夹|混合 Runbook 辅助角色|在混合 Runbook 辅助角色上使用[观察程序任务](automation-watchers-tutorial.md)。|
|运行资源密集型脚本|混合 Runbook 辅助角色| Azure 沙箱具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定要求的模块| 混合 Runbook 辅助角色|一些示例如下：</br> WinSCP - winscp.exe 上的依赖项 </br> IIS 管理-依赖于启用或管理 IIS。|
|使用安装程序安装模块|混合 Runbook 辅助角色|沙盒的模块必须支持复制。|
|使用需要不同于4.7.2 的 .NET Framework 版本的 runbook 或模块|混合 Runbook 辅助角色|自动化沙盒支持 .NET Framework 4.7.2，不支持升级到不同版本。|
|运行需要提升的脚本|混合 Runbook 辅助角色|沙盒不允许提升。 使用混合 Runbook 辅助角色，你可以在运行需要提升的命令时关闭 UAC 并使用[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。|
|运行需要访问 Windows Management Instrumentation （WMI）的脚本|混合 Runbook 辅助角色|在云中沙盒中运行的作业不能访问 WMI 提供程序。 |

## <a name="using-modules-in-your-runbooks"></a>在 runbook 中使用模块

Azure 自动化支持多个默认模块，包括 AzureRM 模块（AzureRM）和包含多个内部 cmdlet 的模块。 还支持使用可安装的模块，包括 Az 模块（Az），当前正用于 AzureRM 模块。 有关适用于 runbook 和 DSC 配置的模块的详细信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

## <a name="creating-resources"></a>创建资源

如果 runbook 创建资源，则在尝试创建资源之前，该脚本应检查是否已存在该资源。 下面是一个基本示例。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="supporting-time-dependent-scripts"></a>支持时间相关的脚本

Runbook 必须可靠且能够处理暂时性错误，从而导致它们重新启动或失败。 如果 runbook 失败，Azure 自动化会重试该操作。

如果你的 runbook 通常在时间约束内运行，则让脚本实现逻辑来检查执行时间。 此检查可确保仅在特定时间运行诸如启动、关闭或扩展的操作。

> [!NOTE]
> Azure 沙盒进程的本地时间设置为 UTC。 Runbook 中的日期和时间计算必须考虑到这一点。

## <a name="tracking-progress"></a>跟踪进程

最佳做法是，将 runbook 编写为模块化，并使用可轻松重复使用和重新启动的逻辑。 如果存在问题，可以通过在 runbook 中跟踪进度来确保正确执行 runbook 逻辑。 可以使用外部源（例如存储帐户、数据库或共享文件）跟踪 runbook 的进度。 你可以在 runbook 中创建逻辑，以首先检查执行的最后一个操作的状态。 然后，根据检查结果，逻辑可以跳过或继续 runbook 中的特定任务。

## <a name="preventing-concurrent-jobs"></a>阻止并发作业

如果某些 runbook 同时跨多个作业运行，则它的行为奇怪。 在这种情况下，runbook 必须实现逻辑来确定是否已有一个正在运行的作业。 下面是一个基本示例。

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="working-with-multiple-subscriptions"></a>使用多个订阅

若要处理多个订阅，runbook 必须使用[AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet。 此 cmdlet 可确保不从同一沙盒中运行的另一个 runbook 检索身份验证上下文。 Runbook 还在 Az module`AzContext` cmdlet 上使用参数并向其传递适当的上下文。

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="handling-exceptions"></a>处理异常

本部分介绍在 runbook 中处理异常或间歇问题的一些方法。 例如，WebSocket 异常。 更正异常处理可防止暂时性的网络故障导致 runbook 失败。 

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)变量确定 PowerShell 如何响应非终止错误。 终止错误始终终止，不受影响`ErrorActionPreference`。

当 runbook 使用`ErrorActionPreference`时，通常非终止错误（如`PathNotFound` [get-childitem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) cmdlet）将停止 runbook 的完成。 以下示例显示了 `ErrorActionPreference` 的用法。 当脚本停止时，最终的[写入输出](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7)命令永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[尝试 Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)用于处理终止错误。 该脚本可以使用此机制来捕获特定的异常或一般异常。 `catch`语句应用于跟踪或尝试处理错误。 以下示例尝试下载一个不存在的文件。 它将捕获`System.Net.WebException`异常并返回任何其他异常的最后一个值。

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

### <a name="throw"></a>Throw

可以使用 [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 来生成终止性错误。 在 runbook 中定义自己的逻辑时，此机制可能会很有用。 如果脚本满足应停止的条件，则可以使用`throw`语句停止。 下面的示例使用此语句来显示所需的函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="using-executables-or-calling-processes"></a>使用可执行文件或调用进程

在 Azure 沙盒中运行的 runbook 不支持调用进程（如可执行文件（**.exe**文件）或子进程）。 出现这种情况的原因是，Azure 沙箱是一个共享进程，该进程在可能无法访问所有底层 Api 的容器中运行。 对于需要第三方软件或对子进程的调用的方案，应在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上执行 runbook。

## <a name="accessing-device-and-application-characteristics"></a>访问设备和应用程序的特征

在 Azure 沙盒中运行的 Runbook 作业无法访问任何设备或应用程序特征。 用于在 Windows 上查询性能指标的最常见 API 是 WMI，其中一些常用指标为内存和 CPU 使用率。 但是，因为在云中运行的作业不能访问基于 Web 的企业管理（WBEM）的 Microsoft 实现，所以这并不重要。 此平台基于通用信息模型（CIM）构建，为定义设备和应用程序特征提供行业标准。

## <a name="handling-errors"></a>处理错误

Runbook 必须能够处理错误。 PowerShell 有两种类型的错误，即终止和非终止。 终止错误会在 runbook 执行发生时停止。 Runbook 停止并且作业状态为 "失败"。

非终止错误允许脚本即使在发生之后也能继续。 非终止错误的一个示例是 runbook 将`Get-ChildItem` cmdlet 与不存在的路径一起使用时出现的错误。 PowerShell 发现路径不存在，然后引发错误，并继续转到下一文件夹。 此情况下的错误不会将 runbook 作业状态设置为 "失败"，甚至可能会完成该作业。 若要强制 runbook 在发生非终止性错误时停止，可以使用 `ErrorAction Stop` cmdlet。

## <a name="handling-jobs"></a>处理作业

您可以重复使用同一自动化帐户中的作业的执行环境。 一个 runbook 可以同时运行多个作业。 同时运行的作业越多，就越可能将其分派到同一个沙盒中。

在同一沙盒过程中运行的作业可能会相互影响。 一个示例是运行[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) cmdlet。 此 cmdlet 的执行会断开共享沙盒进程中的每个 runbook 作业。

从在 Azure 沙盒中运行的 runbook 启动的 PowerShell 作业可能无法在完整[PowerShell 语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。 若要详细了解如何与 Azure Automation 中的作业进行交互，请参阅[通过 PowerShell 检索作业状态](#retrieving-job-status-using-powershell)。

### <a name="job-statuses"></a>作业状态

下表描述了作业可能的状态。

| 状态 | 说明 |
|:--- |:--- |
| 已完成 |作业已成功完成。 |
| Failed |未能编译图形或 PowerShell 工作流 runbook。 PowerShell 脚本 runbook 无法启动或作业有异常。 请参阅[Azure 自动化 runbook 类型](automation-runbook-types.md)。|
| 失败，正在等待资源 |作业失败，因为它已达到[公平份额](#fair-share)限制三次，并且每次都从同一个检查点或 Runbook 开始处启动。 |
| 已排队 |作业正在等待自动化工作线程上的资源变为可用，以便能够启动。 |
| 正在启动 |作业已分配给辅助角色，并且系统正在将它启动。 |
| 正在恢复 |系统正在恢复已暂停的作业。 |
| 运行 |作业正在运行。 |
| 正在运行，正在等待资源 |作业已卸载，因为它已达到公平份额限制。 片刻之后，它将从其上一个检查点恢复。 |
| 已停止 |作业在完成之前已被用户停止。 |
| 正在停止 |系统正在停止作业。 |
| Suspended |仅适用于[图形和 PowerShell 工作流 runbook](automation-runbook-types.md) 。 作业已被用户、系统或 Runbook 中的命令暂停。 如果 runbook 没有检查点，则会从开始处开始。 如果它有检查点，它将重新启动并从其上一个检查点继续。 系统仅在发生异常时挂起 runbook。 默认情况下， `ErrorActionPreference`该变量设置为 Continue，表示该作业在出现错误时继续运行。 如果首选项变量设置为 "停止"，则作业将在出现错误时挂起。  |
| 正在暂停 |仅适用于[图形和 PowerShell 工作流 runbook](automation-runbook-types.md) 。 系统正在尝试按用户请求暂停作业。 Runbook 只有在达到其下一个检查点后才能挂起。 如果它已通过其最后一个检查点，则它将完成，然后才能挂起。 |

### <a name="viewing-job-status-from-the-azure-portal"></a>从 Azure 门户查看作业状态

你可以查看所有 runbook 作业的状态摘要，或在 Azure 门户中深入了解特定 runbook 作业的详细信息。 此外，还可配置与 Log Analytics 工作区的集成，以转发 runbook 作业状态和作业流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

在所选自动化帐户的右侧，可以在 "**作业统计信息**" 磁贴下查看所有 runbook 作业的摘要。

![作业统计信息磁贴](./media/automation-runbook-execution/automation-account-job-status-summary.png)

此磁贴显示已执行的每个作业的作业状态的计数和图形表示。

单击磁贴可显示“作业”页，此页包括所有已执行作业的摘要列表。 此页显示每个作业的状态、runbook 名称、开始时间和完成时间。

![自动化帐户作业页](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

可以通过选择 "**筛选作业**" 来筛选作业列表。 根据特定 runbook、作业状态或下拉列表中的选择进行筛选，并提供搜索的时间范围。

![筛选作业状态](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，你可以通过在自动化帐户的 "Runbook" 页中选择相应的 runbook，然后选择 "**作业**" 磁贴，来查看特定 runbook 的作业摘要详细信息。 此操作显示 "作业" 页。 在此处，你可以单击作业记录以查看其详细信息和输出。

![自动化帐户作业页](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>查看作业摘要

上面所述的作业摘要使你可以查看为特定 runbook 创建的所有作业的列表及其最新状态。 若要查看作业的详细信息和输出，请在列表中单击其名称。 作业的详细视图包括提供给该作业的 runbook 参数的值。

可以使用以下步骤查看 Runbook 的作业。

1. 在 Azure 门户中，选择“自动化”****，然后选择自动化帐户的名称。
2. 从中心选择 "**流程自动化**" 下的 " **runbook** "。
3. 在 "Runbook" 页上，从列表中选择一个 runbook。
3. 在所选 runbook 的页上，单击“作业”磁贴****。
4. 单击列表中的一个作业，然后在 "runbook 作业详细信息" 页上查看其详细信息和输出。

### <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 检索作业状态

使用[AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) cmdlet 可检索为 runbook 创建的作业以及特定作业的详细信息。 如果使用`Start-AzAutomationRunbook`PowerShell 启动 runbook，它将返回生成的作业。 使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)检索作业输出。

下面的示例获取示例 runbook 的最后一个作业并显示其状态、为 runbook 参数提供的值以及作业的输出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下面的示例检索特定作业的输出，并返回每条记录。 如果其中一个记录存在异常，则脚本将写入异常，而不是写入值。 此行为很有用，因为异常可以提供在输出过程中可能无法正常记录的其他信息。

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="getting-details-from-the-activity-log"></a>从活动日志中获取详细信息

可以从自动化帐户的活动日志中检索 runbook 详细信息，如启动 runbook 的人员或帐户。 下面的 PowerShell 示例提供最后一个用户来运行指定的 runbook。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>在 runbook 之间共享资源

为了在云中的所有 Runbook 之间共享资源，Azure 自动化会暂时卸载或停止已运行三小时以上的所有作业。 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)和[Python runbook](automation-runbook-types.md#python-runbooks)的作业将停止且不会重新启动，作业状态将变为 "已停止"。

对于长时间运行的任务，建议使用混合 Runbook 辅助角色。 混合 Runbook 辅助角色不受公平份额限制，并且不会限制 runbook 的执行时间。 其他作业[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)适用于 Azure 沙盒和混合 Runbook 辅助角色。 虽然混合 Runbook 辅助角色不受3小时公平份额限制的限制，但你应开发 runbook，使其在支持从意外的本地基础结构问题中重新启动的辅助角色上运行。

另一种方法是使用子 runbook 优化 runbook。 例如，你的 runbook 可能会循环遍历几个资源上的相同函数，例如多个数据库上的数据库操作。 可以将此函数移动到[子 runbook](automation-child-runbooks.md) ，并让 Runbook 使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)调用它。 子 runbook 并行在单独的进程中执行。

使用子 runbook 会缩短父 runbook 完成的总时间。 Runbook 可以使用[AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) cmdlet 来检查子 runbook 的作业状态（如果子 runbook 在子项完成后仍有更多操作）。

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* 若要详细了解可用于在 Azure 自动化中启动 runbook 的方法，请参阅[在 Azure 自动化中启动 runbook](automation-starting-a-runbook.md)。
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
