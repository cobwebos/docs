---
title: 排除 Azure 自动化所需状态配置 （DSC） 的故障
description: 本文提供有关 Desired State Configuration (DSC) 疑难解答的信息
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aeffa0bb736f03403bf483b22775ef468bbcb2bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405461"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>解决 Azure 自动化所需状态配置 （DSC） 的问题

本文提供有关 Desired State Configuration (DSC) 问题疑难解答的信息。

## <a name="diagnosing-an-issue"></a>诊断问题

在 Azure 状态配置中编译或部署配置时出错时，下面是帮助您诊断问题的几个步骤。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 确保您的配置在本地计算机上成功编译

Azure State Configuration 在 PowerShell DSC 基础上构建。 可以在 [PowerShell DSC 文档](https://docs.microsoft.com/powershell/scripting/overview)中找到 DSC 语言和语法的文档。

通过在本地计算机上编译 DSC 配置，可以发现并解决常见错误，例如：

   - 缺少模块
   - 语法错误
   - 逻辑错误

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在节点上查看 DSC 日志

如果您的配置编译成功，但在应用于节点时失败，则可以在 DSC 日志中找到详细信息。 有关在哪里可以找到这些日志的信息，请参阅[DSC 事件日志在哪里](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[xDsc诊断](https://github.com/PowerShell/xDscDiagnostics)模块可以帮助您分析来自 DSC 日志的详细信息。 如果您联系支持部门，他们需要这些日志来诊断您的问题。

您可以使用[安装稳定版本模块](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的说明在本地计算机上安装 xDsc诊断模块。

要在 Azure 计算机上安装 xDsc诊断模块，请使用[Invoke-AzVMRun命令](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 也可在门户中使用“运行命令”选项，**** 只需按照[使用“运行命令”在 Windows VM 中运行 PowerShell 脚本](../../virtual-machines/windows/run-command.md)中的步骤操作即可。

有关使用 xDsc诊断的信息，请参阅[使用 xDsc诊断来分析 DSC 日志](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另请参阅[xDsc诊断 Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 确保节点和自动化工作区具有所需的模块

DSC 取决于节点上安装的模块。 使用 Azure 自动化状态配置时，请使用导入模块 中列出的步骤将任何必需[的模块](../shared-resources/modules.md#importing-modules)导入自动化帐户。 配置还可以依赖于模块的特定版本。 有关详细信息，请参阅[故障排除模块](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-dsc"></a>使用 DSC 时的常见错误

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>方案：无法从门户中删除具有特殊字符的配置

#### <a name="issue"></a>问题

尝试通过门户删除 DSC 配置时，将看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此错误是计划要解决的临时问题。

#### <a name="resolution"></a>解决方法

* 使用 Az Cmdlet "Remove-AzAutomationDscConfiguration" 删除配置。
* 此 cmdlet 的文档尚未更新。  在其更新前，请参考 AzureRM 模块的文档。
  * [删除 AzureRm 自动化DSC配置](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>方案：无法注册 Dsc 代理

#### <a name="issue"></a>问题

尝试运行 `Set-DscLocalConfigurationManager` 或其他 DSC cmdlet 时，会收到错误：

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>原因

此错误通常由防火墙、位于代理服务器后面的计算机或其他网络错误导致。

#### <a name="resolution"></a>解决方法

请验证计算机是否可以访问 Azure Automation DSC 的相应终结点，然后重试。 有关所需端口和地址的列表，请参阅[网络规划](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>方案：状态报告返回响应代码"未授权"

#### <a name="issue"></a>问题

使用状态配置 （DSC） 注册节点时，您会收到以下错误消息之一：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此问题是由证书损坏或过期引起的。  有关详细信息，请参阅[证书过期和重新注册](../automation-dsc-onboarding.md#re-register-a-node)。

此问题也可能由不允许访问 ***.azure-automation.net**的代理配置引起。 有关详细信息，请参阅[专用网络的配置](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解决方法

按照下面列出的步骤重新注册失败的 DSC 节点。

首先，使用以下步骤取消注册节点。

1. 从 Azure 门户，**在家庭** -> **自动化帐户**下 -> [您的自动化帐户] ->**状态配置 （DSC）**
2. 单击"节点"，然后单击遇到故障的节点。
3. 单击"取消注册"以取消注册节点。

其次，从节点卸载 DSC 扩展。

1. 从 Azure 门户，**在家庭** -> **虚拟机**-> [失败节点] ->**扩展**
2. 单击"微软.电源外壳.DSC"。
3. 单击"卸载"，卸载 PowerShell DSC 扩展。

第三，从节点中删除所有损坏或过期的证书。

在提升的 Powershell 提示器中的故障节点上，运行以下操作：

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

最后，使用以下步骤重新注册故障节点。

1. 从 Azure 门户，**在家庭** -> **自动化帐户**下 -> [您的自动化帐户] ->**状态配置 （DSC）**
2. 单击"节点"。
3. 单击"添加"按钮。
4. 选择故障节点。
5. 单击"连接"，然后选择所需的选项。

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

#### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误****：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。

#### <a name="resolution"></a>解决方法

* 确保要为节点分配“节点配置名称”，而不是“配置名称”。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 若要使用 Azure 门户将节点配置分配给节点，请打开“DSC 节点”页，然后选择一个节点，并单击“分配节点配置”按钮********。
  * 若要使用 PowerShell cmdlet 将节点配置分配给节点，请使用 **Set-AzureRmAutomationDscNode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>方案：编译配置时未生成节点配置（MOF 文件）

#### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

如果 DSC 配置中“Node”关键字后面的表达式的计算结果为 `$null`，则不会生成节点配置****。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 确保配置定义中 **Node** 关键字旁边的表达式的计算结果不为 $null。
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](../automation-dsc-compile.md) 传递配置需要的预期值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>场景：DSC 节点报告卡在了“正在进行”状态

#### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

已升级 WMF 版本，已损坏 WMI。

#### <a name="resolution"></a>解决方法

要解决此问题，请按照[DSC 已知问题和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)文章中的说明进行操作。

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>方案：无法在 DSC 配置中使用凭据

#### <a name="issue"></a>问题

DSC 编译作业已暂停，且出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

您在配置中使用了凭据，但没有提供适当的**配置数据**来为每个节点配置设置**PSDscAllowPlainTextPassword**为 true。

#### <a name="resolution"></a>解决方法

* 确保传入正确的 **ConfigurationData**，以便将配置中涉及的每个节点配置的 **PSDscAllowPlainTextPassword** 设置为 true。 有关详细信息，请参阅在[Azure 自动化状态配置中编译 DSC 配置](../automation-dsc-compile.md)。

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>方案：从 dsc 扩展载入，"失败处理扩展"错误

#### <a name="issue"></a>问题

使用 DSC 扩展载入时失败，出现以下错误：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

当为节点分配了服务中不存在的节点配置名称时，通常会出现此错误。

#### <a name="resolution"></a>解决方法

* 确保要为节点分配的节点配置名称与服务中的名称完全匹配。
* 可以选择不包含节点配置名称，这将导致载入节点，而不分配节点配置

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>方案：使用 PowerShell 注册节点将返回错误"发生一个或多个错误"

#### <a name="issue"></a>问题

使用`Register-AzAutomationDSCNode`或`Register-AzureRMAutomationDSCNode`注册节点时，您会收到以下错误。

```error
One or more errors occurred.
```

#### <a name="cause"></a>原因

当您尝试注册与自动化帐户分开的订阅中的节点时，将发生此错误。

#### <a name="resolution"></a>解决方法

将跨订阅节点视为位于单独的云中或本地。

按照以下步骤注册节点。

* Windows -[本地或 Azure/AWS 以外的云中的物理/虚拟 Windows 计算机](../automation-dsc-onboarding.md#onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure)。
* Linux -[本地或 Azure 以外的云中的物理/虚拟 Linux 计算机](../automation-dsc-onboarding.md#onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)。

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>方案：错误消息 - "预配失败"

#### <a name="issue"></a>问题

注册节点时，您会看到错误：

```error
Provisioning has failed
```

#### <a name="cause"></a>原因

当节点和 Azure 之间存在连接问题时，将发生此消息。

#### <a name="resolution"></a>解决方法

确定节点是否位于专用虚拟网络中，还是连接到 Azure 时还有其他问题。

有关详细信息，请参阅[在加入解决方案时排除错误](onboarding.md)。

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>方案：在 Linux 中应用配置，失败时会出现一般错误

#### <a name="issue"></a>问题

在 Linux 中应用配置时，会出现包含以下错误的故障：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客户已确定，如果 `/tmp` 位置设置为 `noexec`，则当前版本的 DSC 将无法应用配置。

#### <a name="resolution"></a>解决方法

* 从 `/tmp` 位置中删除 `noexec` 选项。

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>方案：重叠的节点配置名称可能会导致发布错误

#### <a name="issue"></a>问题

如果使用单个配置脚本来生成多个节点配置，而某些节点配置的名称是其他名称的子集，则编译服务出问题可能导致分配的配置错误。  这只发生在使用单个脚本来生成配置且每个节点都有配置数据的情况下，并且仅发生在字符串开头出现名称重叠的情况下。

例如，如果在使用单个配置脚本生成配置时，根据的是使用 cmdlet 作为哈希表传递的节点数据，且节点数据包含名为“server”和“1server”的服务器，则可能会发生上述错误。

#### <a name="cause"></a>原因

编译服务的已知问题。

#### <a name="resolution"></a>解决方法

最佳解决方法将是在本地或 CI/CD 管道中进行编译，然后将 MOF 文件直接上传到服务。  如果必须在服务中进行编译，则较佳解决方法将是拆分编译作业，这样就不会发生名称重叠现象。

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>方案：DSC 配置上载上的网关超时错误

#### <a name="issue"></a>问题

上传 DSC 配置时收到`GatewayTimeout`错误。 

#### <a name="cause"></a>原因

编译时间过长的 DSC 配置可能会导致此错误。

#### <a name="resolution"></a>解决方法

通过显式包含任何`ModuleName``Import-DscResource`调用的参数，可以使 DSC 配置解析得更快。 有关详细信息，请参阅[使用导入 DSC 资源](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
