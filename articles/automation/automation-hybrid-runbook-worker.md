---
title: "Azure 自动化混合 Runbook 辅助角色 | Microsoft Docs"
description: "本文介绍了如何安装和使用混合 Runbook 辅助角色，该角色是 Azure 自动化的一项功能，可以用于在你本地数据中心的计算机上运行 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 5cd863c3e357b67d281adb8484376295ad9099ec
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>使用混合 Runbook 辅助角色使数据中心内的资源实现自动化
Azure 自动化中的 Runbook 无法访问你本地数据中心的资源，因为它们运行在 Azure 云中。  利用 Azure 自动化的混合 Runbook 辅助角色功能，你可以在位于数据中心的计算机上运行 Runbook，以便管理本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后会发送到一个或多个本地计算机。  

下图说明了此功能：<br>  

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

你可以指定数据中心的一台或多台计算机充当混合 Runbook 辅助角色，然后通过 Azure 自动化运行 Runbook。  每个辅助角色都需要可以连接到 Microsoft Operations Management Suite 和 Azure 自动化 Runbook 环境的 Microsoft 管理代理。  Operations Management Suite 仅用于安装和维护管理代理并监视辅助角色的功能。  Runbook 及其运行指令的传送由 Azure 自动化来执行。

为混合 Runbook 辅助角色提供支持时，没有入站防火墙要求。 本地计算机上的代理可启动与云中 Azure 自动化的所有通信。 启动 Runbook 时，Azure 自动化会创建一种通过代理进行检索的指令。 代理然后会拉取 Runbook 和任何参数，然后再运行 Runbook。  它还会检索由 Azure 自动化中的 Runbook 使用的[资产](http://msdn.microsoft.com/library/dn939988.aspx)。

> [!NOTE]
> 为了使用所需状态配置 (DSC) 管理支持混合 Runbook 辅助角色的服务器配置，需将其添加为 DSC 节点。  若要进一步了解如何载入它们以供 DSC 管理，请参阅[载入由 Azure 自动化 DSC 管理的计算机](automation-dsc-onboarding.md)。           
><br>
>如果启用[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)，则连接到 OMS 工作区的任何 Windows 计算机将自动配置为混合 Runbook 辅助角色，以支持将 Runbook 用作此解决方案的一部分。  但是，该计算机未注册到任何已在自动化帐户中定义的混合辅助角色组。  只要将同一个帐户同时用于解决方案和混合 Runbook 辅助角色组成员身份，即可将该计算机添加到自动化帐户的混合 Runbook 辅助角色组，以支持自动化 Runbook。  此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。  

## <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组
每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。  一个组可以包含一个代理，但是你可以在一个组中安装多个代理，以实现高可用性。

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。  组的成员会决定由哪个辅助角色来处理请求。  你不能指定特定的辅助角色。

## <a name="hybrid-runbook-worker-requirements"></a>混合 Runbook 辅助角色要求
请指定至少一台本地计算机来运行混合 Runbook 作业。  此计算机上必须安装以下软件：

* Windows Server 2012 或更高版本
* Windows PowerShell 4.0 或更高版本。  建议在计算机上安装 Windows PowerShell 5.0 以提高可靠性。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=50395)下载最新版本
* 至少 2 个核心和 4 GB 的 RAM

对于混合辅助角色，请考虑以下建议：

* 在每个组中指定多个混合辅助角色以保持高可用性。  
* 混合辅助角色可与 Service Management Automation 或 System Center Orchestrator Runbook 服务器并存。
* 请考虑使用实际位于或接近自动化帐户所在区域的计算机，因为当作业完成时，作业数据将发回到 Azure 自动化。

### <a name="configure-proxy-and-firewall-settings"></a>配置代理和防火墙设置
要使本地混合 Runbook 辅助角色连接到并注册到 Microsoft Operations Management Suite (OMS) 服务，它必须有权访问下面所述的端口号和 URL。  除了这些端口和 URL 以外，还需有权访问连接到 OMS 时 [Microsoft Monitoring Agent 需要的端口和 URL](../log-analytics/log-analytics-windows-agents.md#network)。 如果使用代理服务器在代理与 OMS 服务之间通信，则需确保能够访问相应的资源。 如果使用防火墙来限制对 Internet 的访问，则需要将防火墙配置为允许访问。

下面的信息列出了混合 Runbook 辅助角色与自动化通信时所要使用的端口和 URL。

* 端口：只需使用 TCP 443 进行出站 Internet 访问
* 全局 URL：*.azure-automation.net

如果你为特定的区域定义了自动化帐户并想要限制与该区域数据中心之间的通信，请参考下表中为每个区域提供的 DNS 记录。

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲西部 |we-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲北部 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 美国政府弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us |

有关 IP 地址列表（非名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) xml 文件并进行查看。 

