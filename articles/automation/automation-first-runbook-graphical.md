---
title: 我在 Azure 自动化中的第一个图形 Runbook
description: 本教程指导完成创建、 测试和发布一个简单图形 Runbook。
keywords: runbook, runbook 模板, runbook 自动化, azure runbook
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d47a8a3d8343aaa17346cd63c055e8687f25f812
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="my-first-graphical-runbook"></a>我的第一个图形 Runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教程指导在 Azure 自动化中创建 [图形 Runbook](automation-runbook-types.md#graphical-runbooks)。 从一个简单的 Runbook 开始，目的是测试和发布该 Runbook，同时了解如何跟踪 Runbook 作业的状态。 然后，通过修改 Runbook 来实际管理 Azure 资源，这种情况下会启动 Azure 虚拟机。 然后添加 Runbook 参数和条件链接，使该 Runbook 更稳健，从而完成本教程。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 需停止和启动该虚拟机，因此其不应为生产用 VM。

## <a name="create-runbook"></a>创建 Runbook

首先创建一个输出文本 *Hello World* 的简单 Runbook。

1. 在 Azure 门户中，打开自动化帐户。

   通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 大多数此类资产都是自动包括在新的自动化帐户中的模块。 还应具有在 [“先决条件”](#prerequisites)中提到的凭证资产。

2. 在“进程管理”下选择“Runbook”，打开 Runbook 的列表。
3. 通过选择“+ 添加 Runbook”，并单击“创建新 Runbook”，创建一个新 Runbook。
4. 将该 Runbook 命名为 *MyFirstRunbook-Graphical*。
5. 在本示例中，需要创建[图形 Runbook](automation-graphical-authoring-intro.md)，因此应选择“图形”作为“Runbook 类型”。<br> ![新建 Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. 单击“创建”以创建 Runbook 并打开图形编辑器  。

## <a name="add-activities"></a>添加活动

编辑器左侧的库控件允许选择要添加到 Runbook 的活动。 需要添加 **Write-Output** cmdlet 以从 Runbook 输出文本。

1. 在“库”控件中，单击搜索文本框，并键入 **Write-Output**。 搜索结果显示在下图中： <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 向下滚动到列表的底部。 可以先右键单击“Write-Output”，然后选择“添加到画布”，也可以先单击 cmdlet 旁边的省略号，然后选择“添加到画布”。
1. 单击画布上的 **Write-Output** 活动。 此操作会打开可用于配置活动的“配置控件”页。
1. “标签”默认为该 cmdlet 的名称，但可将其更改为更友好的名称。 将其更改为 *Write Hello World to output*。
1. 单击“参数”为 cmdlet 参数提供值。

   某些 cmdlet 有多个参数集，并且需要选择将使用的参数集。 在本示例中，**Write-Output** 只有一个参数集，因此无需选择一个。

1. 选择 **InputObject** 参数。 这是一个可以在其中指定发送到输出流的文本的参数。
1. 在“数据源”下拉列表中，选择“PowerShell 表达式”。 “数据源”下拉列表中提供了用于填充参数值的不同的源  。

   可以使用来自诸如另一个活动、自动化资产或 PowerShell 表达式等类型源的输出。 在这种情况下，输出就是 *Hello World*。 可以使用 PowerShell 表达式并指定一个字符串。<br>

1. 在“表达式”框中，键入“Hello World”，并单击“确定”两次返回到画布。
1. 通过单击“保存” 保存 Runbook。

## <a name="test-the-runbook"></a>测试 Runbook

在发布 Runbook 使其可在生产中使用之前，需对其进行测试以确保其能正常工作。 测试 Runbook 时，可以运行其“草稿”版本并以交互方式查看其输出。

1. 选择“测试窗格”打开“测试”页。
1. 单击“启动”以启动测试  。 这应该是唯一的已启用选项。
1. 会创建一个 [“Runbook 作业”](automation-runbook-execution.md) 并且在窗格中显示其状态 。

   作业状态一开始为“排队”，表示正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。

1. Runbook 作业完成后，会显示其输出。 在这种情况下，会看到 *Hello World*。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 关闭“测试”页返回到画布。

## <a name="publish-and-start-the-runbook"></a>发布和启动 Runbook

创建的 Runbook 仍处于“草稿”模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 选择“发布”以发布该 Runbook，并在出现提示时选择“是”。
1. 如果向左滚动以在“Runbook”页中查看该 Runbook，它会显示“已发布”的“创作状态”。
1. 往回向右滚动查看“MyFirstRunbook-Graphical”页。

   顶部的选项允许我们启动 Runbook，计划其在将来的某个时刻启动，或创建 [webhook](automation-webhooks.md) 以使其可以通过 HTTP 调用启动。

1. 选择“启动”，并在出现提示时选择“是”启动 runbook。
1. 此时将打开已创建的 runbook 作业的作业页。 验证“作业状态”是否显示“已完成”。
1. 一旦此 Runbook 状态显示“已完成”，单击“输出”。 此时会打开“输出”页，可以在窗格中看到 *Hello World*。
1. 关闭“输出”页。
1. 单击“所有日志”打开 Runbook 作业的“流”页。 应该只会在输出流中看到 *Hello World*，但此窗格也可以显示 Runbook 作业的其他流，例如，“详细”和“错误”（如果 Runbook 向其写入）。
1. 关闭“所有日志”页和“作业”页，返回到“MyFirstRunbook-Graphical”页。
1. 若要查看 Runbook 的所有作业，请关闭“作业”页，并在“资源”下选择“作业”。 这会列出此 Runbook 创建的所有作业。 由于只运行该作业一次，应该只会看到一个列出的作业。
1. 可以单击此作业，打开在启动 Runbook 时查看过的“作业”窗格。 这样便可以回溯并查看为特定 Runbook 创建的任何作业的详细信息。

## <a name="create-variable-assets"></a>创建变量资产

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 配置要进行身份验证的 Runbook 之前，先设置下面步骤 6 中要进行身份验证的活动，然后创建一个变量以保存订阅 ID 并引用它。 包括对订阅上下文的引用，可轻松地在多个订阅之间切换。 在继续操作之前，从“导航”窗格的“订阅”选项中复制订阅 ID。

1. 在“自动化帐户”页的“共享资源”下选择“变量”。
1. 选择“添加变量”。
1. 在“新建变量”页中，在“名称”框中输入“AzureSubscriptionId”，在“值”框中输入订阅 ID。 对于“类型”，请保留“字符串”；对于“加密”，请保留默认值。
1. 单击“创建”以创建该变量。

## <a name="add-authentication"></a>添加身份验证

现在已有一个变量来保存订阅 ID，那么就可以配置 Runbook 以使用[先决条件](#prerequisites)中引用的运行方式凭据进行身份验证。 为此，可将 Azure 运行方式连接资产和 Connect-AzureRmAccount cmdlet 添加到画布中。

1. 导航回到 Runbook，在“MyFirstRunbook-Graphical”页上选择“编辑”。
1. 由于不再需要**将 Hello World 写入到输出**，因此请单击省略号 (...)，然后选择“删除”。
1. 在“库”控件中展开“资产”、“连接”，然后通过选择“添加到画布”将“AzureRunAsConnection”添加到画布。
1. 在“库”控件的搜索文本框中，键入“Connect-AzureRmAccount”。
1. 将 Connect-AzureRmAccount 添加到画布。
1. 将鼠标悬停在“获取运行方式连接”上方，直到在该形状的底部显示一个圆圈。 单击该圆圈并将箭头拖至 Connect-AzureRmAccount。 创建的箭头是*链接*。 该 Runbook 会首先运行“获取运行方式连接”，然后运行 Connect-AzureRmAccount。<br> ![创建活动之间的链接](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 在画布上选择“Connect-AzureRmAccount”，并在“配置控件”窗格的“标签”文本框中键入“登录到 Azure”。
1. 单击“参数”，此时会显示“活动参数配置”页。
1. Connect-AzureRmAccount 有多个参数集，因此需要选择其中一个，才能提供参数值。 单击“参数集”，并选择“ServicePrincipalCertificate”参数集。
1. 选择参数集后，这些参数会显示在“活动参数配置”页中。 单击“APPLICATIONID”。<br> ![添加 Azure RM 帐户参数](media/automation-first-runbook-graphical/Connect-AzureRmAccount-params.png)
1. 在“参数值”页中，选择“活动输出”作为“数据源”，并从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“ApplicationId”，单击“确定”。 需指定“字段路径”的属性的名称，因为活动所输出的对象包含多个属性。
1. 单击“CERTIFICATETHUMBPRINT”，并在“参数值”页中，选择“活动输出”作为“数据源”。 从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“CertificateThumbprint”，并单击“确定”。
1. 单击“SERVICEPRINCIPAL”，在“参数值”页中选择“ConstantValue”作为“数据源”，单击选项“True”，并单击“确定”。
1. 单击“TENANTID”，并在“参数值”页中，选择“活动输出”作为“数据源”。 从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“TenantId”，并单击“确定”两次。
1. 在“库”控件的搜索文本框中，键入“Set-AzureRmContext”。
1. 将 **Set-AzureRmContext** 添加到画布。
1. 在画布上选择“Set-AzureRmContext”，并在“配置控件”窗格的“标签”文本框中键入“指定订阅 ID”。
1. 单击“参数”，此时会显示“活动参数配置”页。
1. **Set-AzureRmContext** 有多个参数集，因此需要选择其中一个，才能提供参数值。 单击“参数集”，并选择“SubscriptionId”参数集。
1. 选择参数集后，这些参数会显示在“活动参数配置”页中。 单击 **SubscriptionID**
1. 在“参数值”页中，选择“变量资产”作为“数据源”，从列表中选择“AzureSubscriptionId”，并单击“确定”两次。
1. 将鼠标悬停在“登录到 Azure”上方，直到在该形状的底部显示一个圆圈。 **Specify Subscription Id**。

> [!IMPORTANT]
> Connect-AzureRmAccount 现在是 Connect-AzureRMAccount 的别名。 搜索库项时，如果未看到 Connect-AzureRMAccount，可以使用 Connect-AzureRmAccount，或更新自动化帐户中的模块。

此时，Runbook 看起来将如下所示： <br>![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>添加用于启动 VM 的活动

现在将添加 **Start-AzureRmVM** 活动来启动虚拟机。 可以在 Azure 订阅中选取任何虚拟机。现在，请将该名称硬编码到该 cmdlet 中。

1. 在“库”控件的搜索文本框中，键入“Start-AzureRm”。
2. 将 **Start-AzureRmVM** 添加到画布，然后单击并将其拖放到“指定订阅 ID”下面。
1. 将鼠标悬停在 **Specify Subscription Id** 上方，直到在该形状的底部显示一个圆圈。 单击该圆圈并将箭头拖至 **Start-AzureRmVM**。
1. **Start-AzureRmVM**。 单击“参数”，并单击“参数集”查看 **Start-AzureRmVM** 的参数集。 **ResourceGroupNameParameterSetName** 参数集。 **ResourceGroupName** 和“名称”旁边有感叹号。 这表示它们是必需的参数。 另请注意，两者都需要字符串值。
1. 选择“名称”。 选择“PowerShell 表达式”作为“数据源”，然后键入带双引号的虚拟机名称（一开始用于此 Runbook）。 单击“确定”。
1. 选择“ResourceGroupName”。 使用“PowerShell 表达式”作为“数据源”，并键入带双引号的资源组名称。 单击“确定”。
1. 单击“测试”窗格，以便测试 Runbook。
1. 单击“启动”以启动测试  。 一旦测试完成后，检查已启动的虚拟机。

此时，Runbook 看起来将如下所示： <br>![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>添加其他输入参数

Runbook 目前在资源组中启动虚拟机，该虚拟机已在 **Start-AzureRmVM** cmdlet 中指定。 如果在 Runbook 启动时指定这二者，则该 Runbook 会更有用。 现在请将输入参数添加到 Runbook，以提供该功能。

1. 在“MyFirstRunbook-Graphical”窗格上单击“编辑”，打开图形编辑器。
1. 依次选择“输入和输出”和“添加输入”，打开“Runbook 输入参数”窗格。
1. 指定 *VMName* 作为**名称**。 保留“字符串”作为“类型”，但将“必需”更改为“是”。 单击“确定”。
1. 创建第二个名为 *ResourceGroupName* 的必需输入参数，并单击“确定”关闭“输入和输出”窗格。<br> ![Runbook 输入参数](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. 选择 **Start-AzureRmVM** 活动，并单击“参数”。
1. 将“名称”的“数据源”更改为“Runbook 输入”，然后选择 **VMName**。
1. 将“ResourceGroupName”的“数据源”更改为“Runbook 输入”，然后选择“ResourceGroupName”。<br> ![Start-AzureVM 参数](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。
1. 关闭“测试”窗格。
1. 单击“发布”以发布 Runbook 的新版本  。
1. 停止在上一步中启动的虚拟机。
1. 单击“启动”以启动 Runbook **ResourceGroupName** 。 键入要启动的虚拟机的 **VMName** 和 **ResourceGroupName**。
1. 一旦 Runbook 完成后，检查已启动的虚拟机。

## <a name="create-a-conditional-link"></a>创建条件链接

现在将修改该 Runbook，使之仅在虚拟机尚未启动的情况下尝试启动虚拟机。 为此，需要将 **Get-AzureRmVM** cmdlet 添加到 Runbook，以便获取虚拟机的实例级状态。 然后，需要添加名为“获取状态”的 PowerShell 工作流代码模块，并使用 PowerShell 代码片段来确定虚拟机状态是“正在运行”还是“已停止”。 如果当前的运行状态为“已停止”，则“获取状态”模块中的条件链接只运行 **Start-AzureRmVM**。 最后，使用 PowerShell Write-Output cmdlet 输出一条消息，告知 VM 是否已启动成功。

1. 在图形编辑器中打开“MyFirstRunbook-Graphical”。
1. 单击“指定订阅 ID” 和 **Start-AzureRmVM** 之间的链接，然后按“删除”键将其删除。
1. 在“库”控件的搜索文本框中，键入“Get-AzureRm”。
1. 将 **Get-AzureRmVM** 添加到画布。
1. 选择“Get-AzureRmVM”，并选择“参数集”以查看 **Get-AzureRmVM** 的参数集。 **GetVirtualMachineInResourceGroupNameParamSet** 参数集。 **ResourceGroupName** 和“名称”旁边有感叹号。 这表示它们是必需的参数。 另请注意，两者都需要字符串值。
1. 在“名称”的“数据源”下，选择“Runbook 输入”，并选择 **VMName**。 单击“确定”。
1. 将“ResourceGroupName”的“数据源”下，选择“Runbook 输入”，然后选择“ResourceGroupName”。 单击“确定”。
1. 在“状态”的“数据源”下，选择“常数值”，并单击“True”。 单击“确定”。
1. 创建从“指定订阅 ID”到 **Get-AzureRmVM** 的链接。
1. 在“库”控件中，展开“Runbook 控件”，然后将“Code”添加到画布。  
1. 创建从“Get-AzureRmVM”到“Code”的链接。  
1. 单击“Code”，然后在“配置”窗格中将标签更改为“获取状态”。
1. 选择 **Code** 参数，此时会显示“代码编辑器”页。  
1. 在代码编辑器中，粘贴以下代码片段：

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. 创建从“获取状态”到“Start-AzureRmVM”的链接。<br> ![使用代码模块的 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 选择该链接，然后在“配置”窗格中，将“应用条件”更改为“是”。 请注意，该链接将变为虚线，指示仅在条件解析为 True 时才会运行目标活动。  
1. 对于“条件表达式”，请键入 *$ActivityOutput['Get Status'] -eq "Stopped"*。 **Start-AzureRmVM** 现在仅在虚拟机停止的情况下才会运行。
1. 在库控件中，展开 **Cmdlet** 节点，并展开 **Microsoft.PowerShell.Utility**。
1. 将 **Write-Output** 添加到画布两次。
1. 在第一个 **Write-Output** 控件中，单击“参数”并将“标签”值更改为“通知 VM 已启动”。
1. 对于 **InputObject**，将“数据源”更改为“PowerShell 表达式”，并键入表达式“$VMName successfully started.”。
1. 在第二个 **Write-Output** 控件中，单击“参数”并将“标签”值更改为“通知 VM 启动失败”
1. 对于 **InputObject**，将“数据源”更改为“PowerShell 表达式”，并键入表达式“$VMName could not start.”。
1. 创建从 **Start-AzureRmVM** 到“通知 VM 已启动”和“通知 VM 启动失败”的链接。
1. 选择到“通知 VM 已启动”的链接，并将”应用条件“更改为 **True**。
1. 对于“ **条件表达式**”，键入 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*。 此 Write-Output 控件现在将仅在虚拟机已成功启动的情况下运行。
1. 选择到“通知 VM 启动失败”的链接，并将”应用条件“更改为 **True**。
1. 对于“ **条件表达式**”，键入 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*。 此 Write-Output 控件现在将仅在虚拟机未成功启动的情况下运行。 Runbook 应看起来如下图所示： <br> ![使用 Write-Output 的 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. 保存 Runbook 并打开“测试”窗格。
1. 如果在虚拟机停止时启动 Runbook，那么虚拟机将启动。

## <a name="next-steps"></a>后续步骤

* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* 若要开始使用 PowerShell Runbook，请参阅 [我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)

