---
title: 我在 Azure 自动化中的第一个 PowerShell Runbook
description: 本教程指导创建、测试和发布一个简单的 PowerShell Runbook。
keywords: azure powershell, powershell 脚本教程, powershell 自动化
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0df2680a9f17fc0af950b0ce744a655348b4cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406061"
---
# <a name="my-first-powershell-runbook"></a>我的第一个 PowerShell Runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教程介绍了如何在 Azure 自动化中创建 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 。 从可以测试和发布的简单 Runbook 开始，同时了解如何跟踪 Runbook 作业的状态。 然后修改 Runbook 以实际管理 Azure 资源，在这种情况下启动 Azure 虚拟机。 通过添加 Runbook 参数，完成本教程以使 Runbook 更加健壮。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果您还没有，您可以[激活您的 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-quickstart-create-account.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于您停止并启动此计算机，因此它不应是生产 VM。
* 如有必要，请根据使用的 cmdlet[导入 Azure 模块](shared-resources/modules.md)或[更新模块](automation-update-azure-modules.md)。

## <a name="differences-from-powershell-workflow-runbooks"></a>与 PowerShell 工作流运行簿的差异

PowerShell 运行簿与 PowerShell 工作流运行簿具有相同的生命周期、功能和管理。 但是，存在一些差异和限制。

| 特征  | 电源壳运行簿 | 电源外壳工作流运行簿 |
| ------ | ----- | ----- |
| Speed | 运行速度快，因为它们不使用编译步骤。 | 运行得更慢。 |
| 检查点 | 不支持检查点。 PowerShell 运行簿只能从一开始就恢复操作。 | 使用检查点，允许工作簿从任何点恢复操作。 |
| 命令执行 | 仅支持串行执行。 | 支持串行和并行执行。|
| Runspace | 单个运行空间运行脚本中的所有内容。 | 单独的运行空间可用于活动、命令或脚本块。 |

