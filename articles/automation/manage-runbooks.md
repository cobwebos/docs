---
title: 在 Azure 自动化中管理 Runbook
description: 本文介绍如何在 Azure 自动化中管理 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 93b34af0baed89fd312948aeffe8ea4ac8ef806c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834690"
---
# <a name="manage-runbooks-in-azure-automation"></a>在 Azure 自动化中管理 Runbook

可创建新的 Runbook，或从文件或 [Runbook 库](automation-runbook-gallery.md)中导入现有 Runbook，将 Runbook 添加到 Azure 自动化中。 本文介绍如何管理从文件中导入的 Runbook。 可在 [Azure 自动化的 Runbook 和模块库](automation-runbook-gallery.md)中获取有关如何访问社区 Runbook 和模块的全部详细信息。

## <a name="create-a-runbook"></a>创建 Runbook

使用 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建新的 Runbook。 Runbook 创建后，就可使用下文中的信息编辑它：

* [在 Azure 自动化中编辑文本 Runbook](automation-edit-textual-runbook.md) 
* [了解自动化 Runbook 的关键 Windows PowerShell 工作流概念](automation-powershell-workflow.md)
* [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* [在 Azure 自动化中管理 Python 2 包](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 门户中创建 Runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心内，在“流程自动化”下选择“Runbook”，打开 Runbook 的列表 。
3. 单击“创建 Runbook”。
4. 键入 Runbook 的名称并选择其[类型](automation-runbook-types.md)。 Runbook 名称必须以字母开头，可包含字母、数字、下划线和短划线。
5. 单击“创建”以创建 Runbook 并打开编辑器。

### <a name="create-a-runbook-with-powershell"></a>通过 PowerShell 创建 Runbook

使用 [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet 创建空的 Runbook。 使用 `Type` 参数指定为 `New-AzAutomationRunbook` 定义的其中一种 Runbook 类型。

以下示例演示了如何创建新的空 Runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>导入 Runbook

可导入 PowerShell 或 PowerShell 工作流脚本 (.ps1)、图形 Runbook (.graphrunbook) 或 Python 2 脚本 (.py) 来创建自己的 Runbook  。  必须指定在导入期间创建的 [Runbook 类型](automation-runbook-types.md)，并考虑以下注意事项。

* 可将不含工作流的 .ps1 文件导入 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果将其导入 PowerShell 工作流 Runbook，它将转换为工作流。 这样的话，Runbook 会包含注释来描述所作的更改。

* 仅可将包含 PowerShell 工作流的 .ps1 文件导入 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果该文件包含多个 PowerShell 工作流，则导入将失败。 必须将每个工作流保存到各自的文件中，并分别导入每个工作流。

* 请勿将包含 PowerShell 工作流的 .ps1 文件导入 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)，因为 PowerShell 脚本引擎无法识别它。

* 仅可将 .graphrunbook 文件导入新的[图形 Runbook](automation-runbook-types.md#graphical-runbooks)。 

### <a name="import-a-runbook-from-the-azure-portal"></a>通过 Azure 门户导入 Runbook

可通过以下过程将脚本文件导入 Azure 自动化。

> [!NOTE]
> 只能通过此门户将 .ps1 文件导入 PowerShell 工作流 Runbook。

1. 在 Azure 门户中，打开自动化帐户。
2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。 
3. 单击“导入 Runbook”。
4. 单击“Runbook 文件”并选择要导入的文件。
5. 如果启用了“名称”字段，则你可更改 Runbook 名称。 该名称必须以字母开头，可包含字母、数字、下划线和短划线。
6. 将自动选择 [Runbook 类型](automation-runbook-types.md)，但可以在考虑适用的限制后更改该类型。
7. 单击“创建”。 新的 runbook 会出现在自动化帐户的 runbook 列表中。
8. 必须先[发布 Runbook](#publish-a-runbook)，才能运行它。

> [!NOTE]
> 导入图形 Runbook 后，可将其转换为其他类型。 但是，无法将图形 Runbook 转换为文本 Runbook。

### <a name="import-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 导入 Runbook

使用 [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet 将脚本文件作为草稿 Runbook 导入。 如果该 Runbook 已存在，则导入将失败，除非你将 `Force` 参数与 cmdlet 一起使用。

以下示例演示了如何将脚本文件导入到 Runbook 中。

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

如果你的 Runbook 创建了一项[资源](automation-runbook-execution.md#resources)脚本应检查看看在尝试创建该资源之前它是否已存在。 下面是一个基本示例。

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

可从自动化帐户的[活动日志](automation-runbook-execution.md#activity-logging)中检索 Runbook 详细信息，例如启动 Runbook 的人员或帐户。 以下 PowerShell 示例显示了运行指定 Runbook 的最后一名用户。

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

最佳做法是，使用可轻松重用和重启的逻辑将 Runbook 创作为本质上模块化的内容。 跟踪在 Runbook 中的进度可确保 Runbook 逻辑在出现问题时正确执行。 

可使用外部源（例如存储帐户、数据库或共享文件）来跟踪 Runbook 的进度。 在 Runbook 中创建逻辑，从而先检查所执行的最后一个操作的状态。 然后，根据检查结果，可跳过逻辑，或者逻辑在 Runbook 中继续特定任务。

## <a name="prevent-concurrent-jobs"></a>预防并发作业

如果一些 Runbook 同时跨多个作业运行，则它们可能会表现得很奇怪。 在这种情况下，重要的是让 Runbook 实现逻辑来确定是否已有正在运行的作业。 下面是一个基本示例。

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>处理依赖时间的脚本中的暂时性错误

Runbook 必须可靠且能够处理[错误](automation-runbook-execution.md#errors)，包括可能导致其重启或失败的暂时性错误。 如果 Runbook 失败，Azure 自动化将重试它。

如果你的 Runbook 通常在一定时间内运行，请让脚本实现逻辑来检查执行时间。 该项检查可确保仅在特定的时间内运行诸如启动、关闭或横向扩展之类的操作。

> [!NOTE]
> Azure 沙盒上的本地时间被设置为 UTC。 计算 Runbook 中的日期和时间时，必须考虑到这一事实。

## <a name="work-with-multiple-subscriptions"></a>使用多个订阅

Runbook 必须能够处理[订阅](automation-runbook-execution.md#subscriptions)。 例如，Runbook 会使用 [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet 来处理多个订阅。 该 cmdlet 可确保不从正在同一沙盒中运行的另一 Runbook 中检索身份验证上下文。 Runbook 还使用 Az 模块 cmdlet 上的 `AzContext` 参数并将它传递适当的上下文。

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
> 通过无法在已安装 Log Analytics 代理的主机上运行自定义脚本和 Runbook。 

若要使用自定义脚本：

1. 创建一个自动化帐户并获取[参与者角色](automation-role-based-access-control.md)。
2. [将帐户关联到 Azure 工作区](../security-center/security-center-enable-data-collection.md).
3. 启用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)、[更新管理](automation-update-management.md)或其他自动化功能。 
4. 如果是在 Linux 计算机上，则需要很高的权限。 登录来[关闭签名检查](automation-linux-hrw-install.md#turn-off-signature-validation)。

## <a name="test-a-runbook"></a>测试 Runbook

测试 Runbook 时，将执行[草稿版](#publish-a-runbook)，并会完成其所执行的任何操作。 不会创建作业历史记录，但会在“测试输出”窗格中显示[输出](automation-runbook-output-and-messages.md#use-the-output-stream)与[警告和错误](automation-runbook-output-and-messages.md#monitor-message-streams)。 仅当 [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) 变量设置为 `Continue` 时，“输出”窗格中才会显示发送到[详细流](automation-runbook-output-and-messages.md#monitor-message-streams)的消息。

即使草稿版正在运行，该 Runbook 也仍会正常执行，并针对环境中的资源执行任何操作。 因此，只能在非生产资源中测试 Runbook。

测试各[类型的 Runbook](automation-runbook-types.md) 的流程相同。 Azure 门户中文本编辑器测试与图形编辑器测试之间没有区别。

1. 在[文本编辑器](automation-edit-textual-runbook.md)或[图形编辑器](automation-graphical-authoring-intro.md)中打开 Runbook 的草稿版本。
1. 单击“测试”打开测试页面。
1. 如果 Runbook 具有参数，它们会在左窗格中列出，你可在这里提供要用于测试的值。
1. 若要对[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)运行测试，请将“运行设置”更改为“混合辅助角色”，并选择目标组的名称 。  否则，保留默认值 **Azure**，以在云中运行测试。
1. 单击“启动”，开始测试。
1. 在测试期间，可使用“输出”窗格下面的按钮来停止或暂停 [PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks)或[图形](automation-runbook-types.md#graphical-runbooks) Runbook。 暂停 Runbook 时，该 Runbook 会完成它在被暂停之前正在进行的活动。 暂停 Runbook 后，可以将它停止或重启。
1. 在“输出”窗格中检查来自 Runbook 的输出。

## <a name="publish-a-runbook"></a>发布 Runbook

创建或导入新的 Runbook 时，必须先将其发布，然后才能导入。 Azure 自动化中的每个 Runbook 都有一个草稿版本和一个已发布版本。 只有已发布版才能用来运行，只有草稿版才能用来编辑。 已发布版不受对草稿版所做的任何更改的影响。 当应该提供草稿版本时，你要发布它，使用草稿版本覆盖当前的已发布版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 门户中发布 Runbook

1. 在 Azure 门户中打开 Runbook。
2. 单击 **“编辑”** 。
3. 单击“发布”，然后在对验证消息的响应中单击“是” 。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 运行 Runbook

使用 [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet 发布 Runbook。 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>在 Azure 门户中计划 Runbook

当你的 Runbook 已发布后，可计划它进行操作：

1. 在 Azure 门户中打开 Runbook。
2. 在“资源”下选择“计划” 。
3. 选择“添加计划”。
4. 在“计划 Runbook”窗格中，选择“将计划关联到 Runbook”。
5. 在“计划”窗格中选择“创建新计划”。
6. 在“新建计划”窗格中输入名称、说明和其他参数。 
7. 创建计划后，将其突出显示并单击“确定”。 它现应与你的 Runbook 关联。
8. 查看邮箱中的电子邮件，里面有 Runbook 的状态。

## <a name="obtain-job-statuses"></a>获取作业状态

### <a name="view-statuses-in-the-azure-portal"></a>在 Azure 门户中查看状态

可通过[作业](automation-runbook-execution.md#jobs)查看在 Azure 自动化中处理的作业的详细信息。 准备好查看 Runbook 作业后，使用 Azure 门户并访问你的自动化帐户。 你可在右侧的“作业统计信息”中看到所有 Runbook 作业的摘要。 

![作业统计信息磁贴](./media/manage-runbooks/automation-account-job-status-summary.png)

该摘要显示了所执行的每项作业的状态的计数和图形表示形式。

单击磁贴可显示“作业”页面，其中有所执行的全部作业的汇总列表。 该页面会显示每项作业的状态、Runbook 名称、开始时间和完成时间。

![自动化帐户作业页](./media/manage-runbooks/automation-account-jobs-status-blade.png)

可选择“筛选作业”来筛选作业的列表。 根据特定 Runbook、作业状态或从下拉列表中选择的内容进行筛选，并提供搜索的时间范围。

![筛选作业状态](./media/manage-runbooks/automation-account-jobs-filter.png)

或者，可从自动化帐户中的 Runbook 页面上选择特定的 Runbook，然后选择“作业”来查看该 Runbook 的作业摘要详情。 该操作会显示“作业”页面。 你可在这里单击作业记录，查看它的详细信息和输出内容。

![自动化帐户作业页](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>使用 PowerShell 检索作业状态

使用 [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) cmdlet 检索为 Runbook 创建的作业和特定作业的详细信息。 如果使用 `Start-AzAutomationRunbook` 启动 Runbook，它会返回生成的作业。 使用 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) 检索作业输出。

以下示例会获取示例 Runbook 的最后一项作业，并显示它的状态、为 Runbook 参数提供的值以及作业的输出内容。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

以下示例会检索特定作业的输出，并返回每条记录。 如果其中一个记录出现[异常](automation-runbook-execution.md#exceptions)，脚本将写出异常而不是值。 此行为非常有用，因为异常可提供在输出过程中可能无法正常记录的其他信息。

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

* 若要了解 Runbook 管理的详细信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)。
* 若要准备 PowerShell Runbook，请参阅[在 Azure 自动化中编辑文本 Runbook](automation-edit-textual-runbook.md)。
* 有关编写 PowerShell 工作流 Runbook 的帮助，请参阅[了解 Azure 自动化的 PowerShell 工作流](automation-powershell-workflow.md)。
* 若要详细了解如何编写图形 Runbook，请参阅[在 Azure 自动化中创作图形 Runbook](automation-graphical-authoring-intro.md)。
* 要排查 Runbook 执行问题，请参阅[排查 Runbook 问题](troubleshoot/runbooks.md)。
