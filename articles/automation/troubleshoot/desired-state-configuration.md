---
title: Azure 自动化 Desired State Configuration (DSC) 问题疑难解答
description: 本文提供有关 Desired State Configuration (DSC) 疑难解答的信息
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6de348a19081eba685deafebd8a7c9b9d6556444
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688110"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) 疑难解答

本文提供有关 Desired State Configuration (DSC) 问题疑难解答的信息。

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>排除所需状态配置 (DSC) 的步骤

如果在 Azure 状态配置中编译或部署配置时出现错误, 下面提供了一些步骤来帮助你诊断问题。

1. **确保你的配置在本地计算机上成功编译:** Azure 状态配置是在 PowerShell DSC 上构建的。 可以在[POWERSHELL Dsc 文档](/powershell/dsc/overview/overview)中找到 DSC 语言和语法的文档。

   通过在本地计算机上编译 DSC 配置, 可以发现和解决常见错误, 如:

   - **缺少模块**
   - **语法错误**
   - **逻辑错误**
2. **查看节点上的 DSC 日志:** 如果配置成功编译, 但应用于节点时失败, 则可以在日志中找到详细信息。 有关在何处查找 DSC 日志的信息, 请参阅[Dsc 事件日志的位置](/powershell/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

   Futhermore, [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)可以帮助你分析 DSC 日志中的详细信息。 如果你联系支持人员, 他们将需要这些日志来 dianose 你的问题。

   可以按照[安装稳定版本模块](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的说明在本地计算机上安装**xDscDiagnostics** 。

   若要在 Azure 计算机上安装**xDscDiagnostics** , 可以使用[az vm run-command](/cli/azure/vm/run-command)或[AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 还可以按照在[WINDOWS VM 中运行 PowerShell 脚本和运行命令](../../virtual-machines/windows/run-command.md)中的步骤, 使用门户中的 "**运行" 命令**选项。

   有关使用**xDscDiagnostics**的信息, 请参阅[使用 xDscDiagnostics 分析 DSC 日志](/powershell/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)和[xDscDiagnostics cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。
3. **确保节点和自动化工作区具有所需的模块:** Desired State Configuration 依赖于节点上安装的模块。  使用 Azure 自动化状态配置时, 请使用[导入模块](../shared-resources/modules.md#import-modules)中列出的步骤将任何所需的模块导入到自动化帐户中。 配置还可以依赖于特定版本的模块。  有关详细信息, 请参阅[排查模块问题](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用所需状态配置 (DSC) 时的常见错误

### <a name="unsupported-characters"></a>场景：无法从门户删除带有特殊字符的配置

#### <a name="issue"></a>问题

尝试通过门户删除 DSC 配置时，将看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此错误是计划要解决的临时问题。

#### <a name="resolution"></a>解决

* 使用 Az Cmdlet "Remove-AzAutomationDscConfiguration" 删除配置。
* 此 cmdlet 的文档尚未更新。  在其更新前，请参考 AzureRM 模块的文档。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>场景：未能注册 Dsc 代理

#### <a name="issue"></a>问题

尝试运行`Set-DscLocalConfigurationManager`或其他 DSC cmdlet 时, 收到错误:

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

此错误通常是由防火墙导致, 计算机位于代理服务器后面或其他网络错误。

#### <a name="resolution"></a>解决

验证你的计算机是否可以访问适用于 Azure Automation DSC 的正确终结点, 然后重试。 有关所需端口和地址的列表, 请参阅[网络规划](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

#### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。

#### <a name="resolution"></a>解决

* 确保要为节点分配“节点配置名称”，而不是“配置名称”。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 若要使用 Azure 门户将节点配置分配给节点，请打开“DSC 节点”页，然后选择一个节点，并单击“分配节点配置”按钮。
  * 若要使用 PowerShell cmdlet 将节点配置分配给节点，请使用 **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>场景：编译配置时未生成节点配置（MOF 文件）

#### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

如果 DSC 配置中“Node”关键字后面的表达式的计算结果为 `$null`，则不会生成节点配置。

#### <a name="resolution"></a>解决

下述解决方案中的任何一种都可以解决此问题：

* 确保配置定义中 **Node** 关键字旁边的表达式的计算结果不为 $null。
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](../automation-dsc-compile.md#configurationdata) 传递配置需要的预期值。

### <a name="dsc-in-progress"></a>场景：DSC 节点报告卡在了“正在进行”状态

#### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

已升级 WMF 版本，已损坏 WMI。

#### <a name="resolution"></a>解决

若要解决此问题，请按照 [DSC 已知问题和限制](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)一文中的说明进行操作。

### <a name="issue-using-credential"></a>场景：无法在 DSC 配置中使用凭据

#### <a name="issue"></a>问题

DSC 编译作业已暂停，且出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

已在配置中使用凭据，但未提供正确的 **ConfigurationData**，从而无法将每个节点配置的 **PSDscAllowPlainTextPassword** 设置为 true。

#### <a name="resolution"></a>解决

* 确保传入正确的 **ConfigurationData**，以便将配置中涉及的每个节点配置的 **PSDscAllowPlainTextPassword** 设置为 true。 有关详细信息，请参阅 [Azure 自动化 DSC 中的资产](../automation-dsc-compile.md#assets)。

### <a name="failure-processing-extension"></a>场景：从 dsc 扩展载入时，出现“处理扩展失败”错误

#### <a name="issue"></a>问题

使用 DSC 扩展载入时失败，出现以下错误：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

当为节点分配了服务中不存在的节点配置名称时，通常会出现此错误。

#### <a name="resolution"></a>解决

* 确保要为节点分配的节点配置名称与服务中的名称完全匹配。
* 可以选择不包含节点配置名称，这将导致载入节点，而不分配节点配置

### <a name="failure-linux-temp-noexec"></a>场景：在 Linux 中应用配置时出现故障，并显示一般错误

#### <a name="issue"></a>问题

在 Linux 中应用配置时，会出现包含以下错误的故障：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客户已确定，如果 /tmp 位置设置为 noexec，则当前版本的 DSC 将无法应用配置。

#### <a name="resolution"></a>解决

* 从 /tmp 位置中删除 noexec 选项。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
