---
title: 在 Azure 自动化中创建 PowerShell 工作流 Runbook
description: 本文指导如何创建、测试和发布简单的 PowerShell 工作流 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 613ce41820dc5f388af441836bdb4a93e43e5d07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987629"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>教程：创建 PowerShell 工作流 Runbook

本教程指导在 Azure 自动化中创建 [PowerShell 工作流 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 PowerShell 工作流 Runbook 是基于 Windows PowerShell 工作流的文本 Runbook。 可以在 Azure 门户中使用文本编辑器创建和编辑 Runbook 的代码。 

> [!div class="checklist"]
> * 创建简单的 PowerShell 工作流 Runbook
> * 测试并发布 Runbook
> * 运行 Runbook 作业并跟踪其状态
> * 更新 Runbook 以使用 Runbook 参数启动 Azure 虚拟机

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](../index.yml) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于需要停止并启动此虚拟机，因此它不应当是生产 VM。

## <a name="step-1---create-new-runbook"></a>步骤 1 - 创建新的 Runbook

首先创建一个将输出文本 `Hello World` 的简单 Runbook。

1. 在 Azure 门户中，打开自动化帐户。

   通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 大多数此类资产是自动包括在新的自动化帐户中的模块。 你还应该有与你的订阅关联的凭据资产。
 
2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。 

3. 选择“创建 Runbook”，创建一个新的 Runbook。

4. 将该 Runbook 命名为 **MyFirstRunbook-Workflow**。

