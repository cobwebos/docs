---
title: 在 Azure 自动化中管理 Runbook
description: 本文介绍如何在 Azure 自动化中管理 Runbook。 它介绍基本操作并添加一些最佳实践。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: cd89314b0d847909bf4196361e471b71ebb9b6e9
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995489"
---
# <a name="manage-runbooks-in-azure-automation"></a>在 Azure 自动化中管理 Runbook

可以通过创建新的 runbook，或从文件或[Runbook 库](automation-runbook-gallery.md)导入现有 runbook，将 runbook 添加到 Azure 自动化。 本文提供了有关管理从文件导入的 runbook 的信息。 可以在[Azure 自动化的 Runbook 和模块库](automation-runbook-gallery.md)中找到访问社区 runbook 和模块的所有详细信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="create-a-runbook"></a>创建 runbook

使用 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建新的 runbook。 创建 runbook 后，你可以使用中的信息对其进行编辑：

* [在 Azure 自动化中编辑文本 runbook](automation-edit-textual-runbook.md) 
* [了解自动化 runbook 的关键 Windows PowerShell 工作流概念](automation-powershell-workflow.md)
* [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* [在 Azure 自动化中管理 Python 2 包](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 门户中创建 Runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心选择 "**进程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
3. 单击 "**创建 runbook**"。
4. 输入 runbook 的名称，并选择其[类型](automation-runbook-types.md)。 Runbook 名称必须以字母开头，并且可以包含字母、数字、下划线和短划线。
5. 单击“创建”**** 以创建 Runbook 并打开编辑器。

### <a name="create-a-runbook-with-powershell"></a>通过 PowerShell 创建 Runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet 创建空 runbook。 使用`Type`参数指定为`New-AzAutomationRunbook`定义的 runbook 类型之一。

下面的示例演示如何创建新的空 runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>导入 Runbook

可以导入 PowerShell 或 PowerShell 工作流（**ps1**）脚本、图形 runbook （**. .Graphrunbook**）或 Python 2 脚本（**py**）来生成自己的 runbook。  必须指定在导入期间创建的 [Runbook 类型](automation-runbook-types.md)，并考虑以下注意事项。

* 可以将不包含工作流的**ps1**文件导入到[Powershell Runbook](automation-runbook-types.md#powershell-runbooks)或[powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果将其导入到 PowerShell 工作流 runbook 中，则会将其转换为工作流。 在这种情况下，runbook 中将包含注释来描述所做的更改。

* 只能将包含 PowerShell 工作流的**ps1**文件导入到[PowerShell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果文件包含多个 PowerShell 工作流，导入将失败。 必须将每个工作流保存到各自的文件中，并分别导入每个工作流。

* 不要将包含 PowerShell 工作流的**ps1**文件导入到[powershell Runbook](automation-runbook-types.md#powershell-runbooks)，因为 powershell 脚本引擎无法识别它。

* 仅将 **.graphrunbook**文件导入到新的[图形 runbook](automation-runbook-types.md#graphical-runbooks)中。 

### <a name="import-a-runbook-from-the-azure-portal"></a>从 Azure 门户导入 runbook

可通过以下过程将脚本文件导入 Azure 自动化。

> [!NOTE]
> 只能使用门户将**ps1**文件导入到 PowerShell 工作流 runbook。

1. 在 Azure 门户中，打开自动化帐户。
2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。  
3. 单击 "**导入 runbook**"。
4. 单击 " **Runbook 文件**"，然后选择要导入的文件。
5. 如果 "**名称**" 字段已启用，则可以选择更改 runbook 名称。 名称必须以字母开头，并且可以包含字母、数字、下划线和短划线。
6. 将自动选择 [Runbook 类型](automation-runbook-types.md)，但可以在考虑适用的限制后更改该类型。
7. 单击“创建”。  新的 runbook 会出现在自动化帐户的 runbook 列表中。
8. 必须先[发布 Runbook](#publish-a-runbook)，才能运行它。

> [!NOTE]
> 导入图形 runbook 后，可以将其转换为其他类型。 但是，不能将图形 runbook 转换为文本 runbook。

### <a name="import-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 导入 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet 将脚本文件导入为草稿 runbook。 如果 runbook 已存在，除非将`Force`参数与 cmdlet 一起使用，否则导入将失败。

下面的示例演示如何将脚本文件导入 runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>处理资源

如果 runbook 创建[资源](automation-runbook-execution.md#resources)，则在尝试创建资源之前，该脚本应检查是否已存在该资源。 下面是一个基本示例。

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

## <a name="retrieve-details-from-activity-log"></a>从活动日志中检索详细信息

可以从自动化帐户的[活动日志](automation-runbook-execution.md#activity-logging)中检索 runbook 详细信息，如启动 runbook 的人员或帐户。 下面的 PowerShell 示例提供最后一个用户来运行指定的 runbook。

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

## <a name="track-progress"></a>跟踪进度

最佳做法是，将 runbook 编写为模块化，并使用可轻松重复使用和重新启动的逻辑。 在 runbook 中跟踪进度可确保 runbook 逻辑在出现问题时正确执行。 

你可以使用外部源（例如存储帐户、数据库或共享文件）跟踪 runbook 的进度。 在 runbook 中创建逻辑，以首先检查执行的最后一个操作的状态。 然后，根据检查结果，逻辑可以跳过或继续 runbook 中的特定任务。

## <a name="prevent-concurrent-jobs"></a>预防并发作业

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>处理依赖于时间的脚本中的暂时性错误

Runbook 必须可靠且能够处理[错误](automation-runbook-execution.md#errors)，包括暂时性错误，这些错误会导致这些错误重新启动或失败。 如果 runbook 失败，Azure 自动化会重试该操作。

如果你的 runbook 通常在时间约束内运行，则让脚本实现逻辑来检查执行时间。 此检查可确保仅在特定时间运行诸如启动、关闭或扩展的操作。

> [!NOTE]
> Azure 沙盒进程的本地时间设置为 UTC。 Runbook 中的日期和时间计算必须考虑到这一点。

## <a name="work-with-multiple-subscriptions"></a>使用多个订阅

Runbook 必须能够处理[订阅](automation-runbook-execution.md#subscriptions)。 例如，若要处理多个订阅，runbook 将使用[AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet。 此 cmdlet 可确保不从同一沙盒中运行的另一个 runbook 检索身份验证上下文。 Runbook 还在 Az module`AzContext` cmdlet 上使用参数并向其传递适当的上下文。

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

## <a name="work-with-a-custom-script"></a>使用自定义脚本

> [!NOTE]
> 通常无法在安装了 Log Analytics 代理的主机上运行自定义脚本和 runbook。 

使用自定义脚本：

1. 创建自动化帐户并获取[参与者角色](automation-role-based-access-control.md)。
2. 将[帐户链接到 Azure 工作区](../security-center/security-center-enable-data-collection.md)。
3. 启用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)、[更新管理](automation-update-management.md)或其他自动化功能。 
4. 如果在 Linux 计算机上，则需要较高的权限。 登录以[关闭签名检查](automation-linux-hrw-install.md#turn-off-signature-validation)。

## <a name="test-a-runbook"></a>测试 Runbook

测试 Runbook 时，将执行[草稿版](#publish-a-runbook)，并会完成其所执行的任何操作。 不会创建任何作业历史记录，但会在 "测试输出" 窗格中显示[输出](automation-runbook-output-and-messages.md#output-stream)、[警告和错误](automation-runbook-output-and-messages.md#message-streams)流。 仅当[VerbosePreference](automation-runbook-output-and-messages.md#preference-variables)变量设置为`Continue`时，才会在 "输出" 窗格中显示[详细流](automation-runbook-output-and-messages.md#message-streams)的消息。

即使草稿版正在运行，该 Runbook 也仍会正常执行，并针对环境中的资源执行任何操作。 因此，只能在非生产资源中测试 Runbook。

测试每种[runbook 类型](automation-runbook-types.md)的过程是相同的。 Azure 门户中的文本编辑器和图形编辑器之间的测试没有区别。

1. 在[文本编辑器](automation-edit-textual-runbook.md)或[图形编辑器](automation-graphical-authoring-intro.md)中打开 runbook 的草稿版本。
1. 单击 "**测试**" 以打开 "测试" 页。
1. 如果 runbook 具有参数，则这些参数将列在左侧窗格中，你可以在其中提供用于测试的值。
1. 如果要在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行测试，则将 "**运行设置**" 更改为 "**混合辅助角色**" 并选择目标组的名称。  否则，保留默认值 **Azure**，以在云中运行测试。
1. 单击“启动”，开始测试。 
1. 你可以使用 "输出" 窗格下的按钮来停止或挂起正在测试的[PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks)或[图形](automation-runbook-types.md#graphical-runbooks)runbook。 暂停 Runbook 时，该 Runbook 会完成它在被暂停之前正在进行的活动。 暂停 Runbook 后，可以将它停止或重启。
1. 在输出窗格中检查 runbook 的输出。

## <a name="publish-a-runbook"></a>发布 Runbook

创建或导入新的 Runbook 时，必须先将其发布，然后才能导入。 Azure Automation 中的每个 runbook 都有草稿版本和已发布的版本。 只有已发布版才能用来运行，只有草稿版才能用来编辑。 已发布版不受对草稿版所做的任何更改的影响。 如果应使草稿版本可用，请发布该版本，并使用草稿版本覆盖当前发布的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 门户中发布 runbook

1. 在 Azure 门户中打开 Runbook。
2. 单击 **“编辑”**。
3. 单击 "**发布**"，然后单击 **"是"** 以响应验证消息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 发布 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet 发布 runbook。 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>在 Azure 门户中计划 runbook

发布 runbook 后，你可以计划其操作：

1. 在 Azure 门户中打开 Runbook。
2. 在 "**资源**" 下选择**计划**。
3. 选择 "**添加计划**"。
4. 在 "计划 Runbook" 窗格中，选择 "将**计划链接到 Runbook**"。
5. 在 "计划" 窗格中选择 "**创建新计划**"。
6. 在 "新建计划" 窗格中输入 "名称"、"说明" 和其他参数。 
7. 创建计划后，突出显示该计划，然后单击 **"确定"**。 它现在应链接到你的 runbook。
8. 查找邮箱中的电子邮件以通知你 runbook 状态。

## <a name="obtain-job-statuses"></a>获取作业状态

### <a name="view-statuses-in-the-azure-portal"></a>查看 Azure 门户中的状态

[作业](automation-runbook-execution.md#jobs)中提供了 Azure 自动化中作业处理的详细信息。 如果已准备好查看 runbook 作业，请使用 Azure 门户和访问自动化帐户。 在右侧，可以在 "**作业统计信息**" 中查看所有 runbook 作业的摘要。 

![作业统计信息磁贴](./media/manage-runbooks/automation-account-job-status-summary.png)

摘要显示每个已执行作业的作业状态的计数和图形表示。

单击磁贴可显示“作业”页，此页包括所有已执行作业的摘要列表。 此页显示每个作业的状态、runbook 名称、开始时间和完成时间。

![自动化帐户作业页](./media/manage-runbooks/automation-account-jobs-status-blade.png)

可以通过选择 "**筛选作业**" 来筛选作业列表。 根据特定 runbook、作业状态或下拉列表中的选择进行筛选，并提供搜索的时间范围。

![筛选作业状态](./media/manage-runbooks/automation-account-jobs-filter.png)

或者，你可以通过在自动化帐户的 "Runbook" 页中选择该 runbook，然后选择 "**作业**" 来查看特定 runbook 的作业摘要详细信息。 此操作显示 "作业" 页。 在此处，你可以单击作业记录以查看其详细信息和输出。

![自动化帐户作业页](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>使用 PowerShell 检索作业状态

使用[AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) cmdlet 可检索为 runbook 创建的作业以及特定作业的详细信息。 如果使用`Start-AzAutomationRunbook`启动 runbook，它将返回生成的作业。 使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)检索作业输出。

下面的示例获取示例 runbook 的最后一个作业并显示其状态、为 runbook 参数提供的值以及作业的输出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下面的示例检索特定作业的输出，并返回每条记录。 如果其中一个记录存在[异常](automation-runbook-execution.md#exceptions)，则脚本将写入异常，而不是写入值。 此行为很有用，因为异常可以提供在输出过程中可能无法正常记录的其他信息。

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

## <a name="next-steps"></a>后续步骤

* 若要了解 runbook 处理，请参阅[在 Azure 自动化中执行 runbook](automation-runbook-execution.md)。
* 若要了解有关使用文本编辑器编辑 PowerShell 和 PowerShell 工作流 runbook 的详细信息，请参阅[在 Azure 自动化中编辑文本 runbook](automation-edit-textual-runbook.md)。
* 若要详细了解图形 runbook 创作，请参阅[Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
