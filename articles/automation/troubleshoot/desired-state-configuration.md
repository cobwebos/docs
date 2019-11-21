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
ms.openlocfilehash: ab9a39cfba082ea4c4d1cc6c29764619011d8cb8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231553"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) 疑难解答

本文提供有关 Desired State Configuration (DSC) 问题疑难解答的信息。

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Steps to troubleshoot Desired State Configuration (DSC)

When you have errors compiling or deploying configurations in Azure State Configuration, here are a few steps to help you diagnose the issue.

1. **Ensure your configuration compiles successfully on your local machine:**  Azure State Configuration is built on PowerShell DSC. You can find the documentation for the DSC language and syntax in the [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

   By compiling your DSC configuration on your local machine you can discover and resolve common errors, such as:

   - **Missing Modules**
   - **Syntax Errors**
   - **Logic Errors**

2. **View DSC logs on your Node:** If your configuration compiles successfully, but fails when applied to a Node, you can find detailed information in the logs. For information about where to find DSC logs, see [Where are the DSC Event Logs](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Furthermore, the [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) can assist you in parsing detailed information from the DSC logs. If you contact support, they will require these logs to diagnose your issue.

   You can install **xDscDiagnostics** on your local machine using the instructions found under [Install the stable version module](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   To install **xDscDiagnostics** on your Azure machine, you can use [az vm run-command](/cli/azure/vm/run-command) or [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). You can also use the **Run command** option from the portal, by following the steps found in [Run PowerShell scripts in your Windows VM with Run Command](../../virtual-machines/windows/run-command.md).

   For information on using **xDscDiagnostics**, see [Using xDscDiagnostics to analyze DSC logs](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), as well as [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Ensure your Nodes and Automation workspace have the required modules:** Desired State Configuration depends on modules installed on the Node.  When using Azure Automation State Configuration, import any required modules into your automation account using the steps listed in [Import Modules](../shared-resources/modules.md#import-modules). Configurations can also have a dependency on specific versions of modules.  For more information, see, [Troubleshoot Modules](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用所需状态配置 (DSC) 时的常见错误

### <a name="unsupported-characters"></a>Scenario: A configuration with special characters cannot be deleted from the portal

#### <a name="issue"></a>问题

When attempting to delete a DSC configuration from the portal, you see the following error:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

This error is a temporary issue that is planned to be resolved.

#### <a name="resolution"></a>分辨率

* Use the Az Cmdlet "Remove-AzAutomationDscConfiguration" to delete the configuration.
* The documentation for this cmdlet hasn't been updated yet.  Until then, refer to the documentation for the AzureRM module.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: Failed to register Dsc Agent

#### <a name="issue"></a>问题

When attempting to run `Set-DscLocalConfigurationManager` or another DSC cmdlet you receive the error:

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

This error is normally caused by a firewall, the machine being behind a proxy server, or other network errors.

#### <a name="resolution"></a>分辨率

Verify your machine has access to the proper endpoints for Azure Automation DSC and try again. For a list of ports and addresses needed, see [network planning](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

#### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。

#### <a name="resolution"></a>分辨率

* Make sure that you're assigning the node with "node configuration name" and not the "configuration name".
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * To assign a node configuration to a node using Azure portal, open the **DSC Nodes** page, then select a node and click on **Assign node configuration** button.
  * To assign a node configuration to a node using PowerShell cmdlet, use **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>场景：编译配置时未生成节点配置（MOF 文件）

#### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

如果 DSC 配置中“Node”关键字后面的表达式的计算结果为 `$null`，则不会生成节点配置。

#### <a name="resolution"></a>分辨率

下述解决方案中的任何一种都可以解决此问题：

* Make sure that the expression next to the **Node** keyword in the configuration definition isn't evaluating to $null.
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](../automation-dsc-compile.md) 传递配置需要的预期值。

### <a name="dsc-in-progress"></a>场景：DSC 节点报告卡在了“正在进行”状态

#### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

已升级 WMF 版本，已损坏 WMI。

#### <a name="resolution"></a>分辨率

To fix the issue, follow the instructions in the [DSC known issues and limitations](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) article.

### <a name="issue-using-credential"></a>场景：无法在 DSC 配置中使用凭据

#### <a name="issue"></a>问题

DSC 编译作业已暂停，且出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

You've used a credential in a configuration but didn’t provide proper **ConfigurationData** to set **PSDscAllowPlainTextPassword** to true for each node configuration.

#### <a name="resolution"></a>分辨率

* Make sure to pass in the proper **ConfigurationData** to set **PSDscAllowPlainTextPassword** to true for each node configuration that is mentioned in the configuration. 有关详细信息，请参阅 [Azure 自动化 DSC 中的资产](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation)。

### <a name="failure-processing-extension"></a>Scenario: Onboarding from dsc extension, "Failure processing extension" error

#### <a name="issue"></a>问题

When onboarding using DSC extension, a failure occurs containing the error:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

This error typically occurs when the node is assigned a node configuration name that does not exist in the service.

#### <a name="resolution"></a>分辨率

* Make sure that you're assigning the node with a node configuration name that exactly matches the name in the service.
* You can choose to not include the node configuration name, which will result in onboarding the node but not assigning a node configuration

### <a name="failure-linux-temp-noexec"></a>Scenario: Applying a configuration in Linux, a failure occurs with a general error

#### <a name="issue"></a>问题

When applying a configuration in Linux, a failure occurs containing the error:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

Customers have identified that if the `/tmp` location is set to `noexec`, the current version of DSC will fail to apply configurations.

#### <a name="resolution"></a>分辨率

* Remove the `noexec` option from the `/tmp` location.

### <a name="compilation-node-name-overlap"></a>Scenario: Node configuration names that overlap could result in bad release

#### <a name="issue"></a>问题

If a single configuration script is used to generate multiple node configurations, and some of the node configurations have a name that is a subset of others, an issue in the compilation service could result in assigning the wrong configuration.  This only occurs when using a single script to generate configurations with configuration data per node, and only when the name overlap occurs at the beginning of the string.

Example, if a single configuration script is used to generate configurations based on node data passed as a hashtable using cmdlets, and the node data includes a server named "server" and "1server".

#### <a name="cause"></a>原因

Known issue with the compilation service.

#### <a name="resolution"></a>分辨率

The best workaround would be to compile locally or in a CI/CD pipeline and upload the MOF files directly to the service.  If compilation in the service is a requirement, the next best workaround would be to split the compilation jobs so there is no overlap in names.

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