5. 在此示例中，我们将创建 [PowerShell Workflow Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 选择“PowerShell 工作流”作为“Runbook 类型”。 

6. 单击“创建”以创建 Runbook 并打开文本编辑器  。

## <a name="step-2---add-code-to-the-runbook"></a>步骤 2 - 将代码添加到 Runbook

可以直接将代码键入 Runbook 中，或者从“库”控件中选择 cmdlet、Runbook 和资产，并使用任何相关的参数将它们添加到 Runbook。 在本教程中，我们将代码直接键入 Runbook。

1. Runbook 目前为空，只有必需的 `Workflow` 关键字、Runbook 名称以及括住整个工作流的大括号。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. 在大括号中键入 `Write-Output "Hello World"`。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. 通过单击“保存” 保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3 - 测试 Runbook

在发布 Runbook 使其可在生产中使用之前，应对其进行测试，确保其能正常运行。 测试 Runbook 时将运行其“草稿”版本，你可以通过交互方式查看其输出。

1. 选择“测试窗格”以打开“测试”窗格。

2. 单击“启动”以启动测试，只测试已启用的选项。

3. 请注意，此时会创建一个 [Runbook 作业](../automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态一开始为“排队”，表明作业正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，其状态会变为“正在启动”。 最后，当 Runbook 开始实际运行时，其状态会变为“正在运行”。

4. Runbook 作业完成后，“测试”窗格会显示其输出。 在本例中，你会看到 `Hello World`。

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

你创建的 Runbook 仍处于“草稿”模式。 必须先发布它，然后才能在生产中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是” 。

2. 在“Runbook”页中向左滚动以查看该 Runbook，请注意，“创作状态”字段设置为“已发布”  。

3. 向右滚动，查看 MyFirstRunbook-Workflow 的页面。

   顶部的选项现在可用于启动 Runbook、计划将来的启动时间，或创建一个 [webhook](../automation-webhooks.md) 以便通过 HTTP 调用来启动 Runbook。

4. 选择“启动”，并在出现提示时选择“是” 启动 runbook。

   ![启动 Runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. 此时会为已创建的 Runbook 作业打开一个“作业”窗格。 在此示例中，请让窗格保持打开状态，以便观察作业的进度。

6. 请注意，作业状态显示在“作业摘要”中。 此状态与测试 Runbook 时看到的状态相符。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Runbook 作业窗格的屏幕截图，其中突出显示了“作业摘要”部分。":::

7. 在 Runbook 状态显示为“已完成”后，单击“输出”。 此时会打开“输出”页，可以在其中看到 `Hello World` 消息。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Runbook 作业窗格的屏幕截图，其中突出显示了“作业摘要”部分。":::

8. 关闭“输出”页。

9. 单击“所有日志”打开 Runbook 作业的“流”窗格。 在输出流中应该只会看到 `Hello World`。 请注意，“流”窗格可以显示 Runbook 作业的其他流，例如，详细流和错误流（如果 Runbook 向其写入内容）。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Runbook 作业窗格的屏幕截图，其中突出显示了“作业摘要”部分。":::

10. 关闭“流”窗格和“作业”窗格以返回到 MyFirstRunbook 页。

11. 在“资源”下单击“作业”，以打开此 Runbook 的“作业”页 。 此页列出你的 Runbook 创建的所有作业。 由于只将该作业运行了一次，应该只会看到一个列出的作业。

   ![作业](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. 单击作业名称，以打开你在启动 Runbook 时查看过的同一“作业”窗格。 使用此窗格可查看为 Runbook 创建的任何作业的详细信息。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步骤 5 - 添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 除非它使用该订阅的凭据进行身份验证，否则它不能执行该操作。 身份验证使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) cmdlet。

>[!NOTE]
>对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](../automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

1. 导航到 MyFirstRunbook-Workflow 页，通过单击“编辑”打开文本编辑器。

2. 删除 `Write-Output` 行。

3. 将光标放在大括号之间的空白行上。

4. 键入或者复制并粘贴以下代码，该代码使用自动化运行方式帐户来处理身份验证。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. 单击“测试”窗格，以便测试 Runbook。

6. 单击“启动”以启动测试  。 完成后，你应该会看到类似以下内容的输出，它显示了帐户的基本信息。 此操作确认凭据有效。

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步骤 6 – 添加用于启动虚拟机的代码

现在，你的 Runbook 向 Azure 订阅进行身份验证，你可以管理资源。 让我们添加一项命令，用于启动虚拟机。 你可以选取你的 Azure 订阅中的任何 VM，但现在，你将在 Runbook 中将该名称硬编码。 如果要跨多个订阅管理资源，需要将 `AzContext` 参数和 [Get-AzContext](/powershell/module/az.accounts/get-azcontext) cmdlet 配合使用。

1. 提供要启动的 VM 的名称和其资源组名称，方法是输入对 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 的调用，如下所示。 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. 保存 Runbook，并单击“测试”窗格，以便可以测试 Runbook。

3. 单击“启动”以启动测试  。 测试完成后，检查 VM 是否已启动。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步骤 7 - 将输入参数添加到 Runbook

Runbook 当前启动的是你在 Runbook 中硬编码的 VM。 如果可以在启动 Runbook 时指定 VM，它会更有用。 让我们向 Runbook 中添加输入参数，以提供该功能。

1. 将 `VMName` 和 `ResourceGroupName` 参数的变量添加到 Runbook，并将这些变量与 `Start-AzVM` cmdlet 配合使用，如下所示。

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. 保存 Runbook 并打开“测试”窗格。 现在可以为测试中使用的两个输入变量提供值。

3. 关闭“测试”窗格。

4. 单击“发布”以发布 Runbook 的新版本  。

5. 停止已启动的 VM。

6. 单击“启动”以启动 Runbook **ResourceGroupName** 。 

7. 键入要启动的 VM 的 VMNAME 和 RESOURCEGROUPNAME 的值。

   ![启动 Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Runbook 完成后，验证 VM 是否已启动。

## <a name="next-steps"></a>后续步骤

* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
* 若要开始使用图形 Runbook，请参阅[创建图形 Runbook](automation-tutorial-runbook-graphical.md)。
* 若要开始使用 PowerShell Runbook，请参阅[创建 PowerShell Runbook](automation-tutorial-runbook-textual-powershell.md)。
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](../automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅 [Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
