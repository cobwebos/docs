<!-- not suitable for Mooncake -->

<properties
   pageTitle="Azure 自动化混合 Runbook 辅助角色 | Azure"
   description="本文介绍了如何安装和使用混合 Runbook 辅助角色，该角色是 Azure 自动化的一项功能，可以用于在你本地数据中心的计算机上运行 Runbook。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
	ms.service="automation"
	ms.date="08/10/2016"
	wacn.date=""/>

# Azure 自动化混合 Runbook 辅助角色

Azure 自动化中的 Runbook 无法访问你本地数据中心的资源，因为它们运行在 Azure 云中。利用 Azure 自动化的混合 Runbook 辅助角色功能，你可以在位于数据中心的计算机上运行 Runbook，以便管理本地资源。Runbook 在 Azure 自动化中进行存储和管理，然后会发送到一个或多个本地计算机。

下图演示了此功能。<br>

![混合 Runbook 辅助角色概述](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

你可以指定数据中心的一台或多台计算机充当混合 Runbook 辅助角色，然后通过 Azure 自动化运行 Runbook。每个辅助角色都需要可以连接到 Microsoft Operations Management Suite 和 Azure 自动化 Runbook 环境的 Microsoft 管理代理。Operations Management Suite 仅用于安装和维护管理代理并监视辅助角色的功能。Runbook 及其运行指令的传送由 Azure 自动化来执行。

![混合 Runbook 辅助角色组件](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components_b.png)

>[AZURE.NOTE] Operational Insights 目前正在集成到 Operations Management Suite 中，你可能会看到门户和文档中使用任一名称。

为混合 Runbook 辅助角色提供支持时，没有入站防火墙要求。本地计算机上的代理可启动与云中 Azure 自动化的所有通信。启动 Runbook 时，Azure 自动化会创建一种通过代理进行检索的指令。代理然后会拉取 Runbook 和任何参数，然后再运行 Runbook。它还会检索由 Azure 自动化中的 Runbook 使用的任何[资产](http://msdn.microsoft.com/zh-cn/library/dn939988.aspx)。

>[AZURE.NOTE] 混合 Runbook 辅助角色当前不支持 [DSC 配置](/documentation/articles/automation-dsc-overview/)。


## 混合 Runbook 辅助角色组

每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。一个组可以包含一个代理，但是你可以在一个组中安装多个代理，以实现高可用性。

当你在混合 Runbook 辅助角色中启动 Runbook 时，你可以指定该辅助角色会在其中运行的组。组的成员会决定由哪个辅助角色来处理请求。你不能指定特定的辅助角色。

## 混合 Runbook 辅助角色要求

必须指定至少一台本地计算机才能运行混合 Runbook 作业。此计算机上必须安装以下软件：

- Windows Server 2012 或更高版本
- Windows PowerShell 4.0 或更高版本
- 至少两个核心和 4 GB 的 RAM

对于混合辅助角色，请考虑以下建议：

- 在每个组中指定多个混合辅助角色以保持高可用性。
- 混合辅助角色可与 Service Management Automation 或 System Center Orchestrator Runbook 服务器并存。
- 请考虑使用实际位于或接近自动化帐户所在区域的计算机，因为当作业完成时，作业数据将发回到 Azure 自动化。


>[AZURE.NOTE] 混合 Runbook 辅助角色版本 7.2.11136.0 目前仅支持使用 PowerShell 脚本通过代理服务器通信。将来的版本支持 PowerShell 工作流脚本。

### 配置代理和防火墙设置

要使本地混合 Runbook 辅助角色连接到并注册到 Microsoft Operations Management Suite (OMS) 服务，它必须有权访问下面所述的端口号和 URL。除了这些端口和 URL 以外，还需要有权访问 [Microsoft Monitoring Agent 连接 OMS 时所要使用的端口和 URL](/documentation/articles/log-analytics-proxy-firewall/#configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent)。如果使用代理服务器在代理与 OMS 服务之间通信，则需要确保能够访问相应的资源。如果使用防火墙来限制对 Internet 的访问，则需要将防火墙配置为允许访问。

下面的信息列出了混合 Runbook 辅助角色与自动化通信时所要使用的端口和 URL。

- 端口：只需使用 TCP 443 进行出站 Internet 访问
- 全局 URL：*.azure-automation.net

如果你为特定的区域定义了自动化帐户并想要限制与该区域数据中心之间的通信，请参考下表中为每个区域提供的 DNS 记录。

|**区域**|**DNS 记录**|
|--------------|--------------|
|中国东部|scus-jobruntimedata-prod-su1.azure-automation.net|
|中国东部 2|eus2-jobruntimedata-prod-su1.azure-automation.net|
|欧洲西部|we-jobruntimedata-prod-su1.azure-automation.net|
|中国北部|ne-jobruntimedata-prod-su1.azure-automation.net|
|加拿大中部|cc-jobruntimedata-prod-su1.azure-automation.net|
|中国东南部|sea-jobruntimedata-prod-su1.azure-automation.net|
|印度中部|cid-jobruntimedata-prod-su1.azure-automation.net|
|日本东部|jpe-jobruntimedata-prod-su1.azure-automation.net|
|澳大利亚东南部|ase-jobruntimedata-prod-su1.azure-automation.net|


## 安装混合 Runbook 辅助角色

以下过程描述如何安装和配置混合 Runbook 辅助角色。针对自动化环境执行前两个步骤一次，然后对每台辅助角色计算机重复其余步骤。

### 1\.创建 Operations Management Suite 工作区

如果尚无 Operations Management Suite 工作区，请使用 [Set up your workspace](https://technet.microsoft.com/zh-cn/library/mt484119.aspx)（设置工作区）中的说明创建工作区。如果你已经有一个工作区，则可以使用现有的。

### 2\.将自动化解决方案添加到 Operations Management Suite 工作区

解决方案将功能添加到 Operations Management Suite。自动化解决方案增加 Azure 自动化的功能，包括支持混合 Runbook 辅助角色。将解决方案添加到工作区时，它会自动将辅助角色组件往下推送到在下一步要安装的代理计算机。

请根据 [To add a solution using the Solutions Gallery](/documentation/articles/log-analytics-add-solutions/)（使用解决方案库添加解决方案）中的说明，将**自动化**解决方案添加到 Operations Management Suite 工作区。

### 3\.安装 Microsoft Monitoring Agent

Microsoft Monitoring Agent 可将计算机连接到 Operations Management Suite。当你在计算机本地安装代理并将其连接到工作区时，代理将自动下载混合 Runbook 辅助角色所需的组件。

按照 [Connect Windows computers to Log Analytics](/documentation/articles/log-analytics-windows-agents/)（将 Windows 计算机连接到 Log Analytics）中的说明在本地计算机上安装代理。可以对多台计算机重复此过程，以将多个辅助角色添加到环境。

代理成功连接到 Operations Management Suite 后，将列在 Operations Management Suite“设置”窗格的“已连接的源”选项卡上。当 C:\\Program Files\\Microsoft Monitoring Agent\\Agent 中出现名为 **AzureAutomationFiles** 的文件夹时，你可确认代理已正确下载自动化解决方案。若要确认混合 Runbook 辅助角色的版本，可以导航到 C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\AzureAutomation\\ 并留意 \*version* 子文件夹。


### 4\.安装 Runbook 环境并连接到 Azure 自动化

将代理添加到 Operations Management Suite 时，自动化解决方案会向下推送 **HybridRegistration** PowerShell 模块，其中包含 **Add-HybridRunbookWorker** cmdlet。使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1

然后，请使用以下语法运行 **Add-HybridRunbookWorker** cmdlet：

	Add-HybridRunbookWorker -Name <String> -EndPoint <Url> -Token <String>

可以从 Azure 门户的“管理密钥”边栏选项卡获取此 cmdlet 所需的信息。在自动化帐户的“元素”面板中单击密钥图标即可打开此边栏选项卡。

![混合 Runbook 辅助角色概述](./media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **名称**是指混合 Runbook 辅助角色组的名称。如果该组已经存在于自动化帐户中，则会将当前计算机添加到其中。如果该组不存在，则会创建它。
- “终结点”是“管理密钥”边栏选项卡中的“URL”字段。
- **令牌**是指“管理密钥”边栏选项卡中的**主访问密钥**。

在 **Add-HybridRunbookWorker** 中添加 **-Verbose** 开关可接收有关安装的详细信息。

### 5\.安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。不过，这些模块不会自动部署到本地计算机，因此必须手动安装。例外情况是 Azure 模块，该模块是默认安装的，可以用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色功能的主要用途是管理本地资源，你很可能需要安装支持这些资源的模块。你可以参考[安装模块](http://msdn.microsoft.com/zh-cn/library/dd878350.aspx)以获取有关安装 Windows PowerShell 模块的信息。

## 删除混合 Runbook 辅助角色

在本地计算机上运行 **Remove-HybridRunbookWorker** cmdlet 即可从该计算机删除混合 Runbook 辅助角色。使用 **-Verbose** 开关可获取删除过程的详细日志。

## 在混合 Runbook 辅助角色中启动 Runbook

[在 Azure 自动化中启动 Runbook](/documentation/articles/automation-starting-a-runbook/) 介绍了用于启动 Runbook 的不同方法。混合 Runbook 辅助角色增加了一个 **RunOn** 选项，你可以在其中指定混合 Runbook 辅助角色组的名称。如果指定了组，则会由该组中的辅助角色检索和运行 Runbook。如果未指定此选项，则会在 Azure 自动化中正常运行 Runbook。

在 Azure 门户中启动 Runbook 时，你会看到一个“运行位置”选项，你可以在其中选择“Azure”或“混合辅助角色”。如果你选择“混合辅助角色”，则可以从下拉列表中选择该组。

使用 **RunOn** 参数，你可以使用以下命令通过 Windows PowerShell 在名为 MyHybridGroup 的混合 Runbook 辅助角色组中启动一个名为 Test-Runbook 的 Runbook。

	Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE] 在 0.9.1 版的 Azure PowerShell 中，**RunOn** 参数已添加到 **Start-AzureAutomationRunbook** cmdlet。如果你安装的是旧版，则应[下载最新版本](/downloads/)。你只需在要在其中通过 Windows PowerShell 启动 Runbook 的工作站上安装此版本。你不需要在辅助角色计算机上安装它，除非你要从该计算机启动 Runbook。你目前还不能通过其他 Runbook 在混合 Runbook 辅助角色上启动 Runbook，因为这需要在你的自动化帐户中安装最新版本的 Azure Powershell。最新版本将在 Azure 自动化中自动更新，并会快速地自动向下推送到辅助角色。

## Runbook 权限

在混合 Runbook 辅助角色上运行的 Runbook 不能使用[通常用于针对 Azure 资源进行 Runbook 身份验证的方法](/documentation/articles/automation-configuring/#configuring-authentication-to-azure-resources)，因为它们将要访问的资源位于 Azure 之外。Runbook 可将自身的身份验证提供给本地资源，或者可以指定 RunAs 帐户以便为所有 Runbook 提供用户上下文。

### Runbook 身份验证

默认情况下，在本地计算机上，Runbook 将在本地系统帐户的上下文中运行，因此必须针对要访问的资源进行身份验证。

你可以在包含 cmdlet 的 Runbook 中使用[凭据](http://msdn.microsoft.com/zh-cn/library/dn940015.aspx)和[证书](http://msdn.microsoft.com/zh-cn/library/dn940013.aspx)资产，这些 cmdlet 可以让你指定凭据，方便你向不同资源进行身份验证。下面的示例显示了用于重新启动计算机的 Runbook 的一部分。它从凭据资产检索凭据，从变量资产检索计算机的名称，然后将这些值用于 Restart-Computer cmdlet。

	$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
	$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

	Restart-Computer -ComputerName $Computer -Credential $Cred

你还可以利用 [InlineScript](/documentation/articles/automation-powershell-workflow/#inline-script)，以便在其他由 [PSCredential 通用参数](http://technet.microsoft.com/zh-cn/library/jj129719.aspx)指定凭据的计算机上运行代码块。

### RunAs 帐户

你不需要让 Runbook 将自身的身份验证提供给本地资源，而可以针对混合辅助角色组指定 **RunAs** 帐户。指定具有本地资源访问权限的[凭据资产](/documentation/articles/automation-credentials/)，在组中的混合 Runbook 辅助角色运行时，所有 Runbook 将在这些凭据下运行。

凭据的用户名必须采用以下格式之一：

- 域\\用户名
- username@domain  

- 用户名（适用于本地计算机的本地帐户）


使用以下过程针对混合辅助角色组指定 RunAs 帐户：

1. 创建具有本地资源访问权限的[凭据资产](/documentation/articles/automation-credentials/)。
2. 在 Azure 门户中打开自动化帐户。
2. 选择“混合辅助角色组”磁贴，然后选择组。
3. 选择“所有设置”，然后选择“混合辅助角色组设置”。
4. 将“运行身份”从“默认”更改为“自定义”。
5. 选择凭据，然后单击“保存”。


## 为混合 Runbook 辅助角色创建 Runbook

运行在 Azure 自动化中的 Runbook 和运行在混合 Runbook 辅助角色上的 Runbook 没有结构上的区别。上述两种 Runbook 使用起来可能会有很大差异，因为用于混合 Runbook 辅助角色的 Runbook 通常会管理你数据中心的本地资源，而 Azure 自动化中的 Runbook 通常会管理 Azure 云中的资源。

你可以在 Azure 自动化中编辑混合 Runbook 辅助角色的 Runbook，但如果你尝试在编辑器中测试 Runbook，则可能会遇到困难。用于访问本地资源的 PowerShell 模块可能没有安装在你的 Azure 自动化环境中，这种情况下，测试会失败。如果你安装了所需的模块，则 Runbook 会运行，但不能访问进行完整测试所需的本地资源。

## 在混合 Runbook 辅助角色中排查 Runbook 问题

[Runbook 输出和消息](/documentation/articles/automation-runbook-output-and-messages/)将从混合辅助角色发送到 Azure 自动化，就像云中运行的 Runbook 作业一样。就像在其他 Runbook 中一样，你还可以启用详细流和进度流。

日志存储在每个混合辅助角色本地的 C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes 中。

如果你的 Runbook 没有成功完成且作业摘要显示的状态为“已暂停”，请参阅故障诊断文章：[Hybrid Runbook Worker: A runbook job terminates with a status of Suspended](/documentation/articles/automation-troubleshooting-hrw-runbook-terminates-suspended/)（混合 Runbook 辅助角色：Runbook 作业以暂停状态终止）。

## 与 Service Management 自动化的关系

[Service Management Automation (SMA)](https://technet.microsoft.com/zh-cn/library/dn469260.aspx) 是 Microsoft Azure Pack (WAP) 的组件，其用来运行的 Runbook 与你本地数据中心的 Azure 自动化所支持的 Runbook 相同。与 Azure 自动化不同，SMA 需要进行本地安装，安装内容包括 Microsoft Azure Pack 管理门户，以及用于保留 Runbook 和 SMA 配置的数据库。Azure 自动化在云中提供这些服务，只要求你在本地环境中维护混合 Runbook 辅助角色。

如果你已经是 SMA 用户，则可以将 Runbook 移到 Azure 自动化处与混合 Runbook 辅助角色一起使用，不需要进行任何更改，前提是这些 Runbook 向[为混合 Runbook 辅助角色创建Runbook](#creating-runbooks-for-hybrid-runbook-worker) 中所述的资源进行身份验证。SMA 中的 Runbook 在辅助角色服务器的服务帐户的上下文中运行，此服务器可以为 Runbook 提供该身份验证。

可以使用以下条件来确定是带有混合 Runbook 辅助角色的 Azure 自动化还是 Service Management 自动化更适合你的要求。

- SMA 需要在本地安装比 Azure 自动化需要更多本地资源和更高维护成本的 Microsoft Azure Pack，而 Azure 自动化只需在本地 Runbook 辅助角色中安装代理。代理由 Operations Management Suite 管理，这进一步降低了其维护成本。
- Azure 自动化在云中存储其 Runbook，然后将这些 Runbook 传送给本地混合 Runbook 辅助角色。如果你的安全策略不允许此行为，则应使用 SMA。
- Microsoft Azure Pack 可免费下载，而 Azure 自动化可能需要订阅费用。
- 使用带混合 Runbook 辅助角色的 Azure 自动化，你可以在一个位置管理云资源和本地资源的 Runbook，而不必分别管理 Azure 自动化和 SMA。
- Azure 自动化具有在 SMA 中不可用的图形创作等高级功能。


## 后续步骤

- 若要详细了解可以用来启动 Runbook 的不同方法，请参阅 [Starting a Runbook in Azure Automation](/documentation/articles/automation-starting-a-runbook/)（在 Azure 自动化中启动 Runbook）
- 若要了解如何通过不同过程使用文本编辑器在 Azure 自动化中处理 PowerShell Runbook 和 PowerShell 工作流 Runbook，请参阅 [Editing a Runbook in Azure Automation](/documentation/articles/automation-edit-textual-runbook/)（在 Azure 自动化中编辑 Runbook）

 

<!---HONumber=Mooncake_0822_2016-->