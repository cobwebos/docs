<!-- not suitable for Mooncake -->

<properties
    pageTitle="我在 Azure 自动化中的第一个图形 Runbook | Azure"
    description="本教程将指导你完成创建、 测试和发布一个简单图形 Runbook。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="runbook, runbook 模板, runbook 自动化, azure runbook"/>
<tags 
	ms.service="automation"
	ms.date="05/31/2016"
	wacn.date=""/>

# 我的第一个图形 Runbook

> [AZURE.SELECTOR] - [Graphical](/documentation/articles/automation-first-runbook-graphical/) - [PowerShell](/documentation/articles/automation-first-runbook-textual-PowerShell/) - [PowerShell Workflow](/documentation/articles/automation-first-runbook-textual/)

本教程将指导你在 Azure 自动化中创建[图形 Runbook](/documentation/articles/automation-runbook-types/#graphical-runbooks)。我们将从一个简单的 Runbook 开始，我们将测试和发布该 Runbook，同时介绍如何跟踪 Runbook 作业的状态。然后我们将通过修改 Runbook 来实际管理 Azure 资源，这种情况下将启动 Azure 虚拟机。然后我们将通过添加 Runbook 参数和条件链接来使该 Runbook 更稳健。

## 先决条件

为了完成本教程，你需要满足以下条件。

-	Azure 订阅创建新存储帐户。如果你还没有帐户，则可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或<a href="/pricing/free-account/" target="_blank">[注册免费帐户](https://azure.microsoft.com/free/)。
-	[Azure 运行方式帐户](/documentation/articles/automation-sec-configure-azure-runas-account/)，用来保存 Runbook 以及向 Azure 资源进行身份验证。此帐户必须有权启动和停止虚拟机。
-	Azure 虚拟机。我们将停止并启动该计算机，因此其不应为生产用计算机。


## 步骤 1 - 创建新的 Runbook

我们首先创建一个输出文本 *Hello World* 的简单 Runbook 。

1.	在 Azure 门户中，打开你的自动化帐户。通过自动化帐户页面可快速查看此帐户中的资源。你应该已拥有某些资产。大多数资产都是自动包括在新的自动化帐户中的模块。你还应具有在“先决条件”中提到的凭证资产[](#prerequisites)。
2.	单击 **Runbook** 磁贴打开 Runbook 的列表。<br>![Runbook 控制](./media/automation-first-runbook-graphical/runbooks-control.png)
3.	通过单击“添加 Runbook”按钮创建一个新 Runbook，然后“创建新 Runbook”。
4.	将该 Runbook 命名为 *MyFirstRunbook-Graphical*。
5.	在这种情况下，我们要创建“图形 Runbook”，因此应选择“图形”作为“Runbook 类型”。[](/documentation/articles/automation-graphical-authoring-intro/)<br>![新建 Runbook](./media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.	单击“创建”以创建 Runbook 并打开图形编辑器。

## 步骤 2 - 向 Runbook 添加活动

编辑器左侧的库控件允许你选择要添加到 Runbook 的活动。我们要添加 **Write-Output** cmdlet 以从 Runbook 输出文本。

1.	在“库”控件中，单击搜索文本框，然后键入 **Write-Output**。搜索结果将显示在下方。<br> ![Microsoft.PowerShell.Utility](./media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.	向下滚动到列表的底部。你可以先右键单击 **Write-Output**，然后选择“添加到画布”，也可以先单击 cmdlet 旁边的省略号，然后选择“添加到画布”。
3.	单击画布上的 **Write-Output** 活动。这将打开可用于配置活动的“配置控件”边栏选项卡。
4.	“标签”默认为该 cmdlet 的名称，但我们可以将其更改为更友好。将其更改为 *Write Hello World to output*。
5.	单击“参数”为 cmdlet 参数提供值。某些 cmdlet 有多个参数集，并且你需要选择将使用的参数集。在这种情况下，**Write-Output** 只具有一个参数集，因此无需选择一个<br>![Write-Output 属性](./media/automation-first-runbook-graphical/write-output-properties-b.png)。
6.	选择 **InputObject** 参数。这是我们将在其中指定要发送到输出流的文本的参数。
7.	在“数据源”下拉列表中，选择“PowerShell 表达式”。“数据源”下拉列表中提供了用于填充参数值的不同的源。你可以使用来自诸如另一个活动、自动化资产或 PowerShell 表达式等类型源的输出。在这种情况下，我们只想输出文本 *Hello World*。我们可以使用 PowerShell 表达式并指定一个字符串。
8.	在“表达式”框中，键入 *"Hello World"* 然后单击“确定”两次以返回到画布。<br>![PowerShell 表达式](./media/automation-first-runbook-graphical/expression-hello-world.png)
9.	通过单击“保存”来保存 Runbook。<br>![保存 Runbook](./media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## 步骤 3 - 测试 Runbook

在我们发布 Runbook 使其可在生产中使用之前，我们要对其进行测试以确保其能正常工作。测试 Runbook 时，你可以运行其“草稿”版本并以交互方式查看其输出。

1.	单击“测试窗格”打开“测试”边栏选项卡。<br>![“测试”窗格](./media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.	单击“启动”以启动测试。这应该是唯一的已启用选项。
3.	会创建一个“Runbook 作业”并且在窗格中显示其状态[](/documentation/articles/automation-runbook-execution/)。作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色可用。在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。
4.	Runbook 作业完成后，会显示其输出。在本例中，我们应该看到 *Hello World*。<br>![Hello World](./media/automation-first-runbook-graphical/runbook-test-results.png)
5.	关闭“测试”边栏选项卡以返回到画布。

## 步骤 4 - 发布和启动 Runbook

我们刚刚创建的 Runbook 仍处于草稿模式。我们需要发布该 Runbook，然后才可将其用于生产。当发布 Runbook 时，你可以用草稿版本覆盖现有的已发布版本。在本例中，我们还没有已发布版本，因为我们刚刚创建 Runbook。

1.	单击“发布”以发布该 Runbook，然后在出现提示时单击“是”。<br>![发布](./media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.	如果你现在向左滚动以在“Runbook”边栏选项卡中查看该 Runbook，它将显示“已发布”的“创作状态”。
3.	向右滚动查看“MyFirstRunbook”边栏选项卡。顶部的选项允许我们启动 Runbook，计划其在将来的某个时刻启动，或创建 [webhook](/documentation/articles/automation-webhooks/) 以使其可以通过 HTTP 调用启动。
4.	我们只想要启动 Runbook，因此单击“启动”，然后在出现提示时单击“是”。<br>![启动 Runbook](./media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.	此时会为我们刚刚创建的 Runbook 作业打开作业边栏选项卡。我们可以关闭此边栏选项卡，但在这种情况下我们需要让其保持打开状态，以便查看该作业的进度。
6.	作业状态显示在“作业摘要”中并且与我们在测试该 Runbook 时看到的状态相匹配。<br>![作业摘要](./media/automation-first-runbook-graphical/runbook-job-summary.png)
7.	一旦此 Runbook 状态显示“已完成”，单击“输出”。此时会打开“输出”边栏选项卡，我们可以在该边栏选项卡中看到 *Hello World*。<br>![作业摘要](./media/automation-first-runbook-graphical/runbook-job-output.png)
8.	关闭“输出”边栏选项卡。
9.	单击“所有日志”打开 Runbook 作业的“流”边栏选项卡。应该只会在输出流中看到 *Hello World*，但此窗格也可以显示 Runbook 作业的其他流，例如，“详细”和“错误”（如果 Runbook 向其写入)。<br>![作业摘要](./media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10.	关闭“所有日志”边栏选项卡和“作业”边栏选项卡，以便返回到“MyFirstRunbook”边栏选项卡。
11.	单击“作业”打开此 Runbook 的“作业”边栏选项卡。这将列出此 Runbook 创建的所有作业。由于我们只运行该作业一次，应该只会看到一个列出的作业。<br>![作业](./media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	你可以在此作业上单击以打开我们启动 Runbook 时查看的相同的作业窗格。这样你就可以回溯并查看为特定 Runbook 创建的任何作业的详细信息。

## 步骤 5 - 创建变量资产

我们已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。我们想要让其管理 Azure 资源。在配置进行身份验证的 Runbook 之前，需要创建一个变量来保存订阅 ID，以便在设置身份验证活动以后引用该 ID（下面的步骤 6）。如果包括对订阅上下文的引用，则可轻松地在多个订阅之间进行切换。在继续操作之前，从“导航”窗格的“订阅”选项中复制订阅 ID。

1. 在“自动化帐户”边栏选项卡中，单击“资产”磁贴即可打开“资产”边栏选项卡。
2. 在“资产”边栏选项卡中，单击“变量”磁贴。
3. 在“变量”边栏选项卡中，单击“添加变量”。<br>![自动化变量](./media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. 在“新建变量”边栏选项卡的“名称”框中，输入“AzureSubscriptionId”，然后在“值”框中输入你的订阅 ID。对于“类型”，请保留“字符串”；对于“加密”，请保留默认值。
5. 单击“创建”以创建该变量。


## 步骤 6 - 添加身份验证来管理 Azure 资源

现在，我们已经有了一个用于保存订阅 ID 的变量，因此可以对 Runbook 进行配置，以便使用在[先决条件](#prerequisites)中提到过的运行方式凭据进行身份验证。为此，可将 Azure 运行方式连接**资产**和 **Add-AzureRMAccount** cmdlet 添加到画布中。

1.	通过单击“MyFirstRunbook”边栏选项卡上的“编辑”打开图形编辑器。<br>![编辑 Runbook](./media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.	我们不再需要 **Write Hello World to output**，因此请右键单击它并选择“删除”。
3.	在“库”控件中展开“连接”，然后通过选择“添加到画布”将“AzureRunAsConnection”添加到画布。
4.	在画布上选择“AzureRunAsConnection”，然后在“配置控件”窗格的“标签”文本框中键入“获取运行方式连接”。这是该连接
5.	在“库”控件的搜索文本框中，键入“Add-AzureRmAccount”。
6.	将 **Add-AzureRmAccount** 添加到画布。<br>![Add-AzureRMAccount](./media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.	将鼠标悬停在“获取运行方式连接”上方，直到在该形状的底部显示一个圆圈。单击该圆圈并将箭头拖至 **Add-AzureRmAccount**。你刚创建的箭头是“链接”。该 Runbook 一开始会运行“获取运行方式连接”，然后运行 **Add-AzureRmAccount**。<br>![创建活动之间的链接](./media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.	在画布上选择“Add-AzureRmAccount”，然后在“配置控件”窗格的“标签”文本框中键入“登录到 Azure”。
9.	单击“参数”，然后就会显示“活动参数配置”边栏选项卡。
10.  **Add-AzureRmAccount** 有多个参数集，因此需要选择其中一个，然后才能提供参数值。单击“参数集”，然后选择“ServicePrincipalCertificate”参数集。
11.  选择参数集后，这些参数将显示在“活动参数配置”边栏选项卡中。单击“APPLICATIONID”。<br>![添加 Azure RM 帐户参数](./media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  在“参数集”边栏选项卡中，选择“活动输出”作为“数据源”，然后从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“ApplicationId”，然后单击“确定”。我们需指定“字段路径”的属性的名称，因为活动所输出的对象包含多个属性。
13.  单击“CERTIFICATETHUMBPRINT”，然后在“参数值”边栏选项卡中，选择“活动输出”作为“数据源”。从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“CertificateThumbrprint”，然后单击“确定”。
14.  单击“SERVICEPRINCIPAL”，在“参数值”边栏选项卡中选择“ConstantValue”作为“数据源”，单击选项“True”，然后单击“确定”。
15.  单击“TENANTID”，然后在“参数值”边栏选项卡中，选择“活动输出”作为“数据源”。从列表中选择“获取运行方式连接”，在“字段路径”文本框中键入“TenantId”，然后单击“确定”两次。
16.  在“库”控件的搜索文本框中，键入“Set-AzureRmContext”。
17.	 将 **Set-AzureRmContext** 添加到画布。
18.	 在画布上选择“Set-AzureRmContext”，然后在“配置控件”窗格的“标签”文本框中键入“指定订阅 ID”。
19.	 单击“参数”，然后就会显示“活动参数配置”边栏选项卡。
20. **Set-AzureRmContext** 有多个参数集，因此需要选择其中一个，然后才能提供参数值。单击“参数集”，然后选择“SubscriptionId”参数集。
21.	 选择参数集后，这些参数将显示在“活动参数配置”边栏选项卡中。单击“SubscriptionID”
22.	 在“参数值”边栏选项卡中，选择“变量资产”作为“数据源”，并从列表中选择“AzureSubscriptionId”，然后单击“确定”两次。
23.  将鼠标悬停在“登录到 Azure”上方，直到在该形状的底部显示一个圆圈。单击该圆圈并将箭头拖至“指定订阅 ID”。


此时，你的 Runbook 看起来将如下所示：<br>![Runbook 身份验证配置](./media/automation-first-runbook-graphical/runbook-auth-config.png)

## 步骤 7 - 添加用于启动虚拟机的活动

我们现在将添加 **Start-AzureRmVM** 活动来启动虚拟机。你可以在你的 Azure 订阅中选取任何虚拟机，现在我们会将该名称硬编码到 cmdlet 中。

1. 在“库”控件的搜索文本框中，键入“Start-AzureRm”。
2. 将 **Start-AzureRmVM** 添加到画布，然后单击并将其拖放到“指定订阅 ID”之下。
3. 将鼠标悬停在“指定订阅 ID”上方，直到在该形状的底部显示一个圆圈。单击该圆圈并将箭头拖至 **Start-AzureRmVM**。
4.	选择“Start-AzureRmVM”。单击“参数”，然后单击“参数集”查看 **Start-AzureRmVM** 的参数集。选择 **ResourceGroupNameParameterSetName** 参数集。请注意，**ResourceGroupName** 和“名称”旁边有感叹号。这表示它们是必需的参数。另请注意，两者都需要字符串值。
5.	选择“名称”。选择“PowerShell 表达式”作为“数据源”，然后键入带双引号的虚拟机名称（一开始将用于此 Runbook）。单击“确定”。<br>![Start-AzureRmVM 名称参数值](./media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.	选择“ResourceGroupName”。使用“PowerShell 表达式”作为“数据源”，然后键入带双引号的资源组名称。单击“确定”。<br>![Start-AzureRmVM 参数](./media/automation-first-runbook-graphical/startazurermvm-params.png)
8.	单击“测试”窗格，以便我们可以测试 Runbook。
9.	单击“启动”以启动测试。一旦测试完成后，检查已启动的虚拟机。

此时，你的 Runbook 看起来将如下所示：<br>![Runbook 身份验证配置](./media/automation-first-runbook-graphical/runbook-startvm.png)

## 步骤 8 - 向 Runbook 添加其他输入参数

我们的 Runbook 当前启动在 **Start-AzureRmVM** cmdlet 中指定的资源组中的虚拟机，但如果我们能够在启动 Runbook 时同时指定这二者，我们的 Runbook 会更有用。我们现在将输入参数添加到 Runbook，以提供该功能。

1. 通过单击 MyFirstRunbook 窗格上的“编辑”打开图形编辑器。
2. 依次单击“输入和输出”和“添加输入”，打开“Runbook 输入参数”窗格。<br>![Runbook 输入和输出](./media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. 指定 *VMName* 作为“名称”。保留“字符串”作为“类型”，但将“必需”更改为“是”。单击“确定”。
4. 创建第二个称为 *ResourceGroupName* 的必需输入参数，然后单击“确定”关闭“输入和输出”窗格。<br>![Runbook 输入参数](./media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. 选择 **Start-AzureRmVM** 活动，然后单击“参数”。
6. 将“名称”的“数据源”更改为“Runbook 输入”，然后选择 **VMName**。<br>
7. 将“ResourceGroupName”的“数据源”更改为“Runbook 输入”，然后选择“ResourceGroupName”。<br>![Start-AzureVM 参数](./media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. 保存 Runbook 并打开“测试”窗格。请注意，现在可以为将在测试中使用的两个输入变量提供值。
9. 关闭“测试”窗格。
10.	单击“发布”以发布 Runbook 的新版本。
11.	停止在上一步中启动的虚拟机。
12.	单击“启动”以启动 Runbook。键入要启动的虚拟机的 **VMName** 和 **ResourceGroupName**。<br>![启动 Runbook](./media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13.	一旦 Runbook 完成后，检查已启动的虚拟机。

## 步骤 9 - 创建条件链接

我们现在将修改该 Runbook，使之仅在虚拟机尚未启动的情况下尝试启动虚拟机。为此，我们需要将 **Get-AzureRmVM** cmdlet 添加到 Runbook，以便获取虚拟机的实例级状态。然后，我们需要添加名为“获取状态”的 PowerShell 工作流代码模块，并使用 PowerShell 代码片段来确定虚拟机状态是“正在运行”还是“已停止”。如果当前的运行状态为“已停止”，则“获取状态”模块中的条件链接将只运行 **Start-AzureRmVM**。最后，我们会使用 PowerShell Write-Output cmdlet 输出一条消息，告知你 VM 是否已启动成功。

1. 在图形编辑器中打开 **MyFirstRunbook**。
2. 单击“指定订阅 ID”和“Start-AzureRmVM”之间的链接，然后按 Del 键将其删除。
3. 在“库”控件的搜索文本框中，键入“Get-AzureRm”。
4. 将 **Get-AzureRmVM** 添加到画布。
5. 选择“Get-AzureRmVM”，然后选择“参数集”以查看 **Get-AzureRmVM** 的参数集。选择 **GetVirtualMachineInResourceGroupNameParamSet** 参数集。请注意，**ResourceGroupName** 和“名称”旁边有感叹号。这表示它们是必需的参数。另请注意，两者都需要字符串值。
6. 在“名称”的“数据源”下，选择“Runbook 输入”，然后选择 **VMName**。单击**“确定”**。
7. 将“ResourceGroupName”的“数据源”下，选择“Runbook 输入”，然后选择“ResourceGroupName”。单击**“确定”**。
8. 在“状态”的“数据源”下，选择“常数值”，然后单击“True”。单击**“确定”**。
9. 创建一个从“指定订阅 ID”到“Get-AzureRmVM”的链接。
10. 在“库”控件中，展开“Runbook 控件”，然后将“Code”添加到画布。
11. 创建从“Get-AzureRmVM”到“Code”的链接。
12. 单击“Code”，然后在“配置”窗格中将标签更改为“获取状态”。
13. 选择 **Code** 参数，此时会显示“代码编辑器”边栏选项卡。
14. 在代码编辑器中，粘贴以下代码片段：

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. 创建从“获取状态”到“Start-AzureRmVM”的链接。<br>![使用代码模块的 Runbook](./media/automation-first-runbook-graphical/runbook-startvm-get-status.png)
16. 选择该链接，然后在“配置”窗格中，将“应用条件”更改为“是”。请注意，该链接将变为虚线，指示仅在条件解析为 True 时才会运行目标活动。
17. 对于“条件表达式”，请键入 *$ActivityOutput['Get Status'] -eq "Stopped"*。**Start-AzureRmVM** 现在仅在虚拟机停止的情况下才会运行。
18.	在库控件中，展开 **Cmdlet** 节点，然后展开 **Microsoft.PowerShell.Utility**。
19.	将 **Write-Output** 添加到画布两次。<br>![使用 Write-Output 的 Runbook](./media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. 在第一个 **Write-Output** 控件中，将“标签”值更改为“通知 VM 已启动”。
21. 对于 **InputObject**，将“数据源”更改为“PowerShell 表达式”，然后键入表达式 *"$VMName successfully started."*。
22. 在第二个 **Write-Output** 控件中，将“标签”值更改为“通知 VM 启动失败”。
23. 对于 **InputObject**，将“数据源”更改为“PowerShell 表达式”，然后键入表达式 *"$VMName could not start."*。
24. 创建从 **Start-AzureRmVM** 到“通知 VM 已启动”和“通知 VM 启动失败”的链接。
25. 选择到“通知 VM 已启动”的链接，并将”应用条件“更改为 **True**。
26. 对于“条件表达式”，键入 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*。此 Write-Output 控件现在将仅在虚拟机已成功启动的情况下运行。
27. 选择到“通知 VM 启动失败”的链接，并将”应用条件“更改为 **True**。
28. 对于“条件表达式”，键入 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*。此 Write-Output 控件现在将仅在虚拟机未成功启动的情况下运行。
29.	保存 Runbook 并打开“测试”窗格。
30.	如果在虚拟机停止时启动 Runbook，那么虚拟机将启动。

## 后续步骤

-	若要详细了解图形创作，请参阅 [Azure 自动化中的图形创作](/documentation/articles/automation-graphical-authoring-intro/)
-	若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](/documentation/articles/automation-first-runbook-textual-powershell/)
-	若要开始使用 PowerShell 工作流 Runbook，请参阅[我的第一个 PowerShell 工作流 Runbook](/documentation/articles/automation-first-runbook-textual/)

<!---HONumber=AcomDC_0718_2016-->