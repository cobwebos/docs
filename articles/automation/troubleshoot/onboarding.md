---
title: 故障排除载入 Azure 自动化管理解决方案
description: 了解如何排查更新管理、更改跟踪和库存解决方案的加入错误
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536958"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>在载入更新管理、更改跟踪和库存时排除错误

在加入更新管理、更改跟踪或库存等解决方案时，可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="known-issues"></a>已知问题

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>方案：重命名注册节点需要重新取消注册/注册

#### <a name="issue"></a>问题

节点注册到 Azure 自动化，然后更改操作系统计算机名称。  来自节点的报告继续以原始名称显示。

#### <a name="cause"></a>原因

重命名注册节点不会更新 Azure 自动化中的节点名称。

#### <a name="resolution"></a>解决方法

从 Azure 自动化状态配置中取消注册节点，然后再次注册。  在此之前发布到服务的报告将不再可用。


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>方案：不支持通过 https 代理重新签名证书

#### <a name="issue"></a>问题

客户报告说，当通过终止 https 流量的代理解决方案进行连接，然后使用新证书重新加密流量时，该服务不允许连接。

#### <a name="cause"></a>原因

Azure 自动化不支持重新签名用于加密流量的证书。

#### <a name="resolution"></a>解决方法

没有针对此问题的解决方法。

## <a name="general-errors"></a>常规错误

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>方案：在消息中加入失败 - 无法启用解决方案

#### <a name="issue"></a>问题

当您尝试将虚拟机登机到解决方案时，您会收到以下消息之一：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此错误是由虚拟机、工作区或用户的权限不正确或缺少引起的。

#### <a name="resolution"></a>解决方法

请确保你有加入虚拟机所需的权限。 查看[加入计算机所需的权限](../automation-role-based-access-control.md#onboarding-permissions)并尝试重新加入解决方案。 如果收到错误`The solution cannot be enabled on this VM because the permission to read the workspace is missing`，请确保具有查找 VM 是否`Microsoft.OperationalInsights/workspaces/read`已连接到工作区的权限。

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>方案：在消息中登录失败 - 无法配置诊断日志记录的自动化帐户

#### <a name="issue"></a>问题

尝试将虚拟机加入解决方案时，你会收到以下消息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定价层与订阅的计费模型不匹配，则可能导致此错误。 有关详细信息，请参阅[Azure 监视器 中的监视使用情况和估计成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解决方法

手动创建日志分析工作区，并重复载入过程以选择创建的工作区。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>场景：ComputerGroupQueryFormatError

#### <a name="issue"></a>问题

此错误代码表示，用来将此解决方案作为目标的已保存搜索计算机组查询没有正确设置格式。 

#### <a name="cause"></a>原因

你可能已更改了该查询，或者系统可能已更改了该查询。

#### <a name="resolution"></a>解决方法

可以删除对此解决方案的查询，重新载入解决方案，这会重新创建查询。 可以在你的工作区内找到此查询，它位于“保存的搜索”下。**** 查询名称是 **MicrosoftDefaultComputerGroup**，查询类别是与此查询关联的解决方案的名称。 如果启用了多个解决方案，则 **MicrosoftDefaultComputerGroup** 会在“保存的搜索”下显示多次。****

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>场景：PolicyViolation

#### <a name="issue"></a>问题

此错误代码表示部署由于违反一个或多个策略而失败。

#### <a name="cause"></a>原因 

实施的某个策略正在阻止操作完成。

#### <a name="resolution"></a>解决方法

为了成功部署解决方案，需要考虑更改指示的策略。 由于可以定义许多不同类型的策略，因此所需的特定更改取决于所违反的策略。 例如，如果在某个资源组上定义了一个拒绝更改该资源组中某些类型的资源内容的权限的策略，则可以（例如）执行以下任一操作：

* 将该策略完全删除。
* 尝试加入到不同的资源组。
* 通过以下方式修改策略，例如：
  * 将策略重定位到特定资源（例如，特定自动化帐户）。
  * 修改该策略已配置为拒绝的资源集。

检查 Azure 门户右上角的通知，或导航到包含自动化帐户的资源组，并在 **"设置"** 下选择 **"部署**"以查看失败的部署。 若要了解有关 Azure Policy 的详细信息，请访问：[Azure Policy 概述](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>方案：尝试取消链接工作区的错误

#### <a name="issue"></a>问题

尝试取消链接工作区时，您会收到以下错误：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

当您的日志分析工作区中仍有依赖于您的自动化帐户和日志分析工作区链接的解决方案处于活动状态时，将发生此错误。

### <a name="resolution"></a>解决方法

要解决此问题，您需要从工作区中删除以下解决方案（如果您正在使用）：

* 更新管理
* 更改跟踪
* 在非工作时间启动/停止 VM

删除解决方案后，可以取消链接工作区。 从工作区和自动化帐户中清理这些解决方案中的任何现有项目也很重要。  

* 更新管理
  * 从自动化帐户中删除更新部署（计划）
* 在非工作时间启动/停止 VM
  * 在**设置** > **锁**下删除自动化帐户中解决方案组件上的任何锁。
  * 有关在非工作时间解决方案期间删除开始/停止 VM 的其他步骤，请参阅[在非工作时间解决方案期间删除"开始/停止"VM。](../automation-solution-vm-management.md#remove-the-solution)

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>MMA 扩展失败

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

部署解决方案时，会部署各种相关资源。 其中一个资源是 Microsoft Monitoring Agent 扩展或 Log Analytics Linux 代理。 这些是虚拟机的来宾代理安装的虚拟机扩展，负责与配置的日志分析工作区通信，以便以后协调下载要载入的解决方案在开始执行后所依赖的二进制文件和其他文件的下载。
通常，通知中心显示的通知首先会指出发生了 MMA 或 Log Analytics Linux 代理安装失败。 单击该通知可以获取有关特定失败问题的更多信息。 依次导航到“资源组”资源和“部署”元素，其中也提供了发生的部署失败问题的详细信息。
MMA 或 Log Analytics Linux 代理安装可能会出于各种原因而失败，这些失败问题的解决步骤根据问题的不同而异。 具体的故障排除步骤如下。

以下部分介绍在载入时可能遇到的导致部署 MMA 扩展失败的各种问题。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>场景：在 WebClient 请求期间发生异常

虚拟机上的 MMA 扩展无法与外部资源通信，并且部署失败。

#### <a name="issue"></a>问题

下面是返回的错误消息示例：

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

此错误的部分潜在原因包括：

* VM 中配置了一个仅允许特定端口的代理。

* 某个防火墙设置已阻止访问所需的端口和地址。

#### <a name="resolution"></a>解决方法

确保已打开正确的端口和地址用于通信。 有关端口和地址的列表，请参阅[规划网络](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>方案：由于暂时性环境问题，安装失败

由于其他安装或操作阻止安装，部署期间安装 Microsoft 监视代理扩展失败

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

此错误的部分潜在原因包括：

* 另一项安装正在进行
* 系统在模板部署期间触发以重新启动

#### <a name="resolution"></a>解决方法

此错误是暂时性的。 请重试部署，以安装该扩展。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>场景：安装超时

由于超时，MMA 扩展的安装未完成。

#### <a name="issue"></a>问题

以下示例是可能返回的错误消息：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

出现此错误的原因是虚拟机在安装过程中负载很大。

### <a name="resolution"></a>解决方法

请尝试在 VM 的负载降低时安装 MMA 扩展。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
