---
title: 在 Azure 自动化中执行 Runbook
description: 详细介绍如何处理 Azure 自动化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: a7dd9de1f2ae41b20d94cf31de48e92fbb71ca6a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405643"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

Azure 自动化中的流程自动化允许您创建和管理 PowerShell、PowerShell 工作流和图形运行簿。 有关详细信息，请参阅[Azure 自动化运行簿](automation-runbook-types.md)。 

自动化根据其中定义的逻辑执行 Runbook。 如果 Runbook 中断，它将在开头重新启动。 此行为要求您编写支持在发生暂时性问题时重新启动的 Runbook。

在 Azure 自动化中启动 Runbook 将创建一个作业，该作业是 Runbook 的单个执行实例。 每个作业都通过连接到 Azure 订阅来访问 Azure 资源。 仅当可从公共云访问这些资源时，作业才能访问数据中心中的资源。

Azure 自动化分配一个辅助角色在 Runbook 执行期间运行每个作业。 尽管辅助角色由多个 Azure 帐户共享，但不同自动化帐户中的作业是相互独立的。 您无法控制为作业请求提供服务的工作。

在 Azure 门户中查看 Runbook 列表时，它会显示每个 Runbook 已启动的每个作业的状态。 Azure 自动化最多存储作业日志 30 天。

