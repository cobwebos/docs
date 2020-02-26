---
title: 我在 Azure 自动化中的第一个 PowerShell 工作流 Runbook
description: 本教程指导使用 PowerShell 工作流创建、测试和发布一个简单的文本 Runbook。
keywords: powershell 工作流, powershell 工作流示例, 工作流 powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: b96860afd649f33936ee8dd2954e6873f908a369
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605072"
---
# <a name="my-first-powershell-workflow-runbook"></a>我的第一个 PowerShell 工作流 Runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教程指导在 Azure 自动化中创建 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 从一个简单的 runbook 开始，你可以测试和发布该 runbook，同时了解如何跟踪 runbook 作业的状态。 然后，将 runbook 修改为实际管理 Azure 资源，通过启动 Azure 虚拟机进行说明。 最后，通过添加 runbook 参数使此 runbook 更可靠。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于你停止并启动此计算机，因此不应为生产 VM。

## <a name="step-1---create-new-runbook"></a>步骤 1 - 创建新的 Runbook

首先创建一个输出文本 "Hello World" 的简单 runbook。

1. 在 Azure 门户中，打开自动化帐户。

   通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 其中的大多数资产都是自动包括在新的自动化帐户中的模块。 还应提供与订阅关联的凭据资产。
 
1. 选择 "**流程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
1. 通过选择 " **+ 创建 runbook**" 来创建新的 runbook。
1. 为 runbook 指定名称 "MyFirstRunbook"。
1. 在这种情况下，你将创建一个[PowerShell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 因此，请选择 " **Powershell 工作流**" 作为 " **Runbook 类型**"。
1. 单击“创建”以创建 Runbook 并打开文本编辑器 。

## <a name="step-2---add-code-to-the-runbook"></a>步骤 2 - 将代码添加到 Runbook

你可以直接在 runbook 中键入代码，也可以从 "库" 控件选择 cmdlet、runbook 和资产，并将其添加到具有任何相关参数的 runbook。 对于本演练，请将代码直接键入 runbook。

1. 你的 runbook 当前为空，仅包含所需的**workflow**关键字、runbook 的名称和以及括住整个工作流的大括号。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 在大括号之间键入 `Write-Output "Hello World"`。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. 通过单击“保存”保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3 - 测试 Runbook

在发布 runbook 以使其在生产中可用之前，应该对其进行测试以确保其正常运行。 测试 runbook 将运行其草稿版本，并允许你以交互方式查看其输出。

1. 选择 "**测试窗格**" 打开 "测试" 窗格。
1. 单击 "**启动**" 以启动测试，并测试 "唯一启用" 选项。
1. 请注意，将创建一个[runbook 作业](automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态将作为排队开始，指示作业正在等待云中的 runbook 辅助角色变为可用。 当工作线程声明作业时，状态将更改为 "正在启动"。 最后，当 runbook 实际开始运行时，状态将变为 "正在运行"。

1. Runbook 作业完成后，"测试" 窗格将显示其输出。 在这种情况下，你会看到 "Hello World"。

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

你创建的 runbook 仍处于草稿模式。 必须先发布它，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是”。
1. 向左滚动以在 " **runbook** " 窗格中查看 runbook，并注意 "**创作状态**" 值已设置为 "已发布"。
1. 向右滚动查看 MyFirstRunbook-Workflow 的窗格。

   通过顶部的选项，你可以立即启动 runbook，计划将来的开始时间，或创建[webhook](automation-webhooks.md)以便可以通过 HTTP 调用启动 runbook。

1. 选择“启动”，并在出现提示时选择“是”启动 runbook。

   ![启动 Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 将为已创建的 runbook 作业打开作业窗格。 在这种情况下，请保持窗格处于打开状态，以便您可以查看该作业的进度。

1. 请注意，作业状态显示在 "**作业摘要**" 中。 此状态与测试 runbook 时看到的状态相匹配。

   ![作业摘要](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook 状态显示为 "已完成" 后，单击 "**输出**"。 此时会打开 "输出" 页，其中显示了 "Hello World" 消息。

   ![作业摘要](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 关闭“输出”页。

1. 单击“所有日志”打开 Runbook 作业的“流”窗格。 只应在输出流中看到 "Hello World"。 请注意，"流" 窗格可以显示 runbook 作业的其他流，如 "详细" 和 "错误流" （如果 runbook 向其写入）。

   ![作业摘要](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. 关闭 "流" 窗格和 "作业" 窗格以返回到 "MyFirstRunbook" 页。
1. 单击 "**资源**" 下的 "**作业**" 打开此 runbook 的 "作业" 页。 此页列出了 runbook 创建的所有作业。 你应该只会看到一个列出的作业，因为你只运行一次作业。

   ![作业](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 单击作业名称，打开在启动 runbook 时查看的相同的作业窗格。 使用此窗格查看为 runbook 创建的任何作业的详细信息。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步骤 5 - 添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 除非它使用订阅凭据进行身份验证，否则不能执行此操作。 身份验证使用**AzAccount** cmdlet。

>[!NOTE]
>对于 PowerShell runbook， **AzAccount**和**add-azurermaccount**是**AzAccount**的别名。 可以使用这些 cmdlet，或者将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新模块。

1. 导航到 "MyFirstRunbook" 页，然后单击 "**编辑**" 打开文本编辑器。
2. 删除**写入输出**行。
3. 将光标放在大括号之间的空白行上。
4. 键入或复制并粘贴以下代码，这些代码用于处理你的自动化运行方式帐户的身份验证。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. 单击“测试”窗格，以便测试 Runbook。
1. 单击“启动”以启动测试 。 完成后，应会看到类似于下面的输出，其中显示了帐户的基本信息。 此操作可确认凭据有效。

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步骤 6 – 添加用于启动虚拟机的代码

现在 Runbook 正在向 Azure 订阅进行身份验证，你可以管理资源。 让我们添加一个命令来启动虚拟机。 你可以在 Azure 订阅中选取任何 VM，现在你可以在 runbook 中硬编码该名称。 如果要跨多个订阅管理资源，则需要将*AzContext*参数与[AzContext](/powershell/module/az.accounts/get-azcontext) cmdlet 一起使用。

1. 输入对[new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) cmdlet 的调用，提供要启动的 VM 的名称和资源组名称，如下所示。 

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

1. 保存 Runbook，并单击“测试”窗格，以便可以测试 Runbook。
1. 单击“启动”以启动测试 。 完成后，检查 VM 是否已启动。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步骤 7 - 将输入参数添加到 Runbook

你的 runbook 当前会启动你在 runbook 中硬编码的虚拟机。 如果可以在启动 runbook 时指定 VM，则此方法将更有用。 向 runbook 添加输入参数以提供该功能。

1. 将*VMName*和*ResourceGroupName*的值添加到 runbook，并将关联的变量与**new-azvm** cmdlet 结合使用，如下所示。

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
4. 单击“发布”以发布 Runbook 的新版本 。
5. 停止已启动的虚拟机。
6. 单击“启动”以启动 Runbook **ResourceGroupName** 。 
7. 键入要启动的虚拟机的**VMNAME**和**RESOURCEGROUPNAME**的值。

   ![启动 Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Runbook 完成后，验证 VM 是否已启动。

## <a name="next-steps"></a>后续步骤

* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* 若要开始处理图形 runbook，请参阅[我的第一个图形 runbook](automation-first-runbook-graphical.md)。
* 若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要了解有关 runbook 类型及其优点和限制的详细信息，请参阅[Azure 自动化 runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅[Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
