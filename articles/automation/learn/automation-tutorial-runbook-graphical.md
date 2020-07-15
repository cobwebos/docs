---
title: 在 Azure 自动化中创建图形 Runbook
description: 本教程指导如何在 Azure 自动化中创建、测试和发布简单的图形 Runbook。
keywords: runbook, runbook 模板, runbook 自动化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 53031efa831f788fe0fe58146496b427f4cfb4db
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185528"
---
# <a name="tutorial-create-a-graphical-runbook"></a>教程：创建图形 Runbook

本教程指导在 Azure 自动化中创建 [图形 Runbook](../automation-runbook-types.md#graphical-runbooks)。 可以在 Azure 门户中使用图形编辑器创建和编辑图形 Runbook 与图形 PowerShell 工作流 Runbook。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建简单的图形 Runbook
> * 测试并发布 Runbook
> * 运行 Runbook 作业并跟踪其状态
> * 更新 Runbook 以使用 Runbook 参数和条件链接启动 Azure 虚拟机

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](../index.yml) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于需要停止并启动此虚拟机，因此它不应当是生产 VM。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个将输出文本 `Hello World` 的简单 Runbook。

1. 在 Azure 门户中，打开自动化帐户。 

    通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 大多数此类资产是自动包括在新的自动化帐户中的模块。 你还应该有与你的订阅关联的凭据资产。

2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。 

3. 选择“创建 Runbook”，创建一个新的 Runbook。

4. 将该 Runbook 命名为 **MyFirstRunbook-Graphical**。

5. 在本例中，你将创建一个[图形 Runbook](../automation-graphical-authoring-intro.md)。 选择“图形”作为“Runbook 类型”。 <br> ![新建 Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)<br>

6. 单击“创建”以创建 Runbook 并打开图形编辑器  。

## <a name="step-2---add-activities"></a>步骤 2 - 添加活动

编辑器左侧的库控件允许选择要添加到 Runbook 的活动。 你将添加 `Write-Output` cmdlet，以便从 Runbook 输出文本。

1. 在“库”控件中，单击搜索字段并键入 `write-output`。 下图显示了搜索结果。 <br> ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. 向下滚动到列表的底部。 右键单击“Write-Output”并选择“添加到画布”。  或者，可以单击 cmdlet 名称旁边的省略号图标 (...)，然后选择“添加到画布”。

3. 单击画布上的 **Write-Output** 活动。 此操作会打开可用于配置活动的“配置控件”页。

4. “标签”字段默认为该 cmdlet 的名称，但可将其更改为更易记的名称。 将其更改为 `Write Hello World to output`。

5. 单击“参数”为 cmdlet 参数提供值。

   某些 cmdlet 有多个参数集，你需要选择要使用的参数集。 在本例中，`Write-Output` 仅有一个参数集。

6. 选择 `InputObject` 参数。 这是可用于指定发送到输出流的文本的参数。

7. “数据源”下拉菜单中提供了可用于填充参数值的源。 在此菜单中选择“PowerShell 表达式”。 

   可以使用来自另一个活动、自动化资产或 PowerShell 表达式之类的源的输出。 在本例中，输出内容只是 `Hello World`。 可以使用 PowerShell 表达式并指定一个字符串。<br>

8. 在“表达式”字段中键入 `Hello World`，然后单击“确定”两次以返回到画布。 

9. 通过单击“保存” 保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3 - 测试 Runbook

在发布 Runbook 使其可在生产中使用之前，应对其进行测试，确保其能正常运行。 测试 Runbook 时将运行其“草稿”版本，你可以通过交互方式查看其输出。

1. 选择“测试窗格”以打开“测试”窗格。

2. 单击“启动”以启动测试  。 这应该是唯一的已启用选项。

