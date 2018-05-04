---
title: Azure 自动化混合 Runbook 辅助角色
description: 本文介绍如何安装和使用混合 Runbook 辅助角色，该角色是 Azure 自动化的一项功能，可以用于在本地数据中心或云提供商的计算机上运行 Runbook。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7065ec97e1e02dfb4ee873993caac584f6a63ba6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>使用混合 Runbook 辅助角色使数据中心或云端的资源实现自动化

Azure 自动化中的 Runbook 无法访问其他云或本地环境中的资源，因为它们在 Azure 云中运行。 利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。

下图说明了此功能：

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

有关混合 Runbook 辅助角色的技术概述和部署注意事项，请参阅[自动化体系结构概述](automation-offering-get-started.md#automation-architecture-overview)。

## <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组

每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。 一个组可以包含一个代理，但是可以在一个组中安装多个代理，以实现高可用性。

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。 组的成员会决定由哪个辅助角色来处理请求。 不能指定特定的辅助角色。

## <a name="relationship-to-service-management-automation"></a>与 Service Management 自动化的关系

使用 [Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) 可以运行与本地数据中心内 Azure 自动化支持的 Runbook 相同的 Runbook。 SMA 与 Windows Azure Pack 一起部署，因为 Microsoft Azure Pack 包含用于管理 SMA 的图形界面。 与 Azure 自动化不同，SMA 需要一个本地安装的程序，其中包括用于托管 API 的 Web 服务器、一个用于包含 Runbook 和 SMA 配置的数据库，以及用于执行 Runbook 作业的 Runbook 辅助角色。 Azure 自动化在云中提供这些服务，只要求在本地环境中维护混合 Runbook 辅助角色。

如果已经是 SMA 用户，则可以将 Runbook 移到 Azure 自动化处与混合 Runbook 辅助角色一起使用，不需要进行任何更改，但前提是这些 Runbook 对[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md) 中所述的资源执行自己的身份验证。 SMA 中的 Runbook 在辅助角色服务器的服务帐户的上下文中运行，此服务器可以为 Runbook 提供该身份验证。

可以使用以下条件来确定是带有混合 Runbook 辅助角色的 Azure 自动化还是 Service Management 自动化更适合要求。

* 如果需要图形管理界面，SMA 要求在本地安装与 Windows Azure Pack 连接的基础组件。 SMA 需要其他一些本地资源，这些资源的维护成本高于 Azure 自动化，后者只需在本地 Runbook 辅助角色中安装一个代理。 代理由 Azure 管理，这进一步降低了维护成本。
* Azure 自动化在云中存储其 Runbook，并将这些 Runbook 传送给本地混合 Runbook 辅助角色。 如果安全策略不允许此行为，则应使用 SMA。
* System Center 随附了 SMA；因此，需要 System Center 2012 R2 的许可证。 Azure 自动化基于分层订阅模型。
* Azure 自动化包含 SMA 所不能提供的一些高级功能，例如图形 Runbook。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>安装 Windows 混合 Runbook 辅助角色

若要安装和配置 Windows 混合 Runbook 辅助角色，可以使用两种方法。 建议的方法是使用自动化 Runbook 来彻底实现配置 Windows 计算机所需的过程的自动化。 第二种方法使用分步过程来手动安装和配置角色。

> [!NOTE]
> 为了使用所需状态配置 (DSC) 管理支持混合 Runbook 辅助角色的服务器配置，需将其添加为 DSC 节点。 若要进一步了解如何载入它们以供 DSC 管理，请参阅[载入由 Azure 自动化 DSC 管理的计算机](automation-dsc-onboarding.md)。
>
>如果启用[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)，任何连接到 Log Analytics 工作区的 Windows 计算机将自动配置为混合 Runbook 辅助角色，以支持此解决方案中包括的 Runbook。 但是，该计算机未注册到任何已在自动化帐户中定义的混合辅助角色组。 只要将同一个帐户同时用于解决方案和混合 Runbook 辅助角色组成员身份，即可将该计算机添加到自动化帐户的混合 Runbook 辅助角色组，以支持自动化 Runbook。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

开始部署混合 Runbook 辅助角色之前，请先查看以下信息：[硬件和软件要求](automation-offering-get-started.md#hybrid-runbook-worker)以及[网络准备相关信息](automation-offering-get-started.md#network-planning)。 成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

### <a name="automated-deployment"></a>自动化部署

执行以下步骤，以便自动完成 Windows 混合辅助角色的安装和配置。

1. 直接从运行混合 Runbook 辅助角色的计算机或环境中的其他计算机的 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript)下载 New-OnPremiseHybridWorker.ps1 脚本，并将其复制到辅助角色。

   在执行期间，New-OnPremiseHybridWorker.ps1 脚本需要以下参数：

   * AutomationAccountName（必需）- 自动化帐户的名称。
   * AAResourceGroupName（必需）- 与自动化帐户关联的资源组的名称。
   * OMSResourceGroupName（可选）- OMS 工作区的资源组名称。 如果未指定，将使用 AAResourceGroupName。
   * HybridGroupName（必需）- 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 Runbook 的目标。
   * SubscriptionID（必需）- 包含自动化帐户的 Azure 订阅 ID。
   * WorkspaceName（可选）- Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。

     > [!NOTE]
     > 目前可与 Log Analytics 集成的自动化区域仅限：澳大利亚东南部、美国东部 2、东南亚以及西欧。 如果自动化帐户不在其中的某个区域，脚本会创建 Log Analytics 工作区，但会警告用户：无法将这些区域链接到一起。

2. 在计算机的“管理员”模式下，从“开始”屏幕启动 **Windows PowerShell**。
3. 从 PowerShell 命令行 shell 中，导航到已下载脚本所在的文件夹并执行该脚本，请记得更改 -AutomationAccountName、-AAResourceGroupName、-OMSResourceGroupName、-HybridGroupName、-SubscriptionId 和 -WorkspaceName 参数的值。

     > [!NOTE]
     > 执行脚本后，系统会提示在 Azure 上进行身份验证。 **必须**以订阅管理员角色成员和订阅共同管理员的帐户登录。

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. 系统会提示用户同意安装 **NuGet** 并使用 Azure 凭据进行身份验证。

5. 脚本完成后，“混合辅助角色组”边栏选项卡会显示新组和成员数，或者会将成员数进行递增（如果组已存在）。 可以从“混合辅助角色组”边栏选项卡上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”边栏选项卡上，会列出组的每个成员。

### <a name="manual-deployment"></a>手动部署

针对自动化环境执行前两个步骤一次，并对每台辅助角色计算机重复其余步骤。

#### <a name="1-create-log-analytics-workspace"></a>1.创建 Log Analytics 工作区

如果尚无 Log Analytics 工作区，请按照[管理工作区](../log-analytics/log-analytics-manage-access.md)中的说明创建工作区。 如果已经有一个工作区，则可以使用现有的。

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2.向 Log Analytics 工作区添加自动化解决方案

解决方案向 Log Analytics 添加功能。 自动化解决方案增加 Azure 自动化的功能，包括支持混合 Runbook 辅助角色。 将解决方案添加到工作区时，它会自动将辅助角色组件往下推送到在下一步要安装的代理计算机。

请根据[使用解决方案库添加解决方案](../log-analytics/log-analytics-add-solutions.md)中的说明，将**自动化**解决方案添加到 Log Analytics 工作区。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安装 Microsoft Monitoring Agent

Microsoft Monitoring Agent 可将计算机连接到 Log Analytics。 在计算机本地安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

按照[将 Windows 计算机连接到 Log Analytics](../log-analytics/log-analytics-windows-agent.md) 中的说明在本地计算机上安装代理。 可以对多台计算机重复此过程，以将多个辅助角色添加到环境。

当 C:\Program Files\Microsoft Monitoring Agent\Agent 中出现名为 **AzureAutomationFiles** 的文件夹时，可确认代理已正确下载自动化解决方案。 若要确认混合 Runbook 辅助角色的版本，可以导航到 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ 并留意 \\*version* 子文件夹。  

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安装 Runbook 环境并连接到 Azure 自动化

将代理添加到 Log Analytics 时，自动化解决方案会向下推送 **HybridRegistration** PowerShell 模块，其中包含 **Add-HybridRunbookWorker** cmdlet。 使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

若要导入模块，请在管理员模式下打开 PowerShell 会话，并运行以下命令：

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

然后，请使用以下语法运行 **Add-HybridRunbookWorker** cmdlet：

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

可以通过在自动化帐户中单击“帐户设置”下的“密钥”选项获取此 cmdlet 所需的信息。

* **GroupName** 是混合 Runbook 辅助角色组的名称。 如果该组已经存在于自动化帐户中，则会将当前计算机添加到其中。 如果该组不存在，则会创建它。
* “终结点”是“密钥”页中的“URL”字段。
* “令牌”是指“密钥”页中的“主访问密钥”。

使用包含 **Add-HybridRunbookWorker** 的 **-Verbose** 开关可接收有关安装的详细信息。

#### <a name="5-install-powershell-modules"></a>5.安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 不过，这些模块不会自动部署到本地计算机，因此必须手动安装。 例外情况是 Azure 模块，该模块是默认安装的，可以用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色功能的主要用途是管理本地资源，很可能需要安装支持这些资源的模块。 可以参考 [Installing Modules](http://msdn.microsoft.com/library/dd878350.aspx)（安装模块），获取有关安装 Windows PowerShell 模块的信息。 安装的模块必须位于 PSModulePath 环境变量所引用的位置，以便混合辅助角色自动将其导入。 有关详细信息，请参阅 [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)（修改 PSModulePath 安装路径）。

## <a name="removing-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

可以从组中删除一个或多个混合 Runbook 辅助角色，或者根据要求删除该组。 若要从本地计算机中删除混合 Runbook 辅助角色，请执行以下步骤：

1. 在 Azure 门户中，导航到自动化帐户。
2. 在“设置”边栏选项卡中，选择“密钥”并记下“URL”和“主访问密钥”字段的值。 下一步需要用到此信息。
3. 在管理员模式下打开 PowerShell 会话，并运行以下命令 - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`。 可使用 **-Verbose** 开关获取删除过程的详细日志。

若要从混合辅助角色组中删除过时的计算机，请使用可选的 `machineName` 参数。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

> [!NOTE]
> 这不会从计算机中删除 Microsoft 监视代理，而只会删除混合 Runbook 辅助角色的功能和配置。

## <a name="remove-hybrid-worker-groups"></a>删除混合辅助角色组

要删除某个组，首先需要使用前面所示的过程，从每台计算机中删除属于该组的混合 Runbook 辅助角色，然后执行以下步骤删除该组。

1. 在 Azure 门户中打开自动化帐户。
1. 在“流程自动化”下选择“混合辅助角色组”。 选择要删除的组。 在选择特定的组之后，会显示“混合辅助角色组”属性边栏选项卡。

   ![混合 Runbook 辅助角色组边栏选项卡](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 在所选组的属性边栏选项卡中，单击“删除”。 此时会显示一条消息请求确认此操作，如果确定要继续，请选择“是”。

   ![确认删除组对话框](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。

## <a name="troubleshooting"></a>故障排除

混合 Runbook 辅助角色依靠 Microsoft Monitoring Agent 与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

1. 混合辅助角色在代理或防火墙后面。

   确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

2. 运行混合辅助角色的计算机不满足最低硬件[要求](automation-offering-get-started.md#hybrid-runbook-worker)。

   运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，才能指定它托管此功能。 否则，在执行过程中，根据其他后台进程的资源使用率和 runbook 所导致的争用，该计算机将变为过度使用，从而导致 runbook 作业延迟或超时。

   确认指定为运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存利用率，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 如果存在内存或 CPU 压力，这可能指示需要升级或添加额外的处理器或增加内存来解决资源瓶颈问题，从而解决此错误。 或者，选择其他可支持最低要求的计算资源，并在工作负荷需求指示需要增加时进行扩展。

3. Microsoft Monitoring Agent 服务未运行。

   如果 Microsoft Monitoring Agent Windows 服务未运行，会导致混合 Runbook 辅助角色无法与 Azure 自动化通信。 在 PowerShell 中输入以下命令，验证代理是否正在运行：`get-service healthservice`。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`start-service healthservice`。

4. 在 Application and Services Logs\Operations Manager 事件日志中，可看到事件 4502、包含 Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent 的 EventMessage 以及下列描述：服务 \<wsid\>.oms.opinsights.azure.com 提供的证书不是由 Microsoft 服务使用的证书颁发机构颁发的*。请联系网络管理员以查看其是否正在运行截获 TLS/SSL 通信的代理。KB3126513 一文还介绍了关于连接问题的其他故障排除信息。*
    这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 可以检查 **Application and Services Logs\Microsoft-SMA\Operations** 和 **Application and Services Logs\Operations Manager** 事件日志中是否写入了任何警告或错误事件，指示出现了影响角色载入 Azure 自动化的连接问题或其他问题，或者在执行正常操作时出现问题。

## <a name="next-steps"></a>后续步骤

查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。
