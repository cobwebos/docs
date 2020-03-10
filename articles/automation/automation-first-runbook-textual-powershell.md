---
title: 我在 Azure 自动化中的第一个 PowerShell Runbook
description: 本教程指导创建、测试和发布一个简单的 PowerShell Runbook。
keywords: azure powershell, powershell 脚本教程, powershell 自动化
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384855"
---
# <a name="my-first-powershell-runbook"></a>我的第一个 PowerShell Runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教程介绍了如何在 Azure 自动化中创建 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 。 首先，可以测试和发布一个简单的 runbook，同时了解如何跟踪 runbook 作业的状态。 然后，将 runbook 修改为实际管理 Azure 资源，在这种情况下，将启动 Azure 虚拟机。 完成本教程，通过添加 runbook 参数使 runbook 更可靠。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-quickstart-create-account.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于你停止并启动此计算机，因此不应为生产 VM。
* 如有必要，请根据所使用的 cmdlet[导入 Azure 模块](shared-resources/modules.md)或[更新模块](automation-update-azure-modules.md)。

## <a name="differences-from-powershell-workflow-runbooks"></a>与 PowerShell 工作流 runbook 的差异

PowerShell runbook 与 PowerShell 工作流 runbook 具有相同的生命周期、功能和管理功能。 但存在一些差异和限制。