3. 请注意，此时会创建一个 [Runbook 作业](../automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态一开始为 `Queued`，表明作业正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，其状态会变为 `Starting`。 最后，当 Runbook 开始实际运行时，其状态会变为 `Running`。

4. Runbook 作业完成后，“测试”窗格会显示其输出。 在本例中，你会看到 `Hello World`。

    ![Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

创建的 Runbook 仍处于“草稿”模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 选择“发布”以发布该 Runbook，并在出现提示时选择“是” 。

2. 在“Runbook”页中向左滚动以查看该 Runbook，请注意，“创作状态”值设置为“已发布” 。

3. 往回向右滚动查看“MyFirstRunbook-Graphical”页。

   顶部的选项现在可用于启动 Runbook、计划将来的启动时间，或创建一个 [webhook](../automation-webhooks.md) 以便通过 HTTP 调用来启动 Runbook。

4. 选择“启动”，并在出现提示时选择“是” 启动 runbook。

5. 此时会为已创建的 Runbook 作业打开一个“作业”窗格。 验证“作业状态”字段是否显示“已完成”。 

6. 单击“输出”打开“输出”页，在其中可以看到显示了 `Hello World`。

7. 关闭“输出”页。

8. 单击“所有日志”打开 Runbook 作业的“流”窗格。 在输出流中应该只会看到 `Hello World`。 

    请注意，“流”窗格可以显示 Runbook 作业的其他流，例如，详细流和错误流（如果 Runbook 向其写入内容）。

9. 关闭“流”窗格和“作业”窗格以返回到“MyFirstRunbook-Graphical”页。

10. 若要查看 Runbook 的所有作业，请在“资源”下选择“作业”。  “作业”页列出了你的 Runbook 创建的所有作业。 由于只将该作业运行了一次，应该只会看到一个列出的作业。

11. 单击作业名称，以打开你在启动 Runbook 时查看过的同一“作业”窗格。 使用此窗格可查看为 Runbook 创建的任何作业的详细信息。

## <a name="step-5---create-variable-assets"></a>步骤 5 - 创建变量资产

现已测试并发布了 Runbook，但到目前为止尚未执行任何有用的操作来管理 Azure 资源。 在配置用于身份验证的 Runbook 之前，必须创建一个变量来保存订阅 ID，设置一个活动来进行身份验证，然后引用该变量。 包括对订阅上下文的引用，这样就可以轻松地处理多个订阅。

1. 从导航窗格上的“订阅”选项中复制你的订阅 ID。

2. 在“自动化帐户”页的“共享资源”下选择“变量”。 

3. 选择“添加变量”。

4. 在“新建变量”页上提供的字段中进行以下设置。

    * **名称** -- 输入 `AzureSubscriptionId`。
    * **值** -- 输入你的订阅 ID。 
    * **类型** -- 保留选择的字符串。
    * **加密** -- 使用默认值。

5. 单击“创建”以创建该变量。

## <a name="step-6---add-authentication"></a>步骤 6 - 添加身份验证

现已创建一个变量来保存订阅 ID，接下来可以配置 Runbook，以使用订阅的运行方式凭据进行身份验证。 为此，可将 Azure 运行方式连接添加为资产。 此外，必须将 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) cmdlet 和 [Set-AzContext](/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet 添加到画布。

>[!NOTE]
>对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 请注意，这些别名不适用于图形 Runbook。 图形 Runbook 只能使用 `Connect-AzAccount` 本身。

1. 导航到 Runbook，在“MyFirstRunbook-Graphical”页上选择“编辑”。

2. 不再需要 `Write Hello World to output` 条目。 只需单击省略号图标，然后选择“删除”即可。

3. 在“库”控件中展开“资产”，然后展开“连接”。  选择“添加到画布”，将 `AzureRunAsConnection` 添加到画布。

4. 将 `AzureRunAsConnection` 重命名为 `Get Run As Connection`。

5. 在“库”控件的搜索字段中，键入 `Connect-AzAccount`。

6. 将 `Connect-AzAccount` 添加到画布。

7. 将鼠标悬停在 `Get Run As Connection` 上方，直到在该形状的底部显示一个圆圈。 单击该圆圈并将箭头拖到 `Connect-AzAccount`，以构成一个链接。 Runbook 首先运行“`Get Run As Connection`”，然后运行 `Connect-AzAccount`。<br> ![创建活动之间的链接](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. 在画布上选择 `Connect-AzAccount`。 在“配置”控件窗格的“标签”字段中键入“登录到 Azure”。 

9. 单击“参数”，此时会显示“活动参数配置”页。

10. `Connect-AzAccount` cmdlet 有多个参数集，需要选择其中一个，然后提供参数值。 单击“参数集”，然后选择“ServicePrincipalCertificateWithSubscriptionId” 。

11. 此参数集的参数会显示在“活动参数配置”页上。 单击“APPLICATIONID”。<br> ![添加 Azure 帐户参数](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. 在“参数值”页上进行以下设置，然后单击“确定”。

   * **数据源** -- 选择“活动输出”。
   * 数据源列表 -- 选择“获取自动化连接”。
   * **字段路径** -- 键入 `ApplicationId`。 需要指定字段路径的属性的名称，因为活动所输出的对象包含多个属性。

13. 单击“CERTIFICATETHUMBPRINT”，在“参数值”页上进行以下设置，然后单击“确定”。 

    * **数据源** -- 选择“活动输出”。
    * 数据源列表 -- 选择“获取自动化连接”。
    * **字段路径** -- 键入 `CertificateThumbprint`。

14. 单击“SERVICEPRINCIPAL”，在“参数值”页上为“数据源”字段选择“ConstantValue”，单击选项“True”；然后单击“确定”    。

15. 单击“TENANTID”，在“参数值”页上进行以下设置。 完成后，单击“确定”两次。

    * **数据源** -- 选择“活动输出”。 
    * 数据源列表 -- 选择“获取自动化连接”。
    * **字段路径** -- 键入 `TenantId`。 

16. 在“库”控件的搜索字段中，键入 `Set-AzContext`。

17. 将 `Set-AzContext` 添加到画布。

18. 在画布上选择 `Set-AzContext`。 在“配置”控件窗格的“标签”字段中输入 `Specify Subscription Id`。

19. 单击“参数”，此时会显示“活动参数配置”页。

20. `Set-AzContext` cmdlet 有多个参数集，需要选择其中一个，然后提供参数值。 单击“参数集”，然后选择“SubscriptionId”。 

21. 此参数集的参数会显示在“活动参数配置”页上。 单击“SubscriptionID”。

22. 在“参数值”页上，为“数据源”字段选择“变量资产”，然后从源列表中选择“AzureSubscriptionId”。   完成后，单击“确定”两次。

23. 将鼠标悬停在 `Login to Azure` 上方，直到在该形状的底部显示一个圆圈。 单击圆圈并将箭头拖到 `Specify Subscription Id`。 此时，Runbook 看起来应该如下所示。

    ![Runbook 身份验证配置](../media/automation-tutorial-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步骤 7 - 添加用于启动虚拟机的活动

现在，必须添加一个 `Start-AzVM` 活动来启动虚拟机。 可以选取 Azure 订阅中的任何 VM，但暂时请在 [Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet 中对 VM 名称进行硬编码。

1. 在“库”控件的搜索字段中，键入 `Start-Az`。

2. 将 `Start-AzVM` 添加到画布，然后将其单击拖放到 `Specify Subscription Id` 下面。

3. 将鼠标悬停在 `Specify Subscription Id` 上方，直到在该形状的底部显示一个圆圈。 单击圆圈并将箭头拖到 `Start-AzVM`。

4. 选择 `Start-AzVM`。 单击“参数”，然后单击“参数集”查看活动集。 

5. 选择“ResourceGroupNameParameterSetName”作为参数集。 “ResourceGroupName”和“Name”字段旁边出现了感叹号，表示它们是必需的参数。  请注意，这两个字段都需要字符串值。

6. 选择“名称”。 为“数据源”字段选择“PowerShell 表达式”。  对于用于启动此 Runbook 的 VM，请键入带双引号的计算机名称。 单击“确定”。

7. 选择“ResourceGroupName”。 对“数据源”字段使用值“PowerShell 表达式”，并键入带双引号的资源组名称 。 单击“确定”。

8. 单击“测试”窗格，以便测试 Runbook。

9. 单击“启动”，开始测试。 完成后，请确保该 VM 已启动。 此时，Runbook 看起来应该如下所示。

    ![Runbook 身份验证配置](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步骤 8 - 添加其他输入参数

Runbook 当前会启动资源组中为 `Start-AzVM` cmdlet 指定的 VM。 如果在 Runbook 启动时指定了 VM 名称和资源组，则该 Runbook 会更有用。 让我们向 Runbook 中添加输入参数，以提供该功能。

1. 在“MyFirstRunbook-Graphical”页上单击“编辑”，打开图形编辑器。

2. 依次选择“输入和输出”和“添加输入”，打开“Runbook 输入参数”窗格 。

3. 在提供的字段中进行以下设置，然后单击“确定”。
   * **名称** -- 指定 `VMName`。
   * **类型** -- 保留字符串设置。
   * **必需** -- 将值更改为“是”。

4. 创建名为 `ResourceGroupName` 的另一个必需输入参数，然后单击“确定”以关闭“输入和输出”窗格。<br> ![Runbook 输入参数](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. 选择 `Start-AzVM` 活动，然后单击“参数”。

6. 将“名称”对应的“数据源”字段更改为“Runbook 输入”。   然后选择“VMName”。

7. 将“ResourceGroupName”对应的“数据源”字段更改为“Runbook 输入”，然后选择“ResourceGroupName”   。<br> ![Start-AzVM 参数](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。

9. 关闭“测试”窗格。

10. 单击“发布”以发布 Runbook 的新版本  。

11. 停止前面启动的 VM。

12. 单击“启动”以启动 Runbook **ResourceGroupName** 。 键入要启动的 VM 的 `VMName` 和 `ResourceGroupName` 值。

13. Runbook 完成后，请确保该 VM 已启动。

## <a name="step-9---create-a-conditional-link"></a>步骤 9 - 创建条件链接

现在可以修改该 Runbook，使之仅在 VM 尚未启动的情况下尝试启动该 VM。 为此，可以添加一个 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet 来检索 VM 的实例级状态。 然后，可以添加名为 `Get Status` 的 PowerShell 工作流代码模块，并使用 PowerShell 代码片段来确定 VM 状态是“正在运行”还是“已停止”。 仅当当前运行状态为“已停止”时，`Get Status` 模块中的条件链接才运行 `Start-AzVM`。 在此过程的最后，Runbook 将使用 `Write-Output` cmdlet 输出一条消息，告知 VM 是否已成功启动。

1. 在图形编辑器中打开“MyFirstRunbook-Graphical”。

2. 单击“`Specify Subscription Id`”和“`Start-AzVM`”之间的链接，然后按“删除”将其删除。

3. 在“库”控件的搜索字段中，键入 `Get-Az`。

4. 将 `Get-AzVM` 添加到画布。

5. 选择 `Get-AzVM`，然后单击“参数集”以查看 cmdlet 集。 

6. **GetVirtualMachineInResourceGroupNameParamSet** 参数集。 “ResourceGroupName”和“Name”字段旁边出现了感叹号，表示它们指定了必需的参数。  请注意，这两个字段都需要字符串值。

7. 在“Name”的“数据源”下选择“Runbook 输入”，然后选择“VMName”   。 单击“确定”。

8. 在“ResourceGroupName”的“数据源”下选择“Runbook 输入”，然后选择“ResourceGroupName”   。 单击“确定”。

9. 在“Status”的“数据源”下选择“常量值”，然后选择“True”   。 单击“确定”。

10. 创建从 `Specify Subscription Id` 到 `Get-AzVM` 的链接。

11. 在“库”控件中，展开“Runbook 控件”，然后将“Code”添加到画布 。  

12. 创建从 `Get-AzVM` 到 `Code` 的链接。  

13. 单击 `Code`，然后在“配置”窗格中将标签更改为“获取状态”。

14. 选择 `Code`，此时会显示“代码编辑器”页。  

15. 将以下代码片段粘贴到编辑器页中。

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

16. 创建从 `Get Status` 到 `Start-AzVM` 的链接。

    ![使用代码模块的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. 选择该链接，然后在“配置”窗格中将“应用条件”更改为“是” 。 请注意，该链接将变为虚线，表示仅在条件解析为 True 时才会运行目标活动。  

18. 对于“条件表达式”，请键入 `$ActivityOutput['Get Status'] -eq "Stopped"`。 现在，仅当 VM 已停止时，`Start-AzVM` 才会运行。

19. 在库控件中，展开 **Cmdlet** 节点，并展开 **Microsoft.PowerShell.Utility**。

20. 将 `Write-Output` 添加到画布两次。

21. 对于第一个 `Write-Output` 控件，请单击“参数”并将“标签”值更改为“通知 VM 已启动”  。

22. 对于“InputObject”，请将“数据源”更改为“PowerShell 表达式”，并键入表达式 `$VMName successfully started.`  。

23. 在第二个 `Write-Output` 控件中，单击“参数”并将“标签”值更改为“通知 VM 启动失败”。  

24. 对于“InputObject”，请将“数据源”更改为“PowerShell 表达式”，并键入表达式 `$VMName could not start`  。

25. 创建从 `Start-AzVM` 到 `Notify VM Started` 和 `Notify VM Start Failed` 的链接。

26. 选择“`Notify VM Started`”的链接并将“应用条件”更改为 true。

27. 对于“条件表达式”，请键入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 现在，只有在 VM 已成功启动的情况下，此 `Write-Output` 控件才会运行。

28. 选择“`Notify VM Start Failed`”的链接并将“应用条件”更改为 true。

29. 对于“条件表达式”字段，请键入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 现在，只有在 VM 未成功启动的情况下，此 `Write-Output` 控件才会运行。 Runbook 看起来应如下图所示。

    ![使用 Write-Output 的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. 保存 Runbook 并打开“测试”窗格。

31. 在 VM 已停止的情况下启动该 Runbook，此时该计算机应会启动。

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形创作，请参阅[在 Azure 自动化中创作图形](../automation-graphical-authoring-intro.md)。
* 若要开始使用 PowerShell Runbook，请参阅[创建 PowerShell Runbook](automation-tutorial-runbook-textual-powershell.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅[创建 PowerShell 工作流 Runbook](automation-tutorial-runbook-textual.md)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
