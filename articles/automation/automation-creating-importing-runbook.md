<properties
	pageTitle="在 Azure 自动化中创建或导入 Runbook"
	description="本文介绍了如何在 Azure 自动化中创建新的 Runbook，或如何从文件中导入 Runbook。"
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="automation"
	ms.date="05/31/2016"
	wacn.date=""/>

# 在 Azure 自动化中创建或导入 Runbook

你可以通过以下方法将 Runbook 添加到 Azure 自动化：[创建新的 Runbook](#creating-a-new-runbook)；从文件或 [Runbook 库](/documentation/articles/automation-runbook-gallery/)导入现有 Runbook。本文介绍如何通过文件创建和导入 Runbook。你可以在 [Azure 自动化的 Runbook 和模块库](/documentation/articles/automation-runbook-gallery/)中获取有关如何访问社区 Runbook 和模块的所有详细信息。

## <a name="creating-a-new-runbook"></a> 创建新的 Runbook

[AZURE.ACOM]{

你可以使用其中一个 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建一个新的 Runbook。一旦创建了 Runbook，你就可以利用[了解 PowerShell 工作流](/documentation/articles/automation-powershell-workflow/)和 [Azure 自动化中的图形创作](/documentation/articles/automation-graphical-authoring-intro/)中的信息对其进行编辑。

[AZURE.ACOM]}

[AZURE.ACN]{

你可以使用其中一个 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建一个新的 Runbook。一旦创建了 Runbook，你就可以利用[了解 PowerShell 工作流](/documentation/articles/automation-powershell-workflow/)中的信息对其进行编辑。

[AZURE.ACN]}

### 使用 Azure 经典管理门户创建新的 Azure 自动化 Runbook

[AZURE.ACOM]{

你只能使用 Azure 门户中的 [PowerShell 工作流 Runbook](/documentation/articles/automation-runbook-types/#powershell-workflow-runbooks)。

[AZURE.ACOM]}

[AZURE.ACN]{

你只能使用 Azure 经典管理门户中的 PowerShell 工作流 Runbook。

[AZURE.ACN]}

1. 在 Azure 经典管理门户中，单击“新建”>“应用程序服务”>“自动化”>“Runbook”>“快速创建”。
2. 输入所需的信息，然后单击“创建”。Runbook 名称必须以字母开头，可以使用字母、数字、下划线和短划线。
3. 若要立即编辑 Runbook，则请单击“编辑 Runbook”。否则，请单击“确定”。
4. 新的 Runbook 将出现在“Runbook”选项卡中。


[AZURE.ACOM]{

### 使用 Azure 门户创建新的 Azure 自动化 Runbook

1. 在 Azure 门户中，打开你的自动化帐户。
2. 单击“Runbook”磁贴打开 Runbook 列表。
3. 单击“添加 Runbook”按钮，然后单击“创建新 Runbook”。
2. 键入 Runbook 的**名称**，然后选择其[类型](/documentation/articles/automation-runbook-types/)。Runbook 名称必须以字母开头，可以使用字母、数字、下划线和短划线。
3. 单击“创建”以创建 Runbook 并打开编辑器。

[AZURE.ACOM]}

### 使用 Windows PowerShell 创建新的 Azure 自动化 Runbook

[AZURE.ACOM]{

你可以使用 [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/mt619376.aspx) cmdlet 创建空的 [PowerShell 工作流 Runbook](/documentation/articles/automation-runbook-types/#powershell-workflow-runbooks)。你可以通过指定 **Name** 参数来创建空的可以随后编辑的 Runbook，也可以通过指定 **Path** 参数来导入 Runbook 文件。还应包括 **Type** 参数，以指定四种 Runbook 类型之一。

[AZURE.ACOM]}

[AZURE.ACN]{

你可以使用 [New-AzureAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/dn690272.aspx) cmdlet 创建空的 PowerShell 工作流 Runbook。你可以通过指定 **Name** 参数来创建空的可以随后编辑的 Runbook，也可以通过指定 **Path** 参数来导入脚本文件。

[AZURE.ACN]}

以下示例命令演示了如何创建新的空 Runbook。

    [AZURE.ACOM]{
    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
    [AZURE.ACOM]}
    [AZURE.ACN]{
    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName
    [AZURE.ACN]}

## <a name="ImportRunbook"></a> 将 Runbook 从文件导入 Azure 自动化

[AZURE.ACOM]{

你可以在 Azure 自动化中创建新的 Runbook，方法是导入 PowerShell 脚本或 PowerShell 工作流（扩展名为 .ps1）或导出的图形 Runbook (.graphrunbook)。必须指定要通过导入创建的 [Runbook 类型](/documentation/articles/automation-runbook-types/)，并考虑以下注意事项。

- .graphrunbook 文件只能导入到新的[图形 Runbook](/documentation/articles/automation-runbook-types/#graphical-runbooks) 中，并且只能从 .graphrunbook 文件创建图形 Runbook。
- 包含 PowerShell 工作流的 .ps1 文件只能导入到 [PowerShell 工作流 Runbook](/documentation/articles/automation-runbook-types/#powershell-workflow-runbooks) 中。如果该文件包含多个 PowerShell 工作流，导入将失败。必须将每个工作流保存到各自的文件中，并分别导入每个工作流。
- 不包含工作流的 .ps1 文件可以导入到 [PowerShell Runbook](/documentation/articles/automation-runbook-types/#powershell-runbooks) 中，也可以导入到 [PowerShell 工作流 Runbook](/documentation/articles/automation-runbook-types/#powershell-workflow-runbooks) 中。如果将工作流导入到 PowerShell 工作流 Runbook 中，则会将其转换为工作流，并会在 Runbook 中添加注释，详述所做的更改。

[AZURE.ACOM]}

[AZURE.ACN]{

你可以在 Azure 自动化中创建新的 Runbook，方法是导入 PowerShell 工作流（扩展名为 .ps1）。

如果该文件包含多个 PowerShell 工作流，导入将失败。必须将每个工作流保存到各自的文件中，并分别导入每个工作流。

[AZURE.ACN]}

### 使用 Azure 经典管理门户从文件导入 Runbook

[AZURE.ACOM]{

可通过以下过程将脚本文件导入 Azure 自动化。请注意，你只能通过此门户将 .ps1 文件导入 PowerShell 工作流 Runbook。其他类型必须使用 Azure 门户。

[AZURE.ACOM]}

[AZURE.ACN]{

可通过以下过程将脚本文件导入 Azure 自动化。请注意，你只能通过此门户将 .ps1 文件导入 PowerShell 工作流 Runbook。

[AZURE.ACN]}

1. 在 Azure 经典管理门户中，选择“自动化”，然后选择一个自动化帐户。
2. 单击“导入”。
3. 单击“浏览文件”，找到要导入的脚本文件。
4. 若要立即编辑 Runbook，则请单击“编辑 Runbook”。否则，请单击“确定”。
5. 新的 Runbook 将出现在自动化帐户的“Runbook”选项卡中。
6. 必须先[发布 Runbook](#publishing-a-runbook)，然后才能运行它。

[AZURE.ACOM]{

### 使用 Azure 门户通过文件导入 Runbook
可通过以下过程将脚本文件导入 Azure 自动化。

>[AZURE.NOTE] 请注意，你只能通过此门户将 .ps1 文件导入 PowerShell 工作流 Runbook。

1. 在 Azure 门户中，打开你的自动化帐户。
2. 单击“Runbook”磁贴打开 Runbook 列表。
3. 单击“添加 Runbook”按钮，然后单击“导入”。
4. 单击“Runbook 文件”以选择要导入的文件
2. 如果“名称”字段已启用，则可以选择更改它。Runbook 名称必须以字母开头，可以使用字母、数字、下划线和短划线。
3. 将自动选择 [Runbook 类型](/documentation/articles/automation-runbook-types/)，但你可以在考虑适用的限制后更改该类型。
3. 新的 Runbook 将出现在自动化帐户的 Runbook 列表中。
4. 必须先[发布 Runbook](#publishing-a-runbook)，然后才能运行它。

>[AZURE.NOTE] 在导入图形 Runbook 或图形 PowerShell 工作流 Runbook 后，你可以选择转换为其他类型（如果需要）。不能转换为文本 Runbook。

[AZURE.ACOM]}

### <a name="ImportRunbookScriptPS"></a> 使用 Windows PowerShell 从脚本文件导入 Runbook

[AZURE.ACOM]{

你可以使用 [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/mt603735.aspx) cmdlet 将脚本文件导入为 PowerShell 工作流草稿 Runbook。如果 Runbook 已存在，则除非你使用 *-Force* 参数，否则导入将失败。

下面的示例命令演示了如何将脚本文件导入到 Runbook 中。

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 

[AZURE.ACOM]}

[AZURE.ACN]{

可以使用 [Set-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/zh-cn/library/dn690267.aspx) cmdlet 将脚本文件导入到现有 Runbook 的草稿版本中。脚本文件必须包含单个 Windows PowerShell 工作流。如果 Runbook 已有草稿版本，则除非你使用 Overwrite 参数，否则导入将失败。导入 Runbook 后，可以使用 [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/dn690266.aspx) 来发布它。

下面的示例命令演示了如何将脚本文件导入到现有 Runbook 中，然后将其发布。

    $automationAccountName = "AutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName

[AZURE.ACN]}


## <a name="publishing-a-runbook"></a> 发布 Runbook

创建或导入新的 Runbook 时，必须先将其发布，然后才能导入。自动化中的每个 Runbook 都有草稿版和已发布版。只有已发布版才能用来运行，只有草稿版才能用来编辑。已发布版不受对草稿版所做的任何更改的影响。当草稿版可以使用时，你可以发布草稿版，这样草稿版就会覆盖已发布版。

## 使用 Azure 经典管理门户发布 Runbook

1. 在 Azure 经典管理门户中打开 Runbook。
1. 在屏幕顶部，单击“创作”。
1. 在屏幕底部，单击“发布”，然后在出现验证消息时单击“是”。

[AZURE.ACOM]{

## 使用 Azure 门户发布 Runbook

1. 在 Azure 门户中打开 Runbook。
1. 单击“Edit”（编辑）按钮。
1. 单击“发布”按钮，然后对验证消息单击“是”。

[AZURE.ACOM]}

## 使用 Windows PowerShell 发布 Runbook

[AZURE.ACOM]{

可以使用 Windows PowerShell，通过 [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/mt603705.aspx) cmdlet 来发布 Runbook。以下示例命令显示了如何发布示例 Runbook。

[AZURE.ACOM]}

[AZURE.ACN]{

可以使用 Windows PowerShell，通过 [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/zh-cn/library/dn690266.aspx) cmdlet 来发布 Runbook。以下示例命令显示了如何发布示例 Runbook。

[AZURE.ACN]}

	$automationAccountName = "AutomationAccount"
	$runbookName = "Sample_TestRunbook"

	[AZURE.ACOM]{
    $RGName = "ResourceGroup"

	Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
	-Name $runbookName -ResourceGroupName $RGName
    [AZURE.ACOM]}
	[AZURE.ACN]{
    Publish-AzureAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName
    [AZURE.ACN]}


## 后续步骤
- 若要了解你可以如何从 Runbook 和 PowerShell 模块库中受益，请参阅 [Azure 自动化的 Runbook 和模块库](/documentation/articles/automation-runbook-gallery/)
- 若要了解有关使用文本编辑器编辑 PowerShell 和 PowerShell 工作流 Runbook 的详细信息，请参阅[编辑 Azure 自动化中的文本 Runbook](/documentation/articles/automation-edit-textual-runbook/)
- [AZURE.ACOM]{
- 若要详细了解图形 Runbook 创作，请参阅 [Azure 自动化中的图形创作](/documentation/articles/automation-graphical-authoring-intro/)
- [AZURE.ACOM]}

<!---HONumber=Mooncake_0725_2016-->