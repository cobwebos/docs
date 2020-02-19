---
title: Azure 自动化所需状态配置（DSC）疑难解答
description: 本文提供有关 Desired State Configuration (DSC) 疑难解答的信息
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430527"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>排查 Azure 自动化所需状态配置（DSC）的问题

本文提供有关 Desired State Configuration (DSC) 问题疑难解答的信息。

## <a name="diagnosing-an-issue"></a>诊断问题

如果在 Azure 状态配置中编译或部署配置时出现错误，下面提供了一些步骤来帮助你诊断问题。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 确保你的配置在本地计算机上成功编译

Azure 状态配置是在 PowerShell DSC 上构建的。 可以在[POWERSHELL Dsc 文档](https://docs.microsoft.com/powershell/scripting/overview)中找到 DSC 语言和语法的文档。

通过在本地计算机上编译 DSC 配置，可以发现和解决常见错误，如：

   - 缺少模块
   - 语法错误
   - 逻辑错误

### <a name="2-view-dsc-logs-on-your-node"></a>2. 查看节点上的 DSC 日志

如果配置成功编译，但在应用于节点时失败，则可以在 DSC 日志中找到详细信息。 有关在何处查找这些日志的信息，请参阅[DSC 事件日志的位置](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)模块可以帮助你分析 DSC 日志中的详细信息。 如果联系支持人员，则需要这些日志来诊断问题。

可以按照[安装稳定版本模块](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的说明在本地计算机上安装 xDscDiagnostics 模块。

若要在 Azure 计算机上安装 xDscDiagnostics 模块，请使用[AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 还可以按照在[WINDOWS VM 中运行 PowerShell 脚本和运行命令](../../virtual-machines/windows/run-command.md)中的步骤，使用门户中的 "**运行" 命令**选项。

有关使用 xDscDiagnostics 的信息，请参阅[使用 xDscDiagnostics 分析 DSC 日志](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另请参阅[XDscDiagnostics cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 确保节点和自动化工作区具有所需的模块

DSC 依赖于节点上安装的模块。 使用 Azure 自动化状态配置时，请使用[导入模块](../shared-resources/modules.md#import-modules)中列出的步骤将任何所需的模块导入到自动化帐户中。 配置还可以依赖于特定版本的模块。 有关详细信息，请参阅[模块故障排除](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-dsc"></a>使用 DSC 时的常见错误

### <a name="unsupported-characters"></a>方案：无法从门户中删除包含特殊字符的配置

#### <a name="issue"></a>问题

尝试从门户中删除 DSC 配置时，会看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此错误是计划解决的暂时性问题。

#### <a name="resolution"></a>解决方法

* 使用 Az Cmdlet "AzAutomationDscConfiguration" 删除配置。
* 尚未更新此 cmdlet 的文档。  在此之前，请参阅 AzureRM 模块的文档。
  * [Export-azurermautomationdscconfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>方案：未能注册 Dsc 代理

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

此错误通常是由防火墙导致，计算机位于代理服务器后面或其他网络错误。

#### <a name="resolution"></a>解决方法

验证你的计算机是否可以访问适用于 Azure Automation DSC 的正确终结点，然后重试。 有关所需端口和地址的列表，请参阅[网络规划](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>方案：状态报告返回响应代码 "未授权"

#### <a name="issue"></a>问题

当注册具有状态配置（DSC）的节点时，会收到以下错误消息之一：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此问题是由证书损坏或过期引起的。  有关详细信息，请参阅[证书过期和重新注册](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration)。

### <a name="resolution"></a>解决方法

请按照下面列出的步骤重新注册失败的 DSC 节点。

首先，使用以下步骤取消注册该节点。

1. 在 Azure 门户中，在 "**家庭** -> **自动化**帐户" 下，> {你的自动化帐户}->**状态配置（DSC）**
2. 单击 "节点"，然后单击有问题的节点。
3. 单击 "取消注册" 以取消注册该节点。

其次，从节点中卸载 DSC 扩展。

1. 在 Azure 门户中，在 "**主页**" 下 -> **虚拟机**-> {失败的节点}->**扩展**
2. 单击 ""。
3. 单击 "卸载" 以卸载 PowerShell DSC 扩展。

第三，从节点中删除所有错误或过期的证书。

在权限提升的 Powershell 提示符下，运行以下命令：

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

最后，使用以下步骤重新注册失败的节点。

1. 在 Azure 门户中，在 "**家庭** -> **自动化**帐户" 下，> {你的自动化帐户}->**状态配置（DSC）**
2. 单击 "节点"。
3. 单击 "添加" 按钮。
4. 选择 "失败" 节点。
5. 单击 "连接"，然后选择所需的选项。

### <a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

#### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。

#### <a name="resolution"></a>解决方法

* 请确保为节点分配 "节点配置名称"，而不是 "配置名称"。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 若要使用 Azure 门户将节点配置分配给节点，请打开 " **DSC 节点**" 页，然后选择一个节点，然后单击 "**分配节点配置**" 按钮。
  * 若要使用 PowerShell cmdlet 将节点配置分配给节点，请使用**unregister-azurermautomationdscnode** cmdlet

### <a name="no-mof-files"></a>场景：编译配置时未生成节点配置（MOF 文件）

#### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

如果 DSC 配置中“Node”关键字后面的表达式的计算结果为 **，则不会生成节点配置**`$null`。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 确保配置定义中**Node**关键字旁边的表达式的计算结果不是 $null。
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](../automation-dsc-compile.md) 传递配置需要的预期值。

### <a name="dsc-in-progress"></a>场景：DSC 节点报告卡在了“正在进行”状态

#### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

已升级 WMF 版本，已损坏 WMI。

#### <a name="resolution"></a>解决方法

若要解决此问题，请按照[DSC 已知问题和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)一文中的说明进行操作。

### <a name="issue-using-credential"></a>场景：无法在 DSC 配置中使用凭据

#### <a name="issue"></a>问题

DSC 编译作业已暂停，且出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

你已在配置中使用凭据，但未提供正确的**ConfigurationData**将**PSDscAllowPlainTextPassword**设置为 true 以用于每个节点配置。

#### <a name="resolution"></a>解决方法

* 请确保传入正确的**ConfigurationData** ，以将配置中提到的每个节点配置的**PSDscAllowPlainTextPassword**设置为 true。 有关详细信息，请参阅[在 Azure 自动化状态配置中编译 DSC 配置](../automation-dsc-compile.md)。

### <a name="failure-processing-extension"></a>方案：从 dsc 扩展载入 "失败处理扩展" 错误

#### <a name="issue"></a>问题

使用 DSC 扩展进行载入时，会发生失败，其中包含错误：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

当向节点分配了服务中不存在的节点配置名称时，通常会发生此错误。

#### <a name="resolution"></a>解决方法

* 请确保为节点分配的节点配置名称与服务中的名称完全匹配。
* 你可以选择不包括节点配置名称，这将导致加入该节点但不分配节点配置

### <a name="cross-subscription"></a>方案：使用 PowerShell 注册节点会返回错误 "发生了一个或多个错误"

#### <a name="issue"></a>问题

使用 `Register-AzAutomationDSCNode` 或 `Register-AzureRMAutomationDSCNode`注册节点时，会收到以下错误。

```error
One or more errors occurred.
```

#### <a name="cause"></a>原因

当你尝试注册与自动化帐户位于不同订阅中的节点时，会发生此错误。

#### <a name="resolution"></a>解决方法

处理跨订阅节点，就好像它驻留在单独的云或本地。

按照以下步骤注册节点。

* Windows-[本地或 Azure/AWS 以外的云中](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)的 windows 计算机。
* Linux-[本地或 Azure 以外的云中的物理/虚拟 Linux 计算机](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)。

### <a name="agent-has-a-problem"></a>方案：错误消息-"预配失败"

#### <a name="issue"></a>问题

注册节点时，会看到以下错误：

```error
Provisioning has failed
```

#### <a name="cause"></a>原因

当节点与 Azure 之间存在连接问题时，将出现此消息。

#### <a name="resolution"></a>解决方法

确定节点是否处于专用虚拟网络中，或者在连接到 Azure 时是否存在其他问题。

有关详细信息，请参阅[载入解决方案时解决错误](onboarding.md)。

### <a name="failure-linux-temp-noexec"></a>方案：在 Linux 中应用配置，出现失败，出现常规错误

#### <a name="issue"></a>问题

在 Linux 中应用配置时，会发生失败，其中包含错误：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客户已确定，如果 `/tmp` 位置设置为 `noexec`，则 DSC 的当前版本将无法应用配置。

#### <a name="resolution"></a>解决方法

* 从 `/tmp` 位置中删除 `noexec` 选项。

### <a name="compilation-node-name-overlap"></a>情况：重叠的节点配置名称可能导致错误的发布

#### <a name="issue"></a>问题

如果使用单个配置脚本来生成多个节点配置，并且某些节点配置具有作为其他部分的子集的名称，则编译服务中的问题可能会导致分配错误的配置。  这仅在以下情况下发生：每个节点使用单个脚本生成具有配置数据的配置，并且仅当名称在字符串的开头发生重叠时才会发生。

例如，如果一个配置脚本用于基于使用 cmdlet 作为哈希表传递的节点数据生成配置，则节点数据包括一个名为 "server" 和 "1server" 的服务器。

#### <a name="cause"></a>原因

与编译服务有关的已知问题。

#### <a name="resolution"></a>解决方法

最好的解决方法是在本地编译或在 CI/CD 管道中进行编译，并将 MOF 文件直接上传到服务。  如果服务中的编译是必需的，则下一种最佳解决方法是拆分编译作业，使名称中不存在重叠。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