除了这些差异之外，PowerShell Runbook 与 PowerShell 工作流 Runbook 有一些[语法差异](https://technet.microsoft.com/magazine/dn151046.aspx)。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个简单的运行手册，输出文本`Hello World`。

1. 在 Azure 门户中，打开自动化帐户。
2. 在 **"流程自动化**"下选择**Runbook**以打开 Runbook 列表。
3. 通过选择 **"创建运行簿"创建新的 Runbook。**
4. 将该 Runbook 命名为 **MyFirstRunbook-PowerShell**。
5. 在这种情况下，您将创建一个[PowerShell 运行簿](automation-runbook-types.md#powershell-runbooks)。 为**Runbook 类型**选择**PowerShell。**
6. 单击“创建”以创建 Runbook 并打开文本编辑器 **** 。

## <a name="step-2---add-code-to-the-runbook"></a>步骤 2 - 将代码添加到 Runbook

可以直接将代码键入 Runbook 中，或者通过“库”控件选择 cmdlet、Runbook 和资产，并使用任何相关的参数将它们添加到 Runbook。 在本教程中，您将直接在 Runbook 中键入代码。

1. 您的 Runbook 当前为空。 键入`Write-Output "Hello World"`脚本正文。

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. 通过单击“保存” **** 保存 Runbook。

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a>步骤 3 - 测试运行簿

在发布 Runbook 使其在生产中可用之前，应对其进行测试以确保其正常工作。 测试 Runbook 会运行其草稿版本，并允许您以交互方式查看其输出。

1. 单击“测试窗格” **** 打开测试窗格。
2. 单击“启动”以启动测试 **** 。 这应该是唯一的已启用选项。
3. 请注意，将创建[Runbook 作业](automation-runbook-execution.md)，其状态将显示在窗格中。

   作业状态以"排队"开头，指示作业正在等待云中的 Runbook 工作人员变为可用。 当工作人员声明作业时，状态更改为"开始"。 最后，当 Runbook 实际开始运行时，状态变为"运行"。

4. 运行簿作业完成后，"测试"窗格将显示其输出。 在这种情况下，您将看到`Hello World`。

   ![测试窗格输出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

您创建的 Runbook 仍处于草稿模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是”********。
1. 向左滚动以查看 Runbook 页上的 Runbook，并注意 **"创作状态"** 值设置为 **"已发布**"。
1. 向后滚动到右侧以查看**MyFirstRunbook-PowerShell**的页面。
   
   顶部的选项允许您立即启动 Runbook、安排将来的开始时间或创建[Webhook，](automation-webhooks.md)以便通过 HTTP 调用启动 Runbook。
1. 选择“启动”，并在出现提示时选择“是”******** 启动 runbook。 
1. 为已创建的 Runbook 作业打开作业窗格。 尽管可以关闭此窗格，但请立即将其保持打开状态，以便可以监视作业的进度。 作业状态显示在**作业摘要**中，并且可能的状态如测试 Runbook 所述。

   ![作业摘要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook 状态显示"已完成"后，单击"**输出"** 以打开"输出"页`Hello World`，您可以在其中显示显示。

   ![作业输出](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 关闭“输出”页。
1. 单击“所有日志”打开 Runbook 作业的“流”窗格****。 您应该只在输出`Hello World`流中看到。

    请注意，如果 Runbook 写入 Runbook，则"流"窗格可以显示 Runbook 作业的其他流，如"详细"和"错误"流。

   ![所有日志](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. 关闭"流"窗格和作业窗格以返回到"MyFirstRunbook-PowerShell"页。
1. 在 **"详细信息**"下，单击 **"作业**"以打开此 Runbook 的作业页面。 此页列出 Runbook 创建的所有作业。 您应该只看到列出的一个作业，因为您只运行该作业一次。

   ![作业列表](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 单击作业名称可打开启动 Runbook 时查看的相同作业窗格。 使用此窗格可以查看为 Runbook 创建的任何作业的详细信息。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步骤 5 - 添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 为此，Runbook 必须能够使用创建自动化帐户时自动创建的"运行为"帐户进行身份验证。

如下例所示，"运行为"连接与[连接-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 进行。 如果要跨多个订阅管理资源，则需要将`AzContext`参数与[Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)一起使用。

> [!NOTE]
> 对于 PowerShell 运行`Add-AzAccount`簿`Add-AzureRMAccount`，并且是`Connect-AzAccount`的别名。 您可以使用这些 cmdlet，也可以将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使您刚刚创建了新的自动化帐户，您也可能需要更新模块。

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
1. 通过单击 MyFirstRunbook-PowerShell 页上的“编辑”**** 打开文本编辑器。
1. 您不再需要这`Write-Output`条线了。 继续删除它。
1. 键入或复制并粘贴以下代码，这些代码处理具有"自动运行为帐户"的身份验证。

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

1. 单击 **"测试"窗格**，以便可以测试 Runbook。
1. 单击“启动”以启动测试 **** 。 完成后，您应该会看到类似于以下内容的输出，显示帐户中的基本信息。 此输出确认运行方式帐户有效。

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步骤 6 – 添加用于启动虚拟机的代码

现在 Runbook 正在向 Azure 订阅进行身份验证，你可以管理资源。 让我们添加一个命令来启动虚拟机。 您可以选择 Azure 订阅中的任何虚拟机，并且只需在 Runbook 中立即输入名称的硬编码。

1. 添加到 runbook 脚本，添加[启动 AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 以启动虚拟机。 如下所示，cmdlet 启动具有名称`VMName`且资源组名为 的`ResourceGroupName`虚拟机。

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

1. 保存 Runbook，并单击“测试”窗格，以便可以测试 Runbook****。
1. 单击 **"开始"** 以开始测试。 完成后，请确保虚拟机已启动。

## <a name="step-7---add-an-input-parameter"></a>步骤 7 - 添加输入参数

Runbook 当前启动在 Runbook 中硬编码的虚拟机。 如果在启动 Runbook 时指定虚拟机，则 Runbook 更有用。 让我们向 Runbook 添加输入参数以提供该功能。

1. 在文本编辑器中`Start-AzVM`，修改 cmdlet 以使用参数`VMName`和`ResourceGroupName`的变量。 

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
1. 单击“发布”以发布 Runbook 的新版本 **** 。
1. 停止以前启动的虚拟机。
1. 单击“启动”以启动 Runbook **ResourceGroupName** 。 
1. 键入要启动的虚拟机的**VMNAME**和**RESOURCEGROUPNAME**的值，然后单击"**确定**"。<br><br> ![传递参数](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. 运行簿完成后，请确保虚拟机已启动。

## <a name="next-steps"></a>后续步骤

* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅[PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
* 要开始使用图形运行簿，请参阅[我的第一个图形运行簿](automation-first-runbook-graphical.md)。
* 要开始使用 PowerShell 工作流运行簿，请参阅[我的第一个 PowerShell 工作流运行簿](automation-first-runbook-textual.md)。
* 要了解有关 Runbook 类型及其优点和限制的更多信息，请参阅[Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅[Azure 自动化 中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
