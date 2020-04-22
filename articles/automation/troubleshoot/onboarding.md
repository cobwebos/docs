---
title: 对 Azure 自动化管理解决方案的载入进行故障排除
description: 了解如何排除解决方案载入错误。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679235"
---
# <a name="troubleshoot-solution-onboarding"></a>故障排除解决方案

在加入更新管理解决方案或更改跟踪和清单解决方案时，可能会收到错误。 本文介绍了可能发生的各种错误以及如何解决这些问题。

## <a name="known-issues"></a>已知问题

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>方案：重命名注册节点需要取消注册或重新注册

#### <a name="issue"></a>问题

节点注册到 Azure 自动化，然后更改操作系统计算机名称。 来自节点的报告继续以原始名称显示。

#### <a name="cause"></a>原因

重命名注册节点不会更新 Azure 自动化中的节点名称。

#### <a name="resolution"></a>解决方法

从 Azure 自动化状态配置中取消注册节点，然后再次注册。 在此之前发布到服务的报告将不再可用。

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>方案：不支持通过 https 代理重新签名证书

#### <a name="issue"></a>问题

当通过终止 HTTPS 流量然后使用新证书重新加密流量的代理解决方案进行连接时，服务不允许连接。

#### <a name="cause"></a>原因

Azure 自动化不支持重新签名用于加密流量的证书。

#### <a name="resolution"></a>解决方法

此问题当前没有解决方法。

## <a name="general-errors"></a>常规错误

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>方案：在消息中加入失败 - 无法启用解决方案

#### <a name="issue"></a>问题

当您尝试将 VM 板载到解决方案时，您会收到以下消息之一：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此错误是由 VM 或工作区或用户的权限不正确或缺少引起的。

#### <a name="resolution"></a>解决方法

确保您具有[在计算机上板所需的正确权限](../automation-role-based-access-control.md#onboarding-permissions)，然后尝试再次将解决方案加入。 如果收到错误`The solution cannot be enabled on this VM because the permission to read the workspace is missing`，请确保具有查找 VM 是否`Microsoft.OperationalInsights/workspaces/read`已连接到工作区的权限。

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>方案：使用消息载入失败：无法配置诊断日志记录的自动化帐户

#### <a name="issue"></a>问题

当您尝试将 VM 连接到解决方案时，您会收到以下消息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定价层与订阅的计费模型不匹配，则可能导致此错误。 请参阅[Azure 监视器 中的监视使用情况和估计成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解决方法

手动创建日志分析工作区，并重复载入过程以选择创建的工作区。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>场景：ComputerGroupQueryFormatError

#### <a name="issue"></a>问题

此错误代码表示用于定位解决方案的保存的搜索计算机组查询格式不正确。 

#### <a name="cause"></a>原因

您可能更改了查询，或者系统可能更改了查询。

#### <a name="resolution"></a>解决方法

您可以删除解决方案的查询，然后再次将解决方案重新加入，从而重新创建查询。 查询可以在工作区中找到，在 **"已保存"搜索**下。 查询的名称为**MicrosoftDefaultComputerGroup**，查询的类别是关联解决方案的名称。 如果启用了多个解决方案 **，Microsoft 默认计算机组**查询将多次显示在 **"已保存的搜索**"下。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>场景：PolicyViolation

#### <a name="issue"></a>问题

此错误代码指示部署由于违反一个或多个策略而失败。

#### <a name="cause"></a>原因 

策略阻止操作完成。

#### <a name="resolution"></a>解决方法

为了成功部署解决方案，必须考虑更改指示的策略。 由于可以定义许多不同类型的策略，因此所需的更改取决于所违反的策略。 例如，如果在资源组上定义了拒绝更改某些包含资源内容的权限的策略，则可以选择以下修补程序之一：

* 将该策略完全删除。
* 尝试将解决方案添加到其他资源组。
* 将策略重新定向到特定资源，例如自动化帐户。
* 修改策略配置为拒绝的资源集。

检查 Azure 门户右上角的通知，或导航到包含自动化帐户的资源组，并在 **"设置"** 下选择 **"部署**"以查看失败的部署。 要了解有关 Azure 策略的详细信息，请参阅[Azure 策略概述](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>方案：尝试取消链接工作区的错误

#### <a name="issue"></a>问题

尝试取消链接工作区时，您会收到以下错误：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

当您的日志分析工作区中仍有依赖于您的自动化帐户和日志分析工作区链接的解决方案处于活动状态时，将发生此错误。

### <a name="resolution"></a>解决方法

如果使用以下解决方案，请从工作区中删除这些解决方案：

* 更新管理
* 更改跟踪和库存
* 在非工作时间启动/停止 VM

删除解决方案后，可以取消链接工作区。 从工作区和自动化帐户中清除这些解决方案中的任何现有项目非常重要 

* 对于更新管理，请从自动化帐户中删除更新部署（计划）。
* 对于非工作时间启动/停止 VM，请删除"**设置** > **锁**"下自动化帐户中解决方案组件上的任何锁。 请参阅[在非工作时间解决方案期间删除开始/停止 VM。](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 扩展失败的日志分析

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

由于各种原因，安装 Windows 扩展的日志分析代理可能会失败。 以下部分介绍在部署 Windows 扩展的日志分析代理期间可能导致故障的载入问题。

>[!NOTE]
>Windows 的日志分析代理是 Microsoft 监视代理 （MMA） 的 Azure 自动化中当前使用的名称。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>场景：在 WebClient 请求期间发生异常

VM 上的 Windows 日志分析扩展无法与外部资源通信，并且部署失败。

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

* 在 VM 中配置的代理仅允许特定端口。
* 某个防火墙设置已阻止访问所需的端口和地址。

#### <a name="resolution"></a>解决方法

确保已打开正确的端口和地址用于通信。 有关端口和地址的列表，请参阅[规划网络](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>方案：由于暂时性环境问题，安装失败

由于其他安装或操作阻止安装，在部署期间安装 Windows 扩展的日志分析失败

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

* 另一个安装正在进行中。
* 系统在模板部署期间触发以重新启动。

#### <a name="resolution"></a>解决方法

此错误本质上是暂时性的。 请重试部署，以安装该扩展。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>场景：安装超时

由于超时，Windows 扩展的日志分析安装未完成。

#### <a name="issue"></a>问题

以下是可能返回的错误消息的示例：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

出现此类错误是因为 VM 在安装过程中负载很大。

### <a name="resolution"></a>解决方法

当 VM 负载较低时，尝试安装 Windows 扩展的日志分析代理。

## <a name="next-steps"></a>后续步骤

如果您在上面看不到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
