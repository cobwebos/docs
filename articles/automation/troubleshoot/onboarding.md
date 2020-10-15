---
title: 排查 Azure 自动化功能部署问题
description: 本文介绍如何排查和解决部署 Azure 自动化功能时出现的问题。
services: automation
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: a6b8384193e821e6c41a0d4d979cda51f6c65b3a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070480"
---
# <a name="troubleshoot-feature-deployment-issues"></a>排查功能部署问题

在虚拟机 (VM) 上部署“Azure 自动化更新管理”功能或“更改跟踪和清单”功能时，你可能会收到错误消息。 本文描述了可能会发生的错误及其解决方法。

## <a name="known-issues"></a>已知问题

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>场景：需要取消注册或重新注册才能重命名已注册的节点

#### <a name="issue"></a>问题

已向 Azure 自动化注册节点，但之后更改了操作系统计算机名。 来自该节点的报表继续显示原始名称。

#### <a name="cause"></a>原因

重命名已注册的节点不会更新 Azure 自动化中的节点名称。

#### <a name="resolution"></a>解决方法

从 Azure 自动化 State Configuration 中注销节点，然后重新注册它。 该时间之前发布到服务的报表将不再可用。

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>场景：不支持通过 HTTPS 代理重新注册证书

#### <a name="issue"></a>问题

如果通过代理连接，而且该代理会终止 HTTPS 流量，然后使用新的证书重新加密该流量，那么服务将禁止该连接。

#### <a name="cause"></a>原因

Azure 自动化不支持对用于加密流量的证书重新签名。

#### <a name="resolution"></a>解决方法

此问题目前没有解决方法。

## <a name="general-errors"></a>常规错误

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>场景：功能部署失败，显示“无法启用解决方案”消息

#### <a name="issue"></a>问题

你尝试在 VM 上启用某项功能时收到下列消息之一：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此错误是由于 VM 或工作区上的权限或者用户权限错误或缺失而导致的。

#### <a name="resolution"></a>解决方法

请确保你有适当的[功能部署权限](../automation-role-based-access-control.md#feature-setup-permissions)，然后尝试再次部署该功能。 如果收到错误消息“`The solution cannot be enabled on this VM because the permission to read the workspace is missing`”，请参阅以下 [故障排除信息](update-management.md#failed-to-enable-error)。

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>场景：功能部署失败，显示“未能配置自动化帐户进行诊断日志记录”

#### <a name="issue"></a>问题

你尝试在 VM 上启用某项功能时收到以下消息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定价层与订阅的计费模型不匹配，则可能导致此错误。 有关详细信息，请参阅[在 Azure Monitor 中监视使用情况和预估成本](../../azure-monitor/platform/usage-estimated-costs.md)。

#### <a name="resolution"></a>解决方法

手动创建 Log Analytics 工作区，然后重复功能部署过程，选择刚才创建的工作区。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>场景：ComputerGroupQueryFormatError

#### <a name="issue"></a>问题

此错误代码表示已保存的用于将此功能作为目标的搜索计算机组查询格式错误。 

#### <a name="cause"></a>原因

你或者系统更改了查询。

#### <a name="resolution"></a>解决方法

可删除对该功能的查询，然后再次启用该功能，这将重新创建查询。 可在工作区的“已保存的搜索”下找到该查询。 查询名称为 MicrosoftDefaultComputerGroup，查询的类别是所关联的功能的名称。 如果启用了多项功能，则 MicrosoftDefaultComputerGroup 查询会在“已保存的搜索”下显示多次 。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>场景：PolicyViolation

#### <a name="issue"></a>问题

此错误代码指出因违反一项或多项策略，部署失败。

#### <a name="cause"></a>原因 

某项策略在阻止操作完成。

#### <a name="resolution"></a>解决方法

要成功部署该功能，你必须考虑更改所指出的策略。 由于可定义很多不同类型的策略，因此所需的更改由所违反的策略而定。 例如，如果在资源组上定义了一项策略，该策略拒绝更改某些所含资源的内容，那么你可选择下列解决方案之一：

* 将该策略完全删除。
* 试着为其他资源组启用该功能。
* 将该策略的目标改为特定资源，例如自动化帐户。
* 修改该策略根据配置要拒绝的一组资源。

请查看 Azure 门户右上角的通知，或转到包含自动化帐户的资源组，然后选择“设置”下的“部署”，查看失败的部署 。 要详细了解 Azure Policy，请参阅 [Azure Policy 概述](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>场景：尝试取消链接工作区时出错

#### <a name="issue"></a>问题

你在尝试取消链接某个工作区时收到以下错误消息：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

如果你依赖自动化帐户的 Log Analytics 工作区和保持链接状态的 Log Analytics 工作区中仍有功能处于活动状态，那么会出现此错误。

### <a name="resolution"></a>解决方法

如果你正在使用以下功能，则请从工作区中删除这些功能的资源：

* 更新管理
* 更改跟踪和库存
* 在非工作时间启动/停止 VM

删除功能资源后，可取消链接工作区。 有必要从工作区和 Azure 自动化帐户中清除来自这些功能的所有现有项目：

* 对于“更新管理”功能，请从自动化帐户中删除“更新部署（计划）”。
* 对于“在非工作时间启动/停止 VM”功能，请在自动化帐户的“设置” > “锁定”下删除功能组件上的所有锁 。 有关详细信息，请参阅[删除功能](../automation-solution-vm-management.md#remove-the-feature)。

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 扩展的 Log Analytics 故障

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

安装适用于 Windows 扩展的 Log Analytics 代理时可能会因各种原因而失败。 以下部分描述了可能会在适用于 Windows 扩展的 Log Analytics 代理的部署期间导致出现故障的功能部署问题。

>[!NOTE]
>Microsoft Monitoring Agent (MMA) 的 Azure 自动化中目前使用“Windows 的 Log Analytics 代理”这个名称。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>场景：在 WebClient 请求期间发生异常

VM 上适用于 Windows 扩展的 Log Analytics 无法与外部资源通信，因此部署失败。

#### <a name="issue"></a>问题

下面是返回的错误消息示例：

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

导致此错误的部分潜在原因包括：

* VM 中配置的某个代理仅允许使用特定端口。
* 某个防火墙设置已阻止访问所需的端口和地址。

#### <a name="resolution"></a>解决方法

确保已打开正确的端口和地址用于通信。 有关端口和地址的列表，请参阅[规划网络](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>场景：因暂时性环境问题导致安装失败

在部署期间，由于正在进行其他安装或有操作阻止安装，导致适用于 Windows 扩展的 Log Analytics 安装失败。

#### <a name="issue"></a>问题

下面是可能返回的错误消息示例：

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

导致此错误的部分潜在原因包括：

* 另一项安装正在进行。
* 模板部署期间触发了系统重启。

#### <a name="resolution"></a>解决方法

此错误必定是暂时性的。 请重试部署，以安装该扩展。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>场景：安装超时

由于超时，适用于 Windows 扩展的 Log Analytics 未完成安装。

#### <a name="issue"></a>问题

下面是可能返回的错误消息示例：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

安装期间 VM 的负载较重，因此出现此类型的错误。

### <a name="resolution"></a>解决方法

请尝试在 VM 负载较低时安装适用于 Windows 扩展的 Log Analytics 代理。

## <a name="next-steps"></a>后续步骤

如果未在此处看到你遇到的问题，或者无法解决你遇到的问题，请尝试以下途径之一以获取其他支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。