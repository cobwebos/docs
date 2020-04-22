---
title: 故障排除 Azure 自动化状态配置 （DSC）
description: 本文提供有关对 Azure 自动化状态配置 （DSC） 进行故障排除的信息。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679308"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>解决 Azure 自动化状态配置 （DSC） 问题

本文提供有关在 Azure 自动化状态配置 （DSC） 中编译或部署配置时出现的故障排除问题的信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="diagnosing-an-issue"></a>诊断问题

当您收到配置的编译或部署错误时，下面是帮助您诊断问题的几个步骤。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 确保您的配置在本地计算机上成功编译

Azure 自动化状态配置 （DSC） 基于 PowerShell 所需状态配置 （DSC） 构建。 可以在 [PowerShell DSC 文档](https://docs.microsoft.com/powershell/scripting/overview)中找到 DSC 语言和语法的文档。

通过在本地计算机上编译 DSC 配置，可以发现并解决常见错误，例如：

   - 缺少模块
   - 语法错误
   - 逻辑错误

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在节点上查看 DSC 日志

如果您的配置编译成功，但在应用于节点时失败，则可以在 DSC 日志中找到详细信息。 有关在哪里可以找到这些日志的信息，请参阅[DSC 事件日志在哪里](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[xDsc诊断](https://github.com/PowerShell/xDscDiagnostics)模块可以帮助您分析来自 DSC 日志的详细信息。 如果您联系支持部门，他们需要这些日志来诊断您的问题。

您可以使用`xDscDiagnostics`[安装稳定版本模块](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的说明将模块安装在本地计算机上。

要在`xDscDiagnostics`Azure 计算机上安装该模块，请使用[调用-AzVMRun命令](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0)。 还可以按照 Windows [VM 中的运行 PowerShell 脚本中](../../virtual-machines/windows/run-command.md)的步骤使用 Azure 门户中的 **"运行"命令**选项。

有关使用**xDsc诊断**的信息，请参阅[使用 xDsc诊断来分析 DSC 日志](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另请参阅[xDsc诊断 Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 确保节点和自动化工作区具有所需的模块

DSC 取决于节点上安装的模块。 使用 Azure 自动化状态配置时，请使用导入模块 中的步骤将任何必需[的模块](../shared-resources/modules.md#importing-modules)导入自动化帐户。 配置还可以依赖于模块的特定版本。 有关详细信息，请参阅[故障排除模块](shared-resources.md#modules)。

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>方案：无法从门户中删除具有特殊字符的配置

### <a name="issue"></a>问题

尝试通过门户删除 DSC 配置时，将看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

此错误是计划要解决的临时问题。

### <a name="resolution"></a>解决方法

使用 [删除-AzAutomationDsc配置]（cmdlet）https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0删除配置。

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>方案：无法注册 Dsc 代理

### <a name="issue"></a>问题

当[Set-Dsc 本地配置管理器](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)或其他 DSC cmdlet 时，您会收到错误：

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

### <a name="cause"></a>原因

此错误通常由防火墙、位于代理服务器后面的计算机或其他网络错误导致。

### <a name="resolution"></a>解决方法

验证计算机是否有权访问 DSC 的正确终结点，然后重试。 有关所需端口和地址的列表，请参阅[网络规划](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>方案：状态报告返回响应代码未授权

### <a name="issue"></a>问题

使用 Azure 自动化状态配置注册节点时，您会收到以下错误消息之一：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此问题是由证书损坏或过期引起的。 请参阅[证书过期和重新注册](../automation-dsc-onboarding.md#re-registering-a-node)。

此问题也可能由不允许访问 ***.azure-automation.net**的代理配置引起。 有关详细信息，请参阅[专用网络的配置](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解决方法

使用以下步骤重新注册失败的 DSC 节点。

步骤 1 - 取消注册节点。

1. 在 Azure 门户中，导航到**家庭** -> **自动化帐户**->（您的自动化帐户） ->**状态配置 （DSC）。**
2. 选择**节点**，然后单击遇到故障的节点。
3. 单击 **"取消注册**"以取消注册节点。

步骤 2 - 从节点卸载 DSC 扩展。

1. 在 Azure 门户中，导航到**家庭** -> **虚拟机**->（失败节点） ->**扩展**。
2. 选择**微软.电源shell.DSC，** 电源外壳DSC扩展。
3. 单击 **"卸载**"以卸载扩展。

步骤 3 - 从节点中删除所有损坏或过期的证书。

在来自提升 PowerShell 提示符的故障节点上，运行以下命令：

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

步骤 4 - 重新注册故障节点。

1. 在 Azure 门户中，导航到**家庭** -> **自动化帐户**->（您的自动化帐户） ->**状态配置 （DSC）**
2. 选择**节点**。
3. 单击“添加”  。
4. 选择故障节点。
5. 单击"**连接"** 并选择所需的选项。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

当节点分配给配置名称（例如**ABC，** 而不是节点配置 （MOF 文件） 名称（例如 ABC）时，通常会发生此错误 **。网络服务器**.

### <a name="resolution"></a>解决方法

* 请确保为节点分配节点配置名称，而不是配置名称。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 在 Azure 门户中，导航到**家庭** -> **自动化帐户**->（您的自动化帐户） ->**状态配置 （DSC），** 然后选择一个节点并单击"**分配节点配置**"。
  * 使用[集-阿兹自动化Dnode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) cmdlet。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>方案：编译配置时未生成节点配置（MOF 文件）

### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

当 DSC`Node`配置中关键字之后的表达式计算为`$null`时，不会生成节点配置。

### <a name="resolution"></a>解决方法

使用以下解决方案之一来解决此问题：

* 确保配置定义中`Node`关键字旁边的表达式未计算为 Null。
* 如果在编译配置时传递[配置数据](../automation-dsc-compile.md)，请确保从配置数据传递配置期望的值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>方案：DSC 节点报告陷入"正在进行"状态

### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

### <a name="cause"></a>原因

已升级 Windows 管理框架 （WMF） 版本并损坏了 Windows 管理工具 （WMI）。

### <a name="resolution"></a>解决方法

按照[DSC 中已知的问题和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)中的说明进行操作。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>方案：无法在 DSC 配置中使用凭据

### <a name="issue"></a>问题

您的 DSC 编译作业因错误而挂起：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

您在配置中使用了凭据，但没有为每个节点配置提供正确的`ConfigurationData`设置为`PSDscAllowPlainTextPassword`true。

### <a name="resolution"></a>解决方法

确保将配置中提到的每个节点配置`ConfigurationData`的正确`PSDscAllowPlainTextPassword`状态传递给 true。 请参阅[在 Azure 自动化状态配置中编译 DSC 配置](../automation-dsc-compile.md)。

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>方案："从 DSC 扩展载入时"失败处理扩展"错误

### <a name="issue"></a>问题

使用 DSC 扩展载入时，将发生包含错误的失败：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

当为节点分配了服务中不存在的节点配置名称时，通常会出现此错误。

### <a name="resolution"></a>解决方法

* 请确保为节点分配的名称与服务中的名称完全匹配。
* 您可以选择不包括节点配置名称，这将导致节点入入，但不分配节点配置。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>方案："使用 PowerShell 注册节点时发生一个或多个错误"错误

### <a name="issue"></a>问题

使用[寄存器-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0)或[寄存器-Azure-Azure 自动化DSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)注册节点时，您会收到以下错误：

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

当您尝试将节点注册到与自动化帐户使用的单独订阅中的节点时，将发生此错误。

### <a name="resolution"></a>解决方法

将跨订阅节点视为为单独的云或本地定义的节点。 使用以下载入选项之一注册节点：

* Windows -[本地或 Azure/AWS 以外的云中的物理/虚拟 Windows 计算机](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)。
* Linux -[本地或 Azure 以外的云中的物理/虚拟 Linux 计算机](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)。

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>方案：错误消息 - "预配失败"

### <a name="issue"></a>问题

注册节点时，您会看到错误：

```error
Provisioning has failed
```

### <a name="cause"></a>原因

当节点和 Azure 之间的连接出现问题时，将发生此消息。

### <a name="resolution"></a>解决方法

确定节点是否位于虚拟专用网络 （VPN） 中，还是连接到 Azure 时还有其他问题。 请参阅[在加入解决方案时排除故障错误](onboarding.md)。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>方案：在 Linux 中应用配置时失败，出现常规错误

### <a name="issue"></a>问题

在 Linux 中应用配置时，会出现包含以下错误的故障：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

如果 **/tmp**位置设置为`noexec`，则当前版本的 DSC 无法应用配置。

### <a name="resolution"></a>解决方法

从`noexec`**/tmp**位置删除该选项。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>方案：重叠的节点配置名称可能会导致发布错误

### <a name="issue"></a>问题

当您使用单个配置脚本生成多个节点配置，并且某些节点配置名称是其他名称的子集时，编译服务最终可能会分配错误的配置。 仅当使用单个脚本生成每个节点具有配置数据的配置时，并且仅当名称重叠发生在字符串的开头时，才会出现此问题。 例如，单个配置脚本用于根据使用 cmdlet 作为哈希表传递的节点数据生成配置，节点数据包括名为**服务器**的服务器和 1 服务器的**服务器**。

### <a name="cause"></a>原因

这是编译服务的已知问题。

### <a name="resolution"></a>解决方法

最佳解决方法是在本地或 CI/CD 管道中编译，并将节点配置 MOF 文件直接上载到服务。 如果服务中的编译是必需的，则下一个最佳解决方法是拆分编译作业，以便在名称中没有重叠。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>方案：DSC 配置上载上的网关超时错误

#### <a name="issue"></a>问题

上传 DSC 配置时收到`GatewayTimeout`错误。 

### <a name="cause"></a>原因

编译时间过长的 DSC 配置可能会导致此错误。

### <a name="resolution"></a>解决方法

通过显式包括任何导入`ModuleName`[DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)调用的参数，可以使 DSC 配置解析得更快。

## <a name="next-steps"></a>后续步骤

如果您在上面看不到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。