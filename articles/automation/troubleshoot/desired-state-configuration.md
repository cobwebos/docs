---
title: Azure 自动化 Desired State Configuration (DSC) 问题疑难解答
description: 本文提供有关 Desired State Configuration (DSC) 疑难解答的信息
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 63bb5c6338cf230c2bb47cb0a2c03810053f970a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002572"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) 疑难解答

本文提供有关 Desired State Configuration (DSC) 问题疑难解答的信息。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用所需状态配置 (DSC) 时的常见错误

### <a name="unsupported-characters"></a>场景：无法从门户删除带有特殊字符的配置

#### <a name="issue"></a>问题

尝试通过门户删除 DSC 配置时，将看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此错误是计划要解析的临时问题。

#### <a name="resolution"></a>解决方法

* 使用 Az Cmdlet "Remove-AzAutomationDscConfiguration" 删除配置。
* 此 cmdlet 的文档尚未更新。  在此之前，请参阅 AzureRM 模块的文档。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>场景：无法注册 Dsc 代理

#### <a name="issue"></a>问题

当尝试运行`Set-DscLocalConfigurationManager`或另一个 DSC cmdlet，您会收到错误：

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

通过防火墙，被代理服务器或其他网络错误后面的计算机通常导致此错误。

#### <a name="resolution"></a>解决方法

验证你的计算机具有访问正确的终结点为 Azure 自动化 DSC，然后重试。 有关端口和所需地址的列表，请参阅[网络规划](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

#### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。

#### <a name="resolution"></a>解决方法

* 请确保将具有"节点配置名称"而不是"配置名称"的节点。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 若要将节点配置分配到使用 Azure 门户的节点，打开**DSC 节点**页上，然后选择一个节点，然后单击**分配节点配置**按钮。  
  * 若要将节点配置分配到使用 PowerShell cmdlet 的节点，请使用**Set-azurermautomationdscnode** cmdlet

### <a name="no-mof-files"></a>场景：编译配置时未生成节点配置（MOF 文件）

#### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

如果 DSC 配置中“Node”关键字后面的表达式的计算结果为 `$null`，则不会生成节点配置。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 请确保旁边的表达式**节点**配置定义中的关键字不评估结果为 $null。
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](../automation-dsc-compile.md#configurationdata) 传递配置需要的预期值。

### <a name="dsc-in-progress"></a>场景：DSC 节点报告卡在了“正在进行”状态

#### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

已升级 WMF 版本，已损坏 WMI。

#### <a name="resolution"></a>解决方法

若要解决此问题，请按照中的说明[DSC 已知问题和限制](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)一文。

### <a name="issue-using-credential"></a>场景：无法在 DSC 配置中使用凭据

#### <a name="issue"></a>问题

DSC 编译作业已暂停，且出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

在配置中使用过凭据但未提供正确**ConfigurationData**若要设置**PSDscAllowPlainTextPassword**为可用于每个节点配置。

#### <a name="resolution"></a>解决方法

* 请确保传入正确**ConfigurationData**若要设置**PSDscAllowPlainTextPassword**为 true 的每个节点配置的配置中所述。 有关详细信息，请参阅 [Azure 自动化 DSC 中的资产](../automation-dsc-compile.md#assets)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