> [!NOTE]
> 此文件包含 Microsoft Azure 数据中心使用的 IP 地址范围（包括计算、SQL 和存储范围）。 每周都将发布更新的文件，反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 请每周下载新的 xml 文件，并在网站上执行必要的更改以正确地标识 Azure 中运行的服务。 快速路由用户可能会注意到，此文件用于在每个月第一周更新 Azure 空间的 BGP 播发。 
> 

## <a name="installing-hybrid-runbook-worker"></a>安装混合 Runbook 辅助角色

若要安装和配置混合 Runbook 辅助角色，可以使用两种方法。  建议的方法是使用自动化 Runbook 来彻底实现配置 Windows 计算机所需的过程的自动化。  第二种方法使用分步过程来手动安装和配置角色。  

### <a name="automated-deployment"></a>自动化部署

执行以下步骤，以便自动完成混合辅助角色的安装和配置。  

1. 直接从运行混合 Runbook 辅助角色的计算机或环境中的其他计算机的 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript)下载 New-OnPremiseHybridWorker.ps1 脚本，然后将其复制到辅助角色。  

    在执行期间，New-OnPremiseHybridWorker.ps1 脚本需要以下参数：

  * AutomationAccountName（必需）- 自动化帐户的名称。  
  * ResourceGroupName（必需）- 与自动化帐户关联的资源组的名称。  
  * HybridGroupName（必需）- 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 Runbook 的目标。 
  *  SubscriptionID（必需）- 包含自动化帐户的 Azure 订阅 ID。
  *  WorkspaceName（可选）- OMS 工作区名称。  如果没有 OMS 工作区，该脚本会创建并配置一个。  

     > [!NOTE]
     > 目前可与 OMS 集成的自动化区域仅限：**澳大利亚东南部**、**美国东部 2**、**东南亚**以及**西欧**。  如果自动化帐户不在其中的某个区域，脚本会创建 OMS 工作区，但会警告用户：无法将这些区域链接到一起。
     > 