下图显示了[PowerShell Runbook、PowerShell](automation-runbook-types.md#powershell-runbooks)[工作流运行簿](automation-runbook-types.md#powershell-workflow-runbooks)和[图形运行簿](automation-runbook-types.md#graphical-runbooks)的运行日志作业的生命周期。

![作业状态 - PowerShell 工作流](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="where-to-run-your-runbooks"></a>运行 runbook 的位置

Azure 自动化中的 Runbook 可以在 Azure 沙盒或[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行。 当 Runbook 设计用于对 Azure 中的资源进行身份验证和运行时，它们在 Azure 沙盒中运行，这是多个作业可以使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。

>[!NOTE]
>Azure 沙盒环境不支持交互式操作。 它还要求对进行 Win32 调用的 Runbook 使用本地 MOF 文件。

可以使用混合 Runbook 辅助角色直接在承载角色的计算机上运行 Runbook，并针对环境中的本地资源运行 Runbook。 Azure 自动化存储和管理 Runbook，然后将它们传递到一个或多个分配的计算机。

下表列出了一些 Runbook 执行任务，其中列出了每个任务所列出的建议执行环境。

|任务|建议|说明|
|---|---|---|
|与 Azure 资源集成|Azure 沙盒|托管在 Azure 中，身份验证更简单。 如果在 Azure VM 上使用混合 Runbook 工作线程，则可以[对 Azure 资源使用托管标识](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。|
|获得最佳性能来管理 Azure 资源|Azure 沙盒|脚本在同一环境中运行，延迟较少。|
|最大程度减少运营成本|Azure 沙盒|没有计算开销，也不需要 VM。|
|执行长时间运行的脚本|混合 Runbook 辅助角色|Azure 沙盒具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|与本地服务交互|混合 Runbook 辅助角色|可以直接访问主机或其他云环境中或本地环境中的资源。 |
|需要第三方软件和可执行文件|混合 Runbook 辅助角色|您可以管理操作系统，并可以安装软件。|
|使用 Runbook 监视文件或文件夹|混合 Runbook 辅助角色|在混合 Runbook 工作线程上使用[观察程序任务](automation-watchers-tutorial.md)。|
|运行资源密集型脚本|混合 Runbook 辅助角色| Azure 沙盒具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定要求的模块| 混合 Runbook 辅助角色|一些示例如下：</br> WinSCP - winscp.exe 上的依赖项 </br> IIS 管理 - 依赖于启用或管理 IIS。|
|安装带有安装程序的模块|混合 Runbook 辅助角色|沙盒模块必须支持复制。|
|使用需要与 4.7.2 不同的 .NET 框架版本的 Runbook 或模块|混合 Runbook 辅助角色|自动化沙盒支持 .NET 框架 4.7.2，不支持升级到其他版本。|
|运行需要提升的脚本|混合 Runbook 辅助角色|沙盒不允许高程。 使用混合 Runbook 辅助角色，您可以在运行需要提升的命令时关闭 UAC 并使用[调用命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。|
|运行需要访问 Windows 管理检测 （WMI） 的脚本|混合 Runbook 辅助角色|在云中的沙盒中运行的作业无法访问 WMI 提供程序。 |

## <a name="runbook-behavior"></a>Runbook 行为

### <a name="creating-resources"></a>创建资源

如果 Runbook 创建了资源，则脚本在尝试创建资源之前应检查该资源是否已存在。 下面是一个基本示例。

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

### <a name="supporting-time-dependent-scripts"></a>支持与时间相关的脚本

Runbook 必须健壮，能够处理可能导致它们重新启动或失败的瞬态错误。 如果 Runbook 失败，Azure 自动化将重试它。

如果 Runbook 通常在时间限制内运行，请让脚本实现逻辑来检查执行时间。 此检查可确保仅在特定时间运行启动、关闭或横向扩展等操作。

> [!NOTE]
> Azure 沙盒进程中的本地时间设置为 UTC。 计算 Runbook 中的日期和时间时必须考虑到这一事实。

### <a name="tracking-progress"></a>跟踪进程

最好将 Runbook 创作为模块化，逻辑可以轻松重用和重新启动。 跟踪 Runbook 中的进度是确保 Runbook 逻辑在出现问题时正确执行的好方法。 可以使用外部源（如存储帐户、数据库或共享文件）跟踪 Runbook 的进度。 您可以在 Runbook 中创建逻辑，以便首先检查上次执行的操作的状态。 然后，根据检查结果，逻辑可以跳过或继续 Runbook 中的特定任务。

### <a name="preventing-concurrent-jobs"></a>防止并发作业

如果某些 Runbook 同时跨多个作业运行，则它们运行得很奇怪。 在这种情况下，Runbook 实现逻辑以确定是否存在正在运行的作业非常重要。 下面是一个基本示例。

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

### <a name="working-with-multiple-subscriptions"></a>使用多个订阅

要处理多个订阅，运行簿必须使用[禁用-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet。 此 cmdlet 可确保不会从在同一沙盒中运行的另一个 Runbook 检索身份验证上下文。 Runbook 还使用`AzContext`Az 模块 cmdlet 上的参数，并将其传递适当的上下文。

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

### <a name="handling-exceptions"></a>处理异常

本节介绍处理 Runbook 中的异常或间歇性问题的一些方法。

#### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorAction 首选项](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)变量确定 PowerShell 如何响应非终止错误。 终止错误始终终止，不受`ErrorActionPreference`的影响。

当 Runbook`ErrorActionPreference`使用 时，通常非终止错误（如`PathNotFound`来自[获取子项目](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7)cmdlet）会阻止 Runbook 完成。 以下示例显示了 `ErrorActionPreference` 的用法。 当脚本停止时，最终[写入输出](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7)命令永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[最后尝试捕获](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)在 PowerShell 脚本中使用来处理终止错误。 脚本可以使用此机制捕获特定的异常或常规异常。 该`catch`语句应用于跟踪或尝试处理错误。 以下示例尝试下载一个不存在的文件。 它捕获异常`System.Net.WebException`并返回任何其他异常的最后一个值。

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

可以使用 [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 来生成终止性错误。 在 Runbook 中定义自己的逻辑时，此机制非常有用。 如果脚本满足应停止它的条件，它可以使用 语句`throw`停止。 下面的示例使用此语句来显示所需的函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可执行文件或调用进程

在 Azure 沙盒中运行的 Runbook 不支持调用进程，例如可执行文件 **（.exe**文件）或子进程。 原因是 Azure 沙盒是在可能无法访问所有基础 API 的容器中运行的共享进程。 对于需要第三方软件或调用子进程的方案，应在[混合 Runbook 工作线程上执行 Runbook。](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>访问设备和应用程序特征

在 Azure 沙盒中运行的 Runbook 作业无法访问任何设备或应用程序特征。 用于在 Windows 上查询性能指标的最常见 API 是 WMI，其中一些常见指标是内存和 CPU 使用率。 但是，使用什么 API 并不重要，因为在云中运行的作业无法访问基于 Web 的企业管理 （WBEM） 的 Microsoft 实现。 该平台基于通用信息模型 （CIM）构建，为定义设备和应用程序特征提供了行业标准。

## <a name="handling-errors"></a>处理错误

您的 Runbook 必须能够处理错误。 PowerShell 有两种类型的错误，即终止错误和非终止错误。 终止错误发生时将停止运行簿执行。 运行簿停止作业状态为"失败"。

非终止错误允许脚本在发生脚本后继续。 非终止错误的一个示例是 Runbook 使用具有不存在的路径的`Get-ChildItem`cmdlet 时发生的错误。 PowerShell 发现路径不存在，然后引发错误，并继续转到下一文件夹。 在这种情况下，错误不会将 Runbook 作业状态设置为"失败"，并且该作业甚至可能完成。 若要强制 runbook 在发生非终止性错误时停止，可以使用 `ErrorAction Stop` cmdlet。

## <a name="handling-jobs"></a>处理作业

您可以为同一自动化帐户中的作业重用执行环境。 一个 runbook 可以同时运行多个作业。 同时运行的作业越多，就越可能将其分派到同一个沙盒中。

在同一沙盒进程中运行的作业可能会相互影响。 一个例子是运行[断开连接-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) cmdlet。 执行此 cmdlet 会断开共享沙盒进程中的每个 Runbook 作业。

PowerShell 作业从在 Azure 沙盒中运行的运行手册开始，可能无法在完整的[PowerShell 语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。 有关在 Azure 自动化中与作业交互的详细信息，请参阅[使用 PowerShell 检索作业状态](#retrieving-job-status-using-powershell)。

### <a name="job-statuses"></a>作业状态

下表描述了作业可能的状态。

| 状态 | 说明 |
|:--- |:--- |
| 已完成 |作业已成功完成。 |
| 失败 |图形或 PowerShell 工作流运行簿编译失败。 PowerShell 脚本运行簿无法启动或作业有异常。 请参阅[Azure 自动化运行簿类型](automation-runbook-types.md)。|
| 失败，正在等待资源 |作业失败，因为它已达到[公平份额](#fair-share)限制三次，并且每次都从同一个检查点或 Runbook 开始处启动。 |
| 已排队 |作业正在等待自动化辅助角色上的资源可用，以便可以启动它。 |
| 正在启动 |作业已分配给辅助角色，并且系统正在将它启动。 |
| 正在恢复 |系统正在恢复已暂停的作业。 |
| 运行 |作业正在运行。 |
| 正在运行，正在等待资源 |作业已卸载，因为它已达到公平份额限制。 片刻之后，它将从其上一个检查点恢复。 |
| 已停止 |作业在完成之前已被用户停止。 |
| 正在停止 |系统正在停止作业。 |
| Suspended |仅适用于[图形和 PowerShell 工作流运行簿](automation-runbook-types.md)。 作业已被用户、系统或 Runbook 中的命令暂停。 如果 Runbook 没有检查点，则从一开始就启动。 如果它有检查点，它将重新启动并从其上一个检查点继续。 仅当发生异常时，系统才会挂起 Runbook。 默认情况下，`ErrorActionPreference`变量设置为"继续"，表示作业在错误时继续运行。 如果首选项变量设置为"停止"，则作业将因错误而挂起。  |
| 正在暂停 |仅适用于[图形和 PowerShell 工作流运行簿](automation-runbook-types.md)。 系统正在尝试按用户请求暂停作业。 Runbook 只有在达到其下一个检查点后才能挂起。 如果已通过其最后一个检查点，则在挂起之前完成。 |

### <a name="viewing-job-status-from-the-azure-portal"></a>从 Azure 门户查看作业状态

您可以查看所有 Runbook 作业的状态摘要，也可以深入了解 Azure 门户中特定 Runbook 作业的详细信息。 此外，还可配置与 Log Analytics 工作区的集成，以转发 runbook 作业状态和作业流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

在所选的自动化帐户的右侧，您可以在 **"作业统计信息"** 磁贴下看到所有 Runbook 作业的摘要。

![作业统计信息磁贴](./media/automation-runbook-execution/automation-account-job-status-summary.png)

此磁贴显示所执行每个作业的作业状态的计数和图形表示形式。

单击磁贴可显示“作业”页，此页包括所有已执行作业的摘要列表。 此页显示每个作业的状态、运行簿名称、开始时间和完成时间。

![自动化帐户作业页](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

您可以通过选择 **"筛选作业"来**筛选作业列表。 筛选特定 Runbook、作业状态或下拉列表中的选择，并提供搜索的时间范围。

![筛选作业状态](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以通过从自动化帐户中的 Runbook 页面中选择该 Runbook，然后选择"作业"磁贴来查看特定 Runbook**的作业**摘要详细信息。 此操作显示"作业"页。 在此处，您可以单击作业记录以查看其详细信息和输出。

![自动化帐户作业页](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>查看作业摘要

通过上述作业摘要，您可以查看为特定 Runbook 创建的所有作业及其最新状态的列表。 要查看作业的详细信息和输出，请单击列表中的名称。 作业的详细视图包括已提供给该作业的 Runbook 参数的值。

可以使用以下步骤查看 Runbook 的作业。

1. 在 Azure 门户中，选择“自动化”****，然后选择自动化帐户的名称。
2. 从集线器中，在 **"过程自动化**"下选择**Runbook。**
3. 在 Runbook 页上，从列表中选择 Runbook。
3. 在所选 runbook 的页上，单击“作业”磁贴****。
4. 单击列表中的作业之一，并在 Runbook 作业详细信息页上查看其详细信息和输出。

### <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 检索作业状态

使用[Get-AzAutomationJobJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) cmdlet 检索为 Runbook 创建的作业以及特定作业的详细信息。 如果使用 使用 启动使用 PowerShell`Start-AzAutomationRunbook`的运行簿，它将返回生成的作业。 使用[获取自动化作业输出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)检索作业输出。

下面的示例获取示例 Runbook 的最后一个作业并显示其状态、为 Runbook 参数提供的值和作业输出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下面的示例检索特定作业的输出并返回每个记录。 如果其中一个记录有异常，脚本将写入异常而不是值。 此行为很有用，因为异常可以提供在输出期间可能无法正常记录的其他信息。

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

## <a name="getting-details-from-the-activity-log"></a>从活动日志获取详细信息

可以从自动化帐户的活动日志中检索 Runbook 详细信息，例如启动 Runbook 的人员或帐户。 以下 PowerShell 示例提供运行指定 Runbook 的最后一个用户。

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>在 Runbook 之间共享资源

为了在云中的所有 Runbook 之间共享资源，Azure 自动化会暂时卸载或停止已运行三小时以上的所有作业。 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)和[Python Runbook](automation-runbook-types.md#python-runbooks)的作业将停止，并且不会重新启动，并且作业状态将变为"已停止"。

对于长时间运行的任务，建议使用混合 Runbook 辅助角色。 混合 Runbook 辅助角色不受公平份额限制，并且不会限制 runbook 的执行时间。 其他作业[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)适用于 Azure 沙盒和混合 Runbook 辅助角色。 虽然混合 Runbook 工作簿不受 3 小时公平份额限制的限制，但您应该开发 Runbook 以运行支持从意外本地基础结构问题重新启动的工作人员。

另一个选项是使用子 Runbook 优化 Runbook。 例如，Runbook 可能会循环访问多个资源上的同一函数，例如多个数据库上的数据库操作。 您可以将此函数移动到[子 Runbook，](automation-child-runbooks.md)并让您的 Runbook 使用["开始-Az自动化 Runbook"调用它](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)。 子 Runbook 在单独的进程中并行执行。

使用子 Runbook 可减少父运行簿完成的总时间量。 如果子运行簿在子运行簿完成后仍具有更多操作，则 Runbook 可以使用[Get-AzAutomationJobJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) cmdlet 检查子 Runbook 的作业状态。

## <a name="next-steps"></a>后续步骤

* 要了解如何使用 Runbook，请参阅在 Azure[自动化 中管理 Runbook。](manage-runbooks.md)
* 要了解有关可用于在 Azure 自动化中启动 Runbook 的方法，请参阅[在 Azure 自动化 中启动 Runbook。](automation-starting-a-runbook.md)
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
