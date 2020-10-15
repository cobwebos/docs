---
title: 在 Azure 自动化中创建 PowerShell Runbook
description: 本文指导如何创建、测试和发布简单的 PowerShell Runbook。
keywords: azure powershell, powershell 脚本教程, powershell 自动化
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: a1b0dff9421f493958554c659043c49ff2874379
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87014994"
---
# <a name="tutorial-create-a-powershell-runbook"></a>教程：创建 PowerShell Runbook

本教程介绍了如何在 Azure 自动化中创建 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) 。 基于 Windows PowerShell 的 PowerShell Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。

> [!div class="checklist"]
> * 创建简单的 PowerShell Runbook
> * 测试并发布 Runbook
> * 运行 Runbook 作业并跟踪其状态
> * 更新 Runbook 以使用 Runbook 参数启动 Azure 虚拟机

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](../automation-quickstart-create-account.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于需要停止并启动此虚拟机，因此它不应当是生产 VM。
* 如果需要，请[导入 Azure 模块](../shared-resources/modules.md)或[更新模块](../automation-update-azure-modules.md)，具体取决于所用的 cmdlet。

## <a name="differences-from-powershell-workflow-runbooks"></a>与 PowerShell 工作流 Runbook 的差异

PowerShell Runbook 与 PowerShell 工作流 Runbook 具有相同的生命周期、功能和管理方式。 但是，它们也存在一些差异和限制。

| 特征  | PowerShell Runbook | PowerShell 工作流 Runbook |
| ------ | ----- | ----- |
| Speed | 不使用编译步骤，因此运行速度较快。 | 运行速度更慢。 |
| 检查点 | 不支持检查点。 PowerShell Runbook 只能从头开始恢复操作。 | 使用检查点，使工作簿可以从任意位置恢复操作。 |
| 命令执行 | 仅支持串行执行。 | 支持串行和并行执行。|
| Runspace | 单个运行空间运行脚本中的所有内容。 | 一个单独的运行空间可用于活动、命令或脚本块。 |

除这些差异外，PowerShell Runbook 还与 PowerShell 工作流 Runbook 存在一些[语法差异](/previous-versions/technet-magazine/dn151046(v=msdn.10))。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个将输出文本 `Hello World` 的简单 Runbook。

1. 在 Azure 门户中，打开自动化帐户。

2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。

3. 选择“创建 Runbook”，创建一个新的 Runbook。

4. 将该 Runbook 命名为 **MyFirstRunbook-PowerShell**。

5. 在本例中，我们将创建一个 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks)。 对于“Runbook 类型”，请选择“PowerShell”。

6. 单击“创建”以创建 Runbook 并打开文本编辑器  。

## <a name="step-2---add-code-to-the-runbook"></a>步骤 2 - 将代码添加到 Runbook

可以直接将代码键入 Runbook 中，或者通过“库”控件选择 cmdlet、Runbook 和资产，并使用任何相关的参数将它们添加到 Runbook。 对于本教程，请直接在 Runbook 中键入代码。

1. Runbook 当前是空的。 在脚本正文中键入 `Write-Output "Hello World"`。

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. 通过单击“保存” 保存 Runbook。

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 步骤 3 - 测试 Runbook

在发布 Runbook 使其可在生产中使用之前，应对其进行测试，确保其能正常运行。 测试 Runbook 时将运行其“草稿”版本，你可以通过交互方式查看其输出。

1. 单击“测试窗格”  打开测试窗格。

2. 单击“启动”以启动测试  。 这应该是唯一的已启用选项。