* PowerShell runbook 比 PowerShell 工作流 runbook 的运行速度更快，因为它们不使用编译步骤。
* PowerShell 工作簿不支持 PowerShell 工作流 runbook 中使用的检查点。 使用检查点，PowerShell 工作流 runbook 可以从任何点恢复操作。 你的 PowerShell runbook 只能从一开始就恢复操作。
* PowerShell runbook 仅可按顺序执行命令。 PowerShell 工作流 runbook 支持串行和并行执行。
* 对于 PowerShell runbook，脚本中的所有内容都在单个运行空间中运行。 在 PowerShell 工作流 runbook 中，活动、命令或脚本块可以有自己的运行空间。 
* PowerShell runbook 与 PowerShell 工作流 runbook 有一些[语法差异](https://technet.microsoft.com/magazine/dn151046.aspx)。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个输出文本 **Hello World** 的简单 Runbook。

1. 在 Azure 门户中，打开自动化帐户。
2. 选择 "**流程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
3. 通过选择 "**创建 runbook**" 来创建新的 runbook。
4. 将该 Runbook 命名为 **MyFirstRunbook-PowerShell**。
5. 在这种情况下，你将创建一个[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)。 选择 " **Powershell** " 作为 " **Runbook 类型**"。
6. 单击“创建”以创建 Runbook 并打开文本编辑器 。

## <a name="step-2---add-code-to-the-runbook"></a>步骤 2 - 将代码添加到 Runbook

可以直接将代码键入 Runbook 中，或者通过“库”控件选择 cmdlet、Runbook 和资产，并使用任何相关的参数将它们添加到 Runbook。 对于本教程，你将直接在 runbook 中键入代码。

1. 你的 runbook 当前为空。 在脚本正文中键入 `Write-Output "Hello World"`。

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. 通过单击“保存”保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3-测试 runbook

在发布 runbook 以使其在生产中可用之前，应该对其进行测试以确保其正常运行。 测试 runbook 将运行其草稿版本，并允许你以交互方式查看其输出。

1. 单击“测试窗格” 打开测试窗格。
2. 单击“启动”以启动测试 。 这应该是唯一的已启用选项。
3. 请注意，将创建一个[runbook 作业](automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态将作为**排队**开始，指示作业正在等待云中的 runbook 辅助角色变为可用。 当工作线程声明作业时，状态将更改为 "**正在启动**"。 最后，当 runbook 实际开始运行时，状态将变为 "**正在运行**"。

4. Runbook 作业完成后，"测试" 窗格将显示其输出。 在这种情况下，会看到 **Hello World**。

   ![测试窗格输出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

你创建的 runbook 仍处于草稿模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是”。
1. 向左滚动以在 "Runbook" 页上查看 runbook，并注意 "**创作状态**" 值已设置为 "**已发布**"。
1. 向右滚动查看 **MyFirstRunbook-PowerShell**的窗格。
   
   通过顶部的选项，你可以立即启动 runbook，计划将来的开始时间，或创建[webhook](automation-webhooks.md)以便可以通过 HTTP 调用启动 runbook。
1. 选择“启动”，并在出现提示时选择“是”启动 runbook。 
1. 将为已创建的 runbook 作业打开作业窗格。 虽然你可以关闭此窗格，但现在使其保持打开状态，以便你可以查看作业的进度。 作业状态显示在 "**作业摘要**" 中，如测试 runbook 所述。

   ![作业摘要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook 状态显示为 "**已完成**" 后，单击 "**输出**" 打开 "输出" 页，您可以在其中看到**Hello World**显示。

   ![作业输出](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 关闭“输出”页。
1. 单击“所有日志”打开 Runbook 作业的“流”窗格。 只应在输出流中看到**Hello World** 。

    请注意，"流" 窗格可以显示 runbook 作业的其他流，如 "详细" 和 "错误流" （如果 runbook 向其写入）。

   ![所有日志](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. 关闭 "流" 窗格和 "作业" 窗格以返回到**MyFirstRunbook**页。
1. 在 "**详细信息**" 下，单击 "**作业**" 打开此 runbook 的 "作业" 页。 此页列出了 runbook 创建的所有作业。 你应该只会看到一个列出的作业，因为你只运行一次作业。

   ![作业列表](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 单击作业名称，打开在启动 runbook 时查看的相同的作业窗格。 使用此窗格查看为 runbook 创建的任何作业的详细信息。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步骤 5 - 添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 要执行此操作，runbook 必须能够使用在创建自动化帐户时自动创建的运行方式帐户进行身份验证。

如以下示例中所示，运行方式连接是用[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 建立的。 如果跨多个订阅管理资源，则需要将*AzContext*参数与[AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)一起使用。

> [!NOTE]
> 对于 PowerShell runbook， **AzAccount**和**add-azurermaccount**是**AzAccount**的别名。 可以使用这些 cmdlet，或者将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新模块。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. 通过单击**MyFirstRunbook**页上的 "**编辑**" 打开文本编辑器。
1. 不再需要**写入输出**行。 请继续并删除它。
1. 键入或复制并粘贴以下代码，这些代码将用自动化运行方式帐户处理身份验证。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. 单击“测试”窗格，以便测试 Runbook。
1. 单击“启动”以启动测试 。 完成后，应会看到类似于下面的输出，其中显示了帐户的基本信息。 此输出确认运行方式帐户有效。

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步骤 6 – 添加用于启动虚拟机的代码

现在 Runbook 正在向 Azure 订阅进行身份验证，你可以管理资源。 让我们添加一个命令来启动虚拟机。 你可以在 Azure 订阅中选取任何虚拟机，而只需将该名称硬编码到 runbook 中。

1. 在 runbook 脚本中，添加[new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 以启动虚拟机。 如下所示，该 cmdlet 使用名为**VMName**的虚拟机和名为**ResourceGroupName**的资源组启动虚拟机。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. 保存 Runbook，并单击“测试”窗格，以便可以测试 Runbook。
1. 单击 "**启动**" 以开始测试。 完成后，请确保虚拟机已启动。

## <a name="step-7---add-an-input-parameter"></a>步骤 7-添加输入参数

你的 runbook 当前会启动你在 runbook 中硬编码的虚拟机。 如果在启动 runbook 时指定虚拟机，runbook 将更有用。 让我们向 runbook 添加输入参数以提供该功能。

1. 在文本编辑器中，修改**new-azvm** cmdlet 以使用参数*VMName*和*ResourceGroupName*的变量。 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。
1. 关闭“测试”窗格。
1. 单击“发布”以发布 Runbook 的新版本 。
1. 停止之前启动的虚拟机。
1. 单击“启动”以启动 Runbook **ResourceGroupName** 。 
1. 键入要启动的虚拟机的**VMNAME**和**RESOURCEGROUPNAME**的值，然后单击 **"确定"** 。<br><br> ![传递参数](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Runbook 完成后，请确保已启动虚拟机。

## <a name="next-steps"></a>后续步骤

* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](/powershell/scripting/overview)。
* 若要开始处理图形 runbook，请参阅[我的第一个图形 runbook](automation-first-runbook-graphical.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)。
* 若要了解有关 runbook 类型及其优点和限制的详细信息，请参阅[Azure 自动化 runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅[Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
