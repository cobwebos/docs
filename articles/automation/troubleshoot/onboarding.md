---
title: 排查 Azure 自动化管理解决方案的载入问题
description: 了解如何排查 Azure Automation 解决方案载入错误。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836523"
---
# <a name="troubleshoot-solution-onboarding"></a>解决解决方案载入问题

在载入 Azure 自动化更新管理解决方案或更改跟踪和清单解决方案时，可能会收到错误消息。 本文介绍了可能发生的各种错误以及如何解决这些错误。

## <a name="known-issues"></a>已知问题

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>方案：重命名已注册的节点需要重新注册或重新注册

#### <a name="issue"></a>问题

节点已注册到 Azure 自动化，然后操作系统计算机名称已更改。 节点中的报表将继续以原始名称显示。

#### <a name="cause"></a>原因

重命名已注册的节点不会更新 Azure 自动化中的节点名称。

#### <a name="resolution"></a>解决方法

从 Azure 自动化状态配置中注销节点，然后重新注册。 在该时间之前发布到服务的报表将不再可用。

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>方案：不支持通过 HTTPS 代理重新签名证书

#### <a name="issue"></a>问题

当你通过代理解决方案进行连接，该解决方案将终止 HTTPS 流量，然后使用新证书重新加密流量时，该服务不允许连接。

#### <a name="cause"></a>原因

Azure 自动化不支持对用于加密流量的证书进行重新签名。

#### <a name="resolution"></a>解决方法

目前没有针对此问题的解决方法。

## <a name="general-errors"></a>常规错误

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>方案：载入失败，并出现消息 "无法启用解决方案"

#### <a name="issue"></a>问题

尝试将 VM 载入解决方案时，会收到以下消息之一：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此错误是由于 VM、工作区或用户的权限不正确或缺失引起的。

#### <a name="resolution"></a>解决方法

请确保你具有[板载计算机所需](../automation-role-based-access-control.md#onboarding-permissions)的正确权限，然后再次尝试载入解决方案。 如果收到错误消息`The solution cannot be enabled on this VM because the permission to read the workspace is missing`，请确保你有`Microsoft.OperationalInsights/workspaces/read`权查看 VM 是否已载入到工作区。

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>方案：载入失败，并出现消息 "无法配置用于诊断日志记录的自动化帐户"

#### <a name="issue"></a>问题

尝试将 VM 载入解决方案时，会收到以下消息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定价层与订阅的计费模型不匹配，则可能会导致此错误。 有关详细信息，请参阅[Azure Monitor 中的监视使用情况和预估成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解决方法

手动创建 Log Analytics 工作区，并重复载入过程以选择创建的工作区。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>场景：ComputerGroupQueryFormatError

#### <a name="issue"></a>问题

此错误代码表示用于针对解决方案的已保存搜索计算机组查询的格式不正确。 

#### <a name="cause"></a>原因

您可能已更改查询，或者系统可能已对其进行了更改。

#### <a name="resolution"></a>解决方法

您可以删除解决方案的查询，然后再次载入解决方案，这将重新创建查询。 可以在工作区中的 "保存的**搜索**" 下找到该查询。 查询的名称为**MicrosoftDefaultComputerGroup**，查询的类别为关联解决方案的名称。 如果启用多个解决方案， **MicrosoftDefaultComputerGroup**查询会在 "保存的**搜索**" 下多次显示。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>场景：PolicyViolation

#### <a name="issue"></a>问题

此错误代码表示部署由于违反一个或多个策略而失败。

#### <a name="cause"></a>原因 

策略正在阻止操作完成。

#### <a name="resolution"></a>解决方法

若要成功部署解决方案，您必须考虑更改指定的策略。 由于可以定义多种不同类型的策略，因此所需的更改取决于所违反的策略。 例如，如果对某个资源组定义了一个策略，而该资源组拒绝更改某些包含资源的内容，则可以选择以下其中一个修复程序：

* 将该策略完全删除。
* 尝试将解决方案载入其他资源组。
* 将策略重定位到特定的资源，例如自动化帐户。
* 修改策略配置为拒绝的资源集。

检查 Azure 门户右上角的通知，或者前往包含自动化帐户的资源组，然后选择 "**设置**" 下的 "**部署**" 以查看失败的部署。 若要了解有关 Azure 策略的详细信息，请参阅[Azure 策略概述](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>方案：尝试取消链接工作区时出错

#### <a name="issue"></a>问题

尝试取消链接工作区时，会收到以下错误消息：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

当你在 Log Analytics 工作区中仍有处于活动状态的解决方案，而该工作区依赖于你的自动化帐户和所链接 Log Analytics

### <a name="resolution"></a>解决方法

如果要使用以下解决方案，请将其从工作区中删除：

* 更新管理
* 更改跟踪和库存
* 在非工作时间启动/停止 VM

删除解决方案后，可以取消链接工作区。 从工作区和自动化帐户中清理这些解决方案中的所有现有项目非常重要。

* 对于更新管理，请从自动化帐户中删除**更新部署（计划）** 。
* 对于在空闲时间启动/停止 VM，请在 "**设置** > " "**锁定**" 下删除自动化帐户中解决方案组件的所有锁定。 有关详细信息，请参阅[删除在空闲时间启动/停止 VM 解决方案](../automation-solution-vm-management.md#remove-the-solution)。

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 扩展故障 Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

安装适用于 Windows 扩展的 Log Analytics 代理可能会因多种原因而失败。 以下部分介绍在部署 Log Analytics agent for Windows extension 期间可能导致故障的载入问题。

>[!NOTE]
>适用于 Windows 的 Log Analytics 代理是当前在 Azure 自动化中为 Microsoft Monitoring Agent （MMA）使用的名称。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>场景：在 WebClient 请求期间发生异常

VM 上的 Windows 扩展 Log Analytics 无法与外部资源通信，部署将失败。

#### <a name="issue"></a>问题

下面是返回的错误消息示例：

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

此错误的一些可能原因是：

* VM 中配置的代理仅允许特定端口。
* 某个防火墙设置已阻止访问所需的端口和地址。

#### <a name="resolution"></a>解决方法

确保已打开正确的端口和地址用于通信。 有关端口和地址的列表，请参阅[规划网络](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>方案：由于暂时性环境问题而导致安装失败

在部署过程中安装 Windows extension Log Analytics 失败，因为另一个安装或操作阻止了安装。

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

此错误的一些可能原因是：

* 正在进行另一个安装。
* 在模板部署期间，系统会触发重新启动。

#### <a name="resolution"></a>解决方法

此错误本质上是暂时性的。 请重试部署，以安装该扩展。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>场景：安装超时

由于超时，Windows extension Log Analytics 的安装未完成。

#### <a name="issue"></a>问题

下面是可能返回的错误消息示例：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

之所以发生这种类型的错误，是因为 VM 在安装期间负载较重。

### <a name="resolution"></a>解决方法

当 VM 负载较低时，尝试安装适用于 Windows extension 的 Log Analytics 代理。

## <a name="next-steps"></a>后续步骤

如果在此处看不到你的问题，或者无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，以改善客户体验。 Azure 支持将 Azure 社区连接到答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。