3. 请注意，此时会创建一个 [Runbook 作业](../automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态一开始为“排队”，表明作业正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，其状态会变为“正在启动”。 最后，当 Runbook 开始实际运行时，其状态会变为“正在运行”。

4. Runbook 作业完成后，“测试”窗格会显示其输出。 在本例中，你会看到 `Hello World`。

   ![测试窗格输出](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

创建的 Runbook 仍处于“草稿”模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是” 。

2. 在“Runbook”页中向左滚动以查看该 Runbook，请注意，“创作状态”值设置为“已发布” 。

3. 向右滚动查看“MyFirstRunbook-PowerShell”的页。
   
   顶部的选项现在可用于启动 Runbook、计划将来的启动时间，或创建一个 [webhook](../automation-webhooks.md) 以便通过 HTTP 调用来启动 Runbook。

4. 选择“启动”，并在出现提示时选择“是” 启动 runbook。 

5. 此时会为已创建的 Runbook 作业打开一个“作业”窗格。 可以关闭此窗格，不过，暂时最好是使其保持打开状态，以便可以观察作业的进度。 作业状态显示在“作业摘要”中，其中描述了测试 Runbook 时会出现的可能状态。

   ![作业摘要](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Runbook 状态显示为“已完成”后，单击“输出”打开“输出”页，可以看到其中显示了 `Hello World`。

   ![作业输出](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. 关闭“输出”页。

8. 单击“所有日志”打开 Runbook 作业的“流”窗格。 在输出流中应该只会看到 `Hello World`。

    请注意，“流”窗格可以显示 Runbook 作业的其他流，例如，详细流和错误流（如果 Runbook 向其写入内容）。

   ![所有日志](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. 关闭“流”窗格和“作业”窗格，返回到“MyFirstRunbook-PowerShell”页。

10. 在“详细信息”下单击“作业”，打开此 Runbook 的“作业”页 。 此页列出你的 Runbook 创建的所有作业。 由于只将该作业运行了一次，应该只会看到一个列出的作业。

   ![作业列表](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. 单击作业名称，以打开你在启动 Runbook 时查看过的同一“作业”窗格。 使用此窗格可查看为 Runbook 创建的任何作业的详细信息。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步骤 5 - 添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 为此，Runbook 必须能够使用在创建自动化帐户时自动创建的运行方式帐户进行身份验证。

如以下示例中所示，运行方式帐户连接是使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 建立的。 如果跨多个订阅管理资源，需要结合 `AzContext` 参数使用 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)。

> [!NOTE]
> 对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](../automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. 通过单击 MyFirstRunbook-PowerShell 页上的“编辑”打开文本编辑器。

2. 不再需要 `Write-Output` 行。 请直接删除它。

3. 键入或者复制并粘贴以下代码，该代码使用自动化运行方式帐户来处理身份验证。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. 单击“测试”窗格，以便测试 Runbook。

5. 单击“启动”以启动测试  。 完成后，你应该会看到类似以下内容的输出，它显示了帐户的基本信息。 此输出确认运行方式帐户有效。

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步骤 6 – 添加用于启动虚拟机的代码

现在 Runbook 正在向 Azure 订阅进行身份验证，你可以管理资源。 让我们添加一项命令，用于启动虚拟机。 可以在 Azure 订阅中选取任何虚拟机，但暂时只需在 Runbook 中对该名称进行硬编码。

1. 在 Runbook 脚本中，添加 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 来启动虚拟机。 如下所示，该 cmdlet 启动名为 `VMName` 的虚拟机以及名为 `ResourceGroupName` 的资源组。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. 保存 Runbook，并单击“测试”窗格，以便可以测试 Runbook。

3. 单击“启动”，开始测试。 完成后，确保虚拟机已启动。

## <a name="step-7---add-an-input-parameter"></a>步骤 7 - 添加输入参数

Runbook 当前会启动已在 Runbook 中硬编码的虚拟机。 如果在启动 Runbook 时指定虚拟机，该 Runbook 会更有用。 让我们向 Runbook 中添加输入参数，以提供该功能。

1. 在文本编辑器中修改 `Start-AzVM` cmdlet，以便对参数 `VMName` 和 `ResourceGroupName` 使用变量。 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。

3. 关闭“测试”窗格。

4. 单击“发布”以发布 Runbook 的新版本  。

5. 停止前面启动的虚拟机。

6. 单击“启动”以启动 Runbook **ResourceGroupName** 。 

7. 键入要启动的虚拟机的“VMNAME”和“RESOURCEGROUPNAME”值，然后单击“确定”。

    ![传递参数](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Runbook 完成后，确保虚拟机已启动。

## <a name="next-steps"></a>后续步骤

* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
* 若要开始使用图形 Runbook，请参阅[创建图形 Runbook](automation-tutorial-runbook-graphical.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅[创建 PowerShell 工作流 Runbook](automation-tutorial-runbook-textual.md)。
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](../automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅 [Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