2. 在计算机的“管理员”模式下，从“开始”屏幕启动 **Windows PowerShell**。  
3. 从 PowerShell 命令行 shell 中，导航到已下载脚本所在的文件夹并执行该脚本，请记得更改 -AutomationAccountName、-ResourceGroupName、-HybridGroupName、-SubscriptionId 和 -WorkspaceName 参数的值。

     > [!NOTE] 
     > 执行脚本后，系统将提示你在 Azure 上进行身份验证。  **必须**以订阅管理员角色成员和订阅共同管理员的帐户登录。  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. 系统会提示用户同意安装 **NuGet** 并使用 Azure 凭据进行身份验证。<br><br> ![执行 New-OnPremiseHybridWorker 脚本](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. 脚本完成后，“混合辅助角色组”边栏选项卡会显示新组和成员数，或者会将成员数进行递增（如果组已存在）。  可以从“混合辅助角色组”边栏选项卡上的列表中选择组，然后选择“混合辅助角色”磁贴。  在“混合辅助角色”边栏选项卡上，将会列出组的每个成员。  

### <a name="manual-deployment"></a>手动部署 
针对自动化环境执行前两个步骤一次，然后对每台辅助角色计算机重复其余步骤。

#### <a name="1-create-operations-management-suite-workspace"></a>1.创建 Operations Management Suite 工作区
如果尚无 Operations Management Suite 工作区，请按照[管理工作区](../log-analytics/log-analytics-manage-access.md)中的说明创建工作区。 如果你已经有一个工作区，则可以使用现有的。

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2.将自动化解决方案添加到 Operations Management Suite 工作区
解决方案将功能添加到 Operations Management Suite。  自动化解决方案增加 Azure 自动化的功能，包括支持混合 Runbook 辅助角色。  将解决方案添加到工作区时，它会自动将辅助角色组件往下推送到在下一步要安装的代理计算机。

请根据[使用解决方案库添加解决方案](../log-analytics/log-analytics-add-solutions.md)中的说明，将**自动化**解决方案添加到 Operations Management Suite 工作区。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安装 Microsoft Monitoring Agent
Microsoft Monitoring Agent 可将计算机连接到 Operations Management Suite。  当你在计算机本地安装代理并将其连接到工作区时，代理将自动下载混合 Runbook 辅助角色所需的组件。

按照[将 Windows 计算机连接到 Log Analytics](../log-analytics/log-analytics-windows-agents.md) 中的说明在本地计算机上安装代理。  可以对多台计算机重复此过程，以将多个辅助角色添加到环境。

代理成功连接到 Operations Management Suite 后，将列在 Operations Management Suite“设置”窗格的“已连接的源”选项卡上。  当 C:\Program Files\Microsoft Monitoring Agent\Agent 中出现名为 **AzureAutomationFiles** 的文件夹时，你可确认代理已正确下载自动化解决方案。  若要确认混合 Runbook 辅助角色的版本，可以导航到 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ 并留意 \\*version* 子文件夹。   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安装 Runbook 环境并连接到 Azure 自动化
将代理添加到 Operations Management Suite 时，自动化解决方案会向下推送 **HybridRegistration** PowerShell 模块，其中包含 **Add-HybridRunbookWorker** cmdlet。  使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

然后，请使用以下语法运行 **Add-HybridRunbookWorker** cmdlet：

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

可以从 Azure 门户的“管理密钥”边栏选项卡获取此 cmdlet 所需的信息。  打开此边栏选项卡，方法是通过自动化帐户在“设置”边栏选项卡中选择“密钥”选项。

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* “名称”是指混合 Runbook 辅助角色组的名称。 如果该组已经存在于自动化帐户中，则会将当前计算机添加到其中。  如果该组不存在，则会创建它。
* “终结点”是“管理密钥”边栏选项卡中的“URL”字段。
* “令牌”是指“管理密钥”边栏选项卡中的“主访问密钥”。  

使用包含 **Add-HybridRunbookWorker** 的 **-Verbose** 开关可接收有关安装的详细信息。

#### <a name="5-install-powershell-modules"></a>5.安装 PowerShell 模块
Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。  不过，这些模块不会自动部署到本地计算机，因此必须手动安装。  例外情况是 Azure 模块，该模块是默认安装的，可以用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色功能的主要用途是管理本地资源，很可能需要安装支持这些资源的模块。  可以参考 [Installing Modules](http://msdn.microsoft.com/library/dd878350.aspx)（安装模块），获取有关安装 Windows PowerShell 模块的信息。  安装的模块必须位于 PSModulePath 环境变量所引用的位置，以便混合辅助角色自动将其导入。  有关详细信息，请参阅 [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)（修改 PSModulePath 安装路径）。 

## <a name="removing-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色 
可以从组中删除一个或多个混合 Runbook 辅助角色，或者根据要求删除该组。  若要从本地计算机删除混合 Runbook 辅助角色，请执行以下步骤。

1. 在 Azure 门户中，导航到自动化帐户。  
2. 在“设置”边栏选项卡中，选择“密钥”并记下“URL”和“主访问密钥”字段的值。  下一步需要用到此信息。
3. 在管理员模式下打开 PowerShell 会话，并运行以下命令 - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`。  可使用 **-Verbose** 开关获取删除过程的详细日志。

> [!NOTE]
> 这不会从计算机中删除 Microsoft 监视代理，而只会删除混合 Runbook 辅助角色的功能和配置。  

## <a name="remove-hybrid-worker-groups"></a>删除混合辅助角色组
若要删除某个组，首先需要使用前面所示的过程，从每台计算机中删除属于该组的混合 Runbook 辅助角色，然后执行以下步骤删除该组。  

1. 在 Azure 门户中打开自动化帐户。
2. 选择“混合辅助角色组”磁贴，然后在“混合辅助角色组”边栏选项卡中选择要删除的组。  在选择特定的组之后，将显示“混合辅助角色组”属性边栏选项卡。<br> ![混合 Runbook 辅助角色组边栏选项卡](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. 在所选组的属性边栏选项卡中，单击“删除”。  此时将显示一条消息请求确认此操作，如果确定要继续，请选择“是”。<br> ![确认删除组对话框](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> 此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中启动 Runbook
[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 介绍了用于启动 Runbook 的不同方法。  混合 Runbook 辅助角色增加了一个 **RunOn** 选项，你可以在其中指定混合 Runbook 辅助角色组的名称。  如果指定了组，则会由该组中的辅助角色检索和运行 Runbook。  如果未指定此选项，则会在 Azure 自动化中正常运行 Runbook。

在 Azure 门户中启动 Runbook 时，你会看到一个“运行位置”选项，可以在其中选择“Azure”或“混合辅助角色”。  如果选择“混合辅助角色”，则可以从下拉列表中选择该组。

使用 **RunOn** 参数。  可以使用以下命令，通过 Windows PowerShell 在名为 MyHybridGroup 的混合 Runbook 辅助角色组中启动一个名为 Test-Runbook 的 Runbook。

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> 在 0.9.1 版的 Microsoft Azure PowerShell中，**RunOn** 参数已添加到 **Start-AzureAutomationRunbook** cmdlet。  如果你安装的是旧版，则应[下载最新版本](https://azure.microsoft.com/downloads/)。  只需在要在其中通过 Windows PowerShell 启动 Runbook 的工作站上安装此版本。  你不需要在辅助角色计算机上安装它，除非你要从该计算机启动 Runbook。  你目前还不能通过其他 Runbook 在混合 Runbook 辅助角色上启动 Runbook，因为这需要在你的自动化帐户中安装最新版本的 Azure Powershell。  最新版本将在 Azure 自动化中自动更新，并会快速地自动向下推送到辅助角色。
>
>

## <a name="runbook-permissions"></a>Runbook 权限
在混合 Runbook 辅助角色上运行的 Runbook 不能使用通常用于针对 Azure 资源进行 Runbook 身份验证的方法，因为它们会访问位于 Azure 之外的资源。  Runbook 可将自身的身份验证提供给本地资源，或者可以指定 RunAs 帐户以便为所有 Runbook 提供用户上下文。

### <a name="runbook-authentication"></a>Runbook 身份验证
默认情况下，在本地计算机上，Runbook 将在本地系统帐户的上下文中运行，因此必须针对要访问的资源进行身份验证。  

可以在包含 cmdlet 的 Runbook 中使用[凭据](http://msdn.microsoft.com/library/dn940015.aspx)和[证书](http://msdn.microsoft.com/library/dn940013.aspx)资产，这些 cmdlet 可以让你指定凭据，方便你向不同资源进行身份验证。  下面的示例显示了用于重新启动计算机的 Runbook 的一部分。  它从凭据资产检索凭据，从变量资产检索计算机的名称，然后将这些值用于 Restart-Computer cmdlet。

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

还可以利用 [InlineScript](automation-powershell-workflow.md#inlinescript)，以便在其他由 [PSCredential 通用参数](http://technet.microsoft.com/library/jj129719.aspx)指定凭据的计算机上运行代码块。

### <a name="runas-account"></a>RunAs 帐户
你不需要让 Runbook 将自身的身份验证提供给本地资源，而可以针对混合辅助角色组指定 **RunAs** 帐户。  指定具有本地资源访问权限的[凭据资产](automation-credentials.md)，在组中的混合 Runbook 辅助角色运行时，所有 Runbook 将在这些凭据下运行。  

凭据的用户名必须采用以下格式之一：

* 域\用户名
* username@domain
* 用户名（适用于本地计算机的本地帐户）

使用以下过程针对混合辅助角色组指定 RunAs 帐户：

1. 创建具有本地资源访问权限的[凭据资产](automation-credentials.md)。
2. 在 Azure 门户中打开自动化帐户。
3. 选择“混合辅助角色组”磁贴，然后选择组。
4. 选择“所有设置”，然后选择“混合辅助角色组设置”。
5. 将“运行身份”从“默认”更改为“自定义”。
6. 选择凭据，然后单击“保存”。

### <a name="automation-run-as-account"></a>自动化运行方式帐户
在 Azure 中部署资源时，可能需要在自动生成过程中要求本地系统支持部署过程中的某个任务或某组步骤。  需安装运行方式帐户证书，然后才能使用运行方式帐户针对 Azure 进行身份验证。  

下面的 PowerShell Runbook（即 *Export-RunAsCertificateToHybridWorker*）将运行方式证书从 Azure 自动化帐户导出，并将其下载和导入到混合辅助角色（已连接到同一帐户）上的本地计算机证书存储。  完成该步骤后，就会验证辅助角色能否成功地使用运行方式帐户向 Azure 进行身份验证。

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

将 *Export-RunAsCertificateToHybridWorker* Runbook 保存到装有 `.ps1` 扩展的计算机。  将其导入自动化帐户中，对 Runbook 进行编辑，将变量 `$Password` 的值更改为你自己的密码。  发布并运行该 Runbook，以混合辅助角色组为目标，该组使用运行方式帐户运行 Runbook 并对其进行身份验证。  作业流会报告将证书导入本地计算机存储的尝试，并且随后会附带多个行，具体取决于在订阅中定义了多少自动化帐户，以及身份验证是否成功。  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>为混合 Runbook 辅助角色创建 Runbook
运行在 Azure 自动化中的 Runbook 和运行在混合 Runbook 辅助角色上的 Runbook 没有结构上的区别。 上述两种 Runbook 使用起来可能会有很大差异，因为用于混合 Runbook 辅助角色的 Runbook 通常会管理数据中心的本地资源，而 Azure 自动化中的 Runbook 通常会管理 Azure 云中的资源。

你可以在 Azure 自动化中编辑混合 Runbook 辅助角色的 Runbook，但如果你尝试在编辑器中测试 Runbook，则可能会遇到困难。  用于访问本地资源的 PowerShell 模块可能没有安装在你的 Azure 自动化环境中，这种情况下，测试会失败。  如果你安装了所需的模块，则 Runbook 会运行，但不能访问进行完整测试所需的本地资源。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中排查 Runbook 问题
[Runbook 输出和消息](automation-runbook-output-and-messages.md)将从混合辅助角色发送到 Azure 自动化，就像云中运行的 Runbook 作业一样。  就像在其他 Runbook 中一样，你还可以启用详细流和进度流。  

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。

如果 Runbook 没有成功完成且作业摘要显示的状态为“已暂停”，请参阅故障诊断文章：[混合 Runbook 辅助角色：Runbook 作业以暂停状态终止](automation-troubleshooting-hrw-runbook-terminates-suspended.md)。   

## <a name="relationship-to-service-management-automation"></a>与 Service Management 自动化的关系
使用 [Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) 可以运行与本地数据中心内 Azure 自动化支持的 Runbook 相同的 Runbook。 SMA 通常与 Windows Azure Pack 一起部署，因为 Windows Azure Pack 包含用于管理 SMA 的图形界面。 与 Azure 自动化不同，SMA 需要一个本地安装的程序，其中包括用于托管 API 的 Web 服务器、一个用于包含 Runbook 和 SMA 配置的数据库，以及用于执行 Runbook 作业的 Runbook 辅助角色。 Azure 自动化在云中提供这些服务，只要求你在本地环境中维护混合 Runbook 辅助角色。

如果你已经是 SMA 用户，则可以将 Runbook 移到 Azure 自动化处与混合 Runbook 辅助角色一起使用，不需要进行任何更改，前提是这些 Runbook 向[为混合 Runbook 辅助角色创建 Runbook](#creating-runbooks-for-hybrid-runbook-worker) 中所述的资源进行身份验证。  SMA 中的 Runbook 在辅助角色服务器的服务帐户的上下文中运行，此服务器可以为 Runbook 提供该身份验证。

可以使用以下条件来确定是带有混合 Runbook 辅助角色的 Azure 自动化还是 Service Management 自动化更适合你的要求。

* 如果需要图形管理界面，SMA 要求在本地安装与 Windows Azure Pack 连接的基础组件。 SMA 需要其他一些本地资源，这些资源的维护成本高于 Azure 自动化，后者只需在本地 Runbook 辅助角色中安装一个代理。 代理由 Operations Management Suite 管理，这进一步降低了维护成本。
* Azure 自动化在云中存储其 Runbook，然后将这些 Runbook 传送给本地混合 Runbook 辅助角色。 如果安全策略不允许此行为，则应使用 SMA。
* System Center 随附了 SMA；因此，需要 System Center 2012 R2 的许可证。 Azure 自动化基于分层订阅模型。
* Azure 自动化包含 SMA 所不能提供的一些高级功能，例如图形 Runbook。

## <a name="next-steps"></a>后续步骤
* 若要详细了解其他可用于启动 Runbook 的方法，请参阅[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md)。  
* 若要了解如何通过不同过程使用文本编辑器在 Azure 自动化中处理 PowerShell Runbook 和 PowerShell 工作流 Runbook，请参阅[在 Azure 自动化中编辑 Runbook](automation-edit-textual-runbook.md)

